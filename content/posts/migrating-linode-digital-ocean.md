+++
author = "Brian Osborne"
title = "Migrating from Linode to Digital Ocean"
date = 2013-03-02
+++

A co-worker of mine recently came back from a Ruby meetup in NYC, where he heard of a new VPS hosting company called Digital Ocean. There's certainly no shortage of VPS hosts around now, but Digital Ocean appeared very appealing because it's lowest tiered plan cost just $5 a month -- too good to be true, right? The features you get with that low plan are nothing to scuff at either, giving you 512MB of memory, 20GB of SSD, and 1TB of bandwidth.

### Comparing Digital Ocean to Linode<
At the time (two weeks ago) I was hosted with Linode. Linode is a great company and I never had even one issue with their service in the two years I've been with them. They have a great control panel, DNS manager, and their help and support articles are unmatched. However, the appeal of Digital Ocean could not be ignored, specifically the 4x reduction in cost. I found a great comparison article [benchmarking the two services](http://jasonormand.com/2013/02/08/linode-vs-digitalocean-performance-benchmarks/). The performance gain of the SSD with Digital Ocean compared to a traditional disk drive with Linode is very noticeable. Linode does have Digital Ocean beat in terms of CPU performance though, but for my needs as a web host and general playground, I'd much rather have the faster IO time.

### DNS Migration
I decided to make the shift over to Digital Ocean as soon as I could. I didn't have a whole lot hosted on my linode that I needed to be concerned with (just this website, and a few side projects and freelance sites). Once thing that was a bit of a bother was that I used the DNS manager with Linode for all of my domains. Digital Ocean also has a DNS manager, but it's a new beta feature and I didn't feel confident in it enough to give it a shot just yet. So, before anything, I switched my name servers back to Namecheap to make the migration easier. I could never really decide if it's better to keep my name servers with the registrar or host them elsewhere. I've switched both registrars and hosting providers often enough in the past that neither seem to weigh more in my mind.

### Setting up my Droplet
Linode has incredibly good help articles on setting up various production environments for your needs. In fact, that was part of my initial appeal towards Linode. At the time I was fairly new to setting up and managing a linux OS from scratch, so they proved to be very helpful. Digital Ocean also has a good support library, but it's not as fleshed out as Linode. I've set up a good amount of VM's in the past year with similar software configurations, so I hit the ground running with this one.

I created a new "droplet" with the lowest plan, and kicked it to Ubuntu 12.04 x64. Digital Ocean emails you the root password for the machine after it's kicked (doesn't take long at all), and then you're on your way. I started by installing apache, MySQL, and PHP one at a time using apt-get. The PHP installation on my Linode was a bit more complicated because I was hosting a few sites - but this time around it was just going to be mine.

I made sure each were running properly after the installation (MySQL in particular requires some additional love, like running mysql_install_db and mysql_secure_installation). It wasn't long before I migrated over my databases and had my website running on my new droplet. I was so excited about the new VPS that I prematurely updated my DNS records to point to the new server...

### MySQL Problems!
The following day, I would check my site periodically and found that MySQL kept crashing. I checked the MySQL logs - nothing. So I checked the upstart logs for MySQL and found this cryptic error message:

    /usr/bin/mysqladmin: connect to server at 'localhost' failed
    error: 'Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (111)'
    Check that mysqld is running and that the socket: '/var/run/mysqld/mysqld.sock' exists!
    130221 14:23:38 \[Note\] Plugin 'FEDERATED' is disabled.
    130221 14:23:38 InnoDB: The InnoDB memory heap is disabled
    130221 14:23:38 InnoDB: Mutexes and rw\_locks use GCC atomic builtins
    130221 14:23:38 InnoDB: Compressed tables use zlib 1.2.3.4
    130221 14:23:38 InnoDB: Initializing buffer pool, size = 128.0M
    InnoDB: mmap(137363456 bytes) failed; errno 12
    130221 14:23:38 InnoDB: Completed initialization of buffer pool
    130221 14:23:38 InnoDB: Fatal error: cannot allocate memory for the buffer pool
    130221 14:23:38 \[ERROR\] Plugin 'InnoDB' init function returned error.
    130221 14:23:38 \[ERROR\] Plugin 'InnoDB' registration as a STORAGE ENGINE failed.
    130221 14:23:38 \[ERROR\] Unknown/unsupported storage engine: InnoDB
    130221 14:23:38 \[ERROR\] Aborting
    130221 14:23:38 \[Note\] /usr/sbin/mysqld: Shutdown complete

I didn't come up with many solid answers after researching. I first tried all sorts of things on the database:

1.  Using [mysqlcheck](http://dev.mysql.com/doc/refman/5.0/en/mysqlcheck.html). Still crashing
2.  Making sure all the tables on my DB where set to use InnoDB instead of MyISAM (a good amount were not actually). Still crashing
3.  Dumping the database, deleting it, and re-importing. Nope, still crashing

I was finally able to determine that MySQL was running out of memory thus causing the crash. I confirmed this after restarting MySQL, and then running apache benchmark to throw a bunch of traffic at the time. I monitored memory usage as it climbed and eventually ended with MySQL crapping out. I never had this issue on my Linode, and it had the same amount of memory. Then I realized my stupid mistake, <em>I never tweaked my.cnf</em> for my server environment. I had already setup tweaked apache2.conf, but it slipped my mind to configure MySQL the day before.

It can be a bit daunting to understand how to properly set up a MySQL configuration for your server. There are a lot of things to consider - and most articles you find on the web won't be the magic bullet you're looking for. Ironically I found a [Linode article](http://www.linode.com/wiki/index.php/MySQL_and_Apache_Optimization_Guide) on some recommended settings for a small server instance. The settings recommended for my.cnf were drastically different (much lower) than what I had in my default my.cnf file - so I made some adjustments and restarted mysql with good results.

### Setting up swap
Even with the MySQL settings set as high as they were, I also found that Digital Ocean does not automatically configure swap for you on the kicked servers. I've never had to deal with this in the past, and I believe Linode set this up for me (I don't recall doing this myself). Luckily this seemed to be a common question for Digital Ocean. They have a step by [step by step guide](https://www.digitalocean.com/community/articles/how-to-add-swap-on-ubuntu-12-04) for setting it up on Ubuntu 12.04. Now, even if MySQL runs out of memory, it will start using up swap instead.

### Conclusion
It was a bit more work than I anticipated setting up the new VPS with Digital Ocean, but nothing too complicated. These types of problems are what you can expect to run into when setting up a web server from scratch - and it's honestly a great experience and something every web developer should know how to do. After running apache benchmark with the new MySQL settings and Swap setup, I've yet to run into an issue. I see good performance on the the VPS so far. Digital Ocean is still very new, and I'm not sure how long that $5/mo price point will last, but I haven't run into anything with the provider itself. Hopefully things continue to go well!