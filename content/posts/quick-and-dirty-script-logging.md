+++
author = "Brian Osborne"
title = "Quick and dirty script logging"
date = 2013-09-24
+++

I write quite a bit of Ruby scripts that run at scheduled intervals via cron, and need a way to monitor their output easily. Ruby has a few popular logging solutions, including one that is part of the standard library, that can be fairly easy to integrate into your script.

For a quick script, I find it unnecessary to add the overhead of a logger class and get it all set up. I also like to write output directly to standard out so that I can run the script manually and easily see its output without having to check the log file (though I am aware I can setup Ruby loggers to log to both a file and stdout).

When writing a Ruby script, I'll write out any useful data using `puts`. I may prepend the puts argument with a `Time.now` which is helpful for long running scripts. Then, since Ruby will buffer its output to STDOUT, I include `STDOUT.sync = true` and STDERR.sync = true at the top of my code. This will immediately flush the buffers, allowing me to monitor the script progress by tailing the log without waiting for the script to finish execution.

So how do we capture this output to a log file?

```
/path/to/script.rb >> /var/log/script.log 2>&1
```

Put this in a crontab and you're set. We're concatenating the output of script.rb to script.log, and redirecting standard out into standard error. This ensures that any errors that may occur in the script will also be sent to the log, which is very important.

After that, all you have to do is set up a simple log rotate config to cycle your logs and you're good to go. Now you can be confident that your script will logs its output, including errors, while still allowing you to see the same output while running the script manually.

Another benefit is that I can write Ruby or bash scripts and capture their output all the time. A lot of my Ruby scripts will just shell out commands, and it's nice to not have to worry about capturing the stdout and stderr of the shelled out command and then passing it to a logger.