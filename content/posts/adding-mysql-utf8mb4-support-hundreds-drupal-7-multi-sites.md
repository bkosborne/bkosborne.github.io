+++
author = "Brian Osborne"
title = "Adding MySQL UTF8MB4 support to hundreds of Drupal 7 multi-sites"
date = 2018-07-21
tags = [
  "Drupal Planet",
]
+++

Unicode characters encoded using UTF8 can technically use 1 to 4 bytes to represent a single character. However, older versions of MySQL only provided support for storing UTF8 encoded characters that used 1 to 3 bytes. This was enough to cover the most commonly used characters, but is not suitable for applications that accept user input where any character can be submitted (like emojis, which use 4 bytes). Newer versions of MySQL provide a character encoding called [utf8mb4](https://dev.mysql.com/doc/refman/5.5/en/charset-unicode-utf8mb4.html) to fix this issue. [Drupal 7 supports this](https://www.drupal.org/node/2754539), but requires some special configuration. Drupal 8 is configured this way by default.

Existing Drupal 7 sites that were setup with MySQL's old 3-byte-max UTF8 encoding must undergo a conversion process to change the character set on tables and text columns from `utf8` to `utf8mb4`. The collation value (what MySQL uses to determine how text fields are sorted) also needs to be changed to the newer utf8mb4 variant. Thankfully, there's already a [drush command](https://www.drupal.org/project/utf8mb4_convert) you can download that does this conversion for you on a single database. Before running it, you should ensure that your MySQL server is properly setup to use the utf8mb4 character encoding. [There's a helpful guide on this available on Drupal.org](https://www.drupal.org/node/2754539). Afterward the conversion is run, you still must configure Drupal to communicate with MySQL using this new encoding as described in the guide I linked to.

Part of my job is to help maintain hundreds of sites running as multi-site in a single codebase. So, same codebase, but hundreds of databases, each of which needed to have its database tables converted over to the new encoding. Converting a single database is not such a big deal, because it only takes a few minutes to run, but since I was dealing with hundreds, I wanted to make sure I had a good process laid out with plenty of logging. I created the below bash script which placed each site in maintenance mode (if it wasn't already), ran the drush command to convert the database, then took the site out of maintenance mode.

All in all, it took about 10 hours to do this for ~250 websites. While the script was running, I was monitoring for errors or other issues, ready to kill the script off if needed. I added a 3 second sleep at the end of each conversion to allow me time to cleanly kill the script.

After the script was completed, I pushed up new code for the common settings.php file (each site is configured to load a common settings file that they all share) which configured Drupal to connect to MySQL using the proper character set. In between the time that a database was converted, and the settings.php was updated for that site, there still should not have been any issues, because MySQL's UTF8MB4 character encoding should be backwards compatible with the original encoding that only supports 3 byte characters.

Here's the script for any that may be interested:

```
#!/usr/bin/env bash

#
# Usage:
# Alter this script to specify the proper Drupal docroot.
# 
# Run this command and pass to it a filename which contains a list of
# multisite directory names, one per line.
#
# For each site listed in the file, this script will first put the site in
# maintenance mode (if it's not already in that state), then run the
# uf8mb4 conversion script. Afterwards it will disable maintenance mode if
# it was previously disabled.
#

### Set to Drupal docroot
docroot="/var/www/html/"

script_begin=$(date +"%s")

count=0
total="$(wc -l $1 | awk '{ print $1 }')"
while read -r site || [[ -n "$site" ]]; do
    start_time=$(date +"%s")
    count=$((count+1))
    echo "--- Processing site #${count}/${total}: $site ---"
    mm="$(drush --root=${docroot} -l ${site} vget --exact maintenance_mode)"
    if [ $? -ne 0 ]; then
        echo "Drush command to check maintenance mode failed, skipping site"
        continue
    fi

    # If maintenance mode is not enabled, enable it.
    if [ -z $mm ] || [ $mm = '0' ]; then
        echo "Enabling maintenance mode."
        drush --root=${docroot} -l ${site} vset maintenance_mode 1
    else
        echo "Maintenance mode already enabled."
    fi

    drush --root=${docroot} -l ${site} utf8mb4-convert-databases -y $site

    # Now disable maintenance mode, as long as it was already disabled before.
    if [ -z $mm ] || [ $mm = '0' ]; then
        echo "Disabling maintenance mode."
        drush --root=${docroot} -l ${site} vset maintenance_mode 0
    else
        echo "Maintenance mode will remain on, it was already on before update."
    fi

    echo "Clearing cache"
    drush --root=${docroot} -l ${site} cc all

    end_time=$(date +"%s")
    echo "Completed in $(($end_time - $start_time)) seconds"
    echo "Done, sleeping 3 seconds before next site"
    sleep 3
done < "$1"

script_end=$(date +"%s")

echo "Ended: $script_end ; Total of $(($script_end - $script_begin)) seconds."
```