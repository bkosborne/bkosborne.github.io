+++
author = "Brian Osborne"
title = "Importing Images Within an RSS Feed Using the Drupal 7 Feeds Module"
date = 2018-02-14
tags = [
  "Drupal Planet",
]
+++

Yes, a blog post about Drupal 7!

I recently worked on an enhancement for a large multi-site Drupal 7 platform to allow its users to import news articles from RSS feeds. Pretty simple request, and given the maturity of the Drupal 7 contrib module ecosystem, it wasn't too difficult to implement.

One somewhat interesting requirement was that images from the RSS feed be imported to an image field on the news article content type. RSS doesn't have direct support for an image element, but it has indirect support via the enclosure element. According to [the RSS spec](http://cyber.harvard.edu/rss/rss.html):

> It has three required attributes. url says where the enclosure is located, length says how big it is in bytes, and type says what its type is, a standard MIME type.

RSS feeds will often use the enclosure element to provide an image for each item in the feed.

Despite being in a beta release still, the [Drupal 7 Feeds module](https://drupal.org/project/feeds) is considered quite stable and mature, with it's most recent release in September 2017. It has a robust interface that suited my use case quite well, allowing me to map RSS elements to fields on the news article content type. However, it doesn't support pulling data out of enclosure elements in the source. But alas, in there's an [8 year old issue containing a very small patch](https://www.drupal.org/project/feeds/issues/952878) that adds the ability.

With that patch installed, the final step is to find the proper "target" to map it's data to. It's not immediately clear how this should work. Feeds needs to be smart enough to accept the URL to the image, download it, create a file entity from it, and assign the appropriate data to the image field on the node. Feeds exposes 4 different targets for an image field:

* Featured Image Display
* Featured Image: Alt
* Featured Image: Title
* Featured Image: URI

<img src="/sites/default/files/node-images/feeds-image-field-targets.png" width="400" height="113" alt="Feeds image field targets"  />

Selecting the "URI" target is the proper choice. Feeds will recognize that you're trying to import a remote image and download it.