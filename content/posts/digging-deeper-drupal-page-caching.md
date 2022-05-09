+++
author = "Brian Osborne"
title = "Digging Deeper into Drupal Page Caching"
date = 2013-12-16
tags = [
  "Drupal Planet",
]
+++

I've been away from full time Drupal development for a couple of years and have recently returned, this time making a commitment improve my understanding of core. There's a lot of information out there on Drupal caching, but I found much of it to be fragmented and outdated (Drupal 6). I wanted to provide a more comprehensive look at Drupal 7's core caching, explaining how some of this stuff is actually working under the hood.

Measuring Performance
---------------------

Before we get started, it's worth discussing how you can measure the performance of your site so you can see for yourself the impact caching will have. The easiest way to do this is to use the [devel](http://drupal.org/project/devel) module, which most Drupal developers should already be familiar with. Among other useful features, this module allows you to print out the time it took to render the page and the total memory usage of PHP to serve a page request.

Devel will reveal that for a Drupal 7 site with a couple dozen contrib modules enabled and no caching enabled, about 30-50 MB of memory will be used to serve each page request. It will also show that page execution time (time it took to render the HTML) is around 400-500ms.

Generally, those numbers are not performant and you won't be serving a lot of simultaneous page requests before bringing your server down. You should always be concerned with optimizing your site to increase page response time and reduce memory usage. Even if you're not developing for high traffic sites, you want every visitor to have the best experience possible.

Another useful and easy to use tool is your browser's developer tools. Years ago you had to use FireBug w/ FireFox, but most of the FireBug features are now built into the dev tools native to all the popular browsers, including Internet Explorer (which isn't so bad these days!).

I use Chrome, and the rapid release cycle for the browser means the packaged dev tools suite is very robust and constantly improving. For looking at performance of your site, dev tools is useful in showing you the number of HTTP requests made (the fewer the better), the time it took the server to respond to these requests, and the HTTP headers sent and received for each request. I encourage you to explore the dev tools and discover their usefulness.

How Drupal sets cached pages
----------------------------

Page caching is when Drupal takes the _entire rendered output_ of a page and stores it in the database (or another cache store; defaults to the database). Pages will only be cached for anonymous traffic and for users that don't have session data, like items in a shopping cart. This is because if dynamic data like a shopping cart or a "Welcome Brian" message was cached, it would screw things up when that cached page was delivered for other anonymous traffic.

We need to look into how Drupal loads up every time a page is requested. It's not as complicated as you may think, and it's fairly straight forward to follow. This process is called "bootstrapping" and is split into many different phases. Each phase loads a different part of Drupal, progressively loading more core API functions, theme code, and module code.

If we look at `index.php`, you can see a call to `drupal_bootstrap(DRUPAL_BOOTSTRAP_FULL);`. If you take a look at the [the code](https://api.drupal.org/api/drupal/includes%21bootstrap.inc/function/drupal_bootstrap/7) for `drupal_bootstrap`, you can see each of the 8 phases and get an idea of what each is doing. Drupal's index.php passes in `DRUPAL_BOOTSTRAP_FULL`, which indicates that every single phase should be executed to load the entire environment. Each phase is loaded in succession. Also of note are the comments for this function, which indicate how you could call `drupal_bootstrap` yourself to load the Drupal environment for a custom script (very useful!).

So how does page caching tie into this? Well, more time, memory, and CPU is used for each bootstrap function that is loaded. Under normal circumstances, each phase of the process is needed so that the page can be properly rendered. However, when page caching is turned on, and the page is eligible to be cached, Drupal will store the rendered page output via the [drupal\_page\_set\_cache](https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_page_set_cache/7) function. This function is called right before the rendered output is flushed and delivered to the browser.

I mentioned above that the page must be eligible to be cached. Even with page caching enabled, Drupal may prevent some pages from being cached. An example is a page that displays a dynamic message, like when a user doesn't fill out a form properly and validation errors are displayed. You wouldn't want that message to be part of the cached page result.

Also of note, there's a useful function [drupal\_page\_is\_cacheable](https://api.drupal.org/api/drupal/includes%21bootstrap.inc/function/drupal_page_is_cacheable/7) that can be used to instruct Drupal NOT to cache the page it was called on.

How Drupal serves cached pages
------------------------------

Now let's say the user reloads that page that was just generated and cached. Drupal again kicks off the full bootstrap process, but this time things are different because of the second phase of the bootstrap: `DRUPAL_BOOTSTRAP_PAGE_CACHE`. This phase is used to determine if a cached page can be delivered to the user, and if so, output it directly. The code is simple to follow. Checks are made to see that:

*   The user has no Drupal session cookie (therefore, user is "anonymous" with no dynamic data)
*   Page caching is actually enabled
*   A page cache entry exists for the requested page

If all three conditions are met, then Drupal loads the cached data out of the cache store using [drupal\_page\_get\_cache](https://api.drupal.org/api/drupal/includes!bootstrap.inc/function/drupal_page_get_cache/7), outputs it, and **exits out of the bootstrap process early**.

It's worth noting that under normal circumstances, two additional phases are loaded before Drupal can serve the page cache (thanks Mark Pavlitski for the heads up): `DRUPAL_BOOTSTRAP_DATABASE` and `DRUPAL_BOOTSTRAP_VARIABLES`. The database phase is needed so Drupal knows how to access the cache, which by default is stored in the database. The variables phase will load all the settings in the variables table and load the "bootstrap" modules (see next section for more info on that).

Alternative cache implementations (like [memcache](https://drupal.org/project/memcache)) don't typically need the database for anything when serving a cached page. You can explicitly tell Drupal to skip loading up the database and system variables by setting `page_cache_without_database` to false in the settings.php file to make responses even faster. Note that since the "bootstrap" modules are not loaded when this setting is enabled, you can't use the hooks discussed below. Everything has a trade off when it comes to performance.

Two hooks you can count on
--------------------------

Since the cache delivery happens almost immediately and early in the bootstrap process, most of Drupal's core API and modules are not loaded at all. That means you cannot run any hooks that affect page output. However, there are two hooks that Drupal will execute even on cached page delivery: [hook\_boot](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_boot/7) and [hook\_exit](https://api.drupal.org/api/drupal/modules!system!system.api.php/function/hook_exit/7).

How are any hooks executed if Drupal doesn't load the hooks system and modules that implement them (this happens at a later bootstrap phase)? Well, when a module implements `hook_boot` or `hook_exit`, Drupal makes note of it in the "system" database table when the module is enabled. These modules will be loaded on demand when the hooks are invoked in `DRUPAL_BOOTSTRAP_PAGE_CACHE`. However, the more modules that implement these hooks, the slower it is for Drupal to actual serve a cached page entry (more code = more time).

Modules can use `hook_boot` to execute any code that must run on _every_ page, where as its companion [hook\_init](https://api.drupal.org/api/drupal/modules!system!system.api.php/function/hook_init/7) is invoked only when a page is first rendered (meaning not on cached pages).

Almost all of the time you'll want to use `hook_init`, typically for things like adding specific CSS or JS files to a page. `hook_exit` is used to execute any code after a page has already been sent to the browser and right before the php process exits.

The popular [devel](https://drupal.org/project/devel) module uses `hook_boot` so it can ensure its profiling code is run even for cached pages. Note I previously wrote that the [redirect](https://drupal.org/project/redirect) module implemented hook\_boot, but that is incorrect. Must have been a late night when I wrote that!

Both `hook_boot` and `hook_exit` can actually be disabled on cached pages as well to provide even further performance gains for cached pages. This can be done by setting `page_cache_invoke_hooks` to false in your settings.php file. A lot of modules rely on those hooks though, so you'd really need to understand the repercussions of turning those hooks off. In Drupal 6 you could control this on the performance settings page, but now it's just an override in your settings.php file.

Page compression
----------------

Once you enable page caching, Drupal will reveal an additional option on the performance page called "Compress cached pages." Doing so, Drupal will first compress the rendered content using PHP's `gzencode` function (see [drupal\_page\_set\_cache](https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_page_set_cache/7)) before saving it. This reduces size of the data to store in the cache dramatically, as well as offering an additional benefit! Web browsers can accept this compressed content directly and uncompress it themselves.

Browsers that support this (just about all of them) add a header indicating as such, and Drupal will deliver the gzipped content directly to the browser. The heavy lifting of decompressing the data is left up to the resources on the users machine - which is a good thing. It reduces the load on your server (except for that first "hit" that must be compressed) and decreases the transfer time and bandwidth.

If page caching is disabled, Drupal won't compress it before delivering it, but your web server can do that if you wish. [Apache](http://httpd.apache.org/docs/2.2/mod/mod_deflate.html) and [Nginx](http://wiki.nginx.org/HttpGzipModule) both support this. [There's some debate](https://groups.drupal.org/node/24318) about whether you should use this in conjunction with Drupal's compression or not. For the most part you should be okay just having Drupal handle it for you. If you are working on a site where performance is a huge concern, this is something you'll need to look into more yourself.

Performance gains
-----------------

The benefits of page caching are immediately clear. Above I mentioned that a Drupal site could use around 30-50 MB of RAM just to serve one page request. While that RAM is used for only a half second or so, it severely limits the amount of traffic you can serve. If a cached page is delivered instead, you're looking at around 2-4 MB of RAM paired with a dramatic improvement in page response time.

You won't be able to use the Devel module to print out the memory usage and execution time for cached page results. That's because Devel has no opportunity to alter the output of a cached page (nor does any other module, as I discussed above). I wrote a blog post a while back explaining how you can [determine php memory usage for cached page results](http://bkosborne.com/blog/determine-php-memory-usage-anonymous-users). Check if out if you're interested.

Your browsers dev tools will also show the dramatic improvement in response time. To test it out, clear your page cache (in performance settings, or using drush) and then load a page with dev tools open. Note the time it took to get the page from the server. Now reload the page and look at the time again (make sure you're logged out). On the second request, Drupal is returning the cached page that was stored from the first request.

Of all the caching methods available in Drupal core, page caching is by far the most effective and performant. Of course it's of no use unless you're serving to "anonymous" logged out users, but the majority of Drupal sites are probably aimed toward static content delivery.

How and when the page cache is cleared
--------------------------------------

There's a Drupal function called [cache\_clear\_all](https://api.drupal.org/api/drupal/includes%21cache.inc/function/cache_clear_all/7) that is used all over the place to wipe out cache entires in various "bins". Here are some of the actions that trigger a call to `cache_clear_all`, clearing (among other cache bins) the page cache:

*   A node is created/edited/deleted
*   A block is created/edited/deleted
*   A comment is created/edited/deleted
*   A vote is registered in a poll
*   User profile fields are manipulated
*   System theme settings are changed
*   Taxonomy terms/vocabularies are manipulated
*   Permissions for roles are changed
*   **Cron is run**

That's quite a list! Why do so many actions trigger a cache clear? For the most part, it's because _Drupal doesn't know where your content is displayed on the site_. It's not quite intelligent enough ([but it will be in Drupal 8](https://drupal.org/node/636454)). It makes the assumption that any one of your cached pages may include a poll, a node, a comment, a taxonomy term, etc. So any time those are changed or added, Drupal clears the entire page cache!

Here's a common example: Let's say you have a View that displays the 5 most recent news articles on your homepage. When you submit a new news article, you'd want that list to be updated. The only way that list is updated is if you clear the page cache entry for the homepage, or else it will display stale content.

All those cache clears can be problematic for a site that sees even a small amount of updates. Whenever a cached page is wiped out Drupal has to regenerate it on the next hit. That unlucky visitor will have to wait a few seconds while the whole thing is rendered instead of the snappy cached version. To combat this, Drupal allows you to enforce a minimum amount of time a cache must be valid.

Minimum cache lifetime
----------------------

This is a setting on the performance page and has been [confusing users](https://drupal.org/node/1279654) for years. The minimum cache lifetime determines the _minimum amount of time that must pass between entire cache clears_. Many users misunderstand this setting to instead apply to the lifetime of individual cached entries, but it has _nothing_ to do with individual entries. If you have the min set to 10 minutes, you could create a new page and have it only be cached for 1 minute before it is cleared from the cache. It doesn't mean that a page will be cached for 10 minutes at the minimum or automatically cleared out after 10 minutes. Nothing is broken, this is how the system is designed for better or worse.

If you don't have the min lifetime set (which is the default), the page cache will clear no matter what on any of those above actions (including cron!). If you do set a minimum, anytime `cache_clear_all` is called to clear the page cache, it will first set a system variable indicating the timestamp of the request. On a subsequent request to clear the page cache, Drupal compares the current time to that previous time that was recorded. If it exceeds the minimum you set, it will then clear the cache.

No matter what you do, the process is very inefficient. What this usually means is that a lot of a your visitors will be hitting non-cached pages and having a bad experience. One solution is to "warm" the cache after it has been cleared. You can do that by using a crawler that hits all pages on your site. You can also use [boost](https://drupal.org/project/boost), which has a built in crawler and more advanced cache logic. Sites with serious traffic will probably use a reverse HTTP proxy like [Varnish](https://drupal.org/project/varnish) instead of Drupal's page caching. There's also the [Alternative Database Cache](https://drupal.org/project/adbc) module that aims to correct some of these core shortcomings (thanks to Eric Peterson for authoring and bringing to my attention).

Expiration of Cached Pages
--------------------------

This other option on the performance page is more straight forward and hopefully shouldn't confuse people thanks to the helpful comment alongside it. At first you may think this is a way to control the maximum amount of time an individual page will be cached before Drupal forces a new rendering of it. However, the comment reads "The maximum time an external cache can use an old version of a page."

This will control the HTTP response header `Cache-Control`, setting the parameter "max-age" to whatever value you indicated. HTTP reverse proxies like Varnish or Nginx (or a CDN like Akamai), which can provide an extra caching layer in front of Drupal, use this important header to expire cached pages in _their_ cache. The setting has nothing to do with Drupal's internal caching mechanisms.

Conclusion
----------

Page caching is a no-brainer for most websites. It dramatically reduces system resources consuming when serving pages and allows for you to serve much more traffic at once. A lot of the page cache settings may seem counter intuitive. It can take hours to really dig through the code and see what's going on and try to figure out why. Hopefully this blog post can clear up some of the confusion and give you a better understanding at what's happening under the hood.

I plan on writing up more on the other forms of caching in Drupal, like Views, Block, and Form caches. Stay tuned, and please comment below.