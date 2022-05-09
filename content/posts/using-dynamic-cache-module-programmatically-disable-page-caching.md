+++
author = "Brian Osborne"
title = "Using the 'Dynamic Cache' Module to Programmatically Disable Page Caching"
date = 2014-02-07
tags = [
  "Drupal Planet",
]
+++

I recently worked on porting over a website to Drupal that had several dynamic elements throughout the site depending on the IP address of the user. Different content could be shown depending on if the user was within a local network, a larger local network, or completely outside the network.

When porting the site over, I realized that it wouldn't be possible to enable page caching for any page that had this dynamic content on it. In Drupal, standard page caching is all or nothing. If you have it enabled and a page is "eligible" to be cached, Drupal saves the entire output of the page and uses it for future requests for the same page (I go into much more detail about page caching [in previous blog post](/node/143)). In my case, if I enabled it, users who hit within one of the local intranets could trigger a page cache set, and now any users outside the intranet would view that same content.

I wanted a solution that let me either differentiate cache entries per by visitor "type" (but not role), or to at least prevent Drupal from serving cached pages to some of the visitors when a cached page already existed. I found a solution for the latter that I'll describe below. But first...

Why this is a hard problem
--------------------------

I already knew I could prevent Drupal from _generating_ a page cache entry using `drupal_page_is_cacheable(FALSE);`. In fact, there's a popular yet very simple module called [Cache Exclude](https://drupal.org/project/cacheexclude) that uses this function and provides an admin interface to specify which pages you want to prevent from being cached.

But what if you _wanted_ to cache the pages, but force _some_ visitors to view the un-cached version? This is what I needed, but Drupal has no API functions to do this. Many Drupal developers know that `hook_boot` is run on every page request, even for cache hits. So why can't you implement the hook and tell Drupal you don't want to serve a cached page? The reason is because of the way Drupal bootstraps, and when it determines if it should return a cached page or not.

There's a whole bootstrap "phase" dedicated to serving a cached page called [\_drupal_bootstrap_page_cache](https://api.drupal.org/api/drupal/includes%21bootstrap.inc/function/_drupal_bootstrap_page_cache/7). If you take a close look, you can see that Drupal doesn't invoke the `boot` hook until after it _already determined_ it's going to serve a cached page. In other words, there's no going back at this point.

Enter the "Dynamic Cache" module
--------------------------------

I came across the [Dynamic Cache](https://drupal.org/project/dynamic_cache) module that seemed solve this problem. Once enabled, this module lets you disable serving a cached page by setting `$GLOBALS['conf']['cache'] = false;` within your own modules `hook_book` implementation - exactly what I suggested was not possible above!

So how was Dynamic Cache doing this? In summary, Dynamic Cache implements `hoot_boot`, checks if you tried to disable serving the cached page, and if so will "hijack" the bootstrap process to render the whole page and ignore the page cache entry that may exist. In then makes sure to "finish" up the request by completing the bootstrap process itself and calling `menu_execute_active_handler();` that is normally done in index.php (but no longer get executed because of the hijack).

I want to note that what Dynamic Cache is doing is pretty scary in that it's almost hacking core without actually modifying any core functions. This fear is actually what triggered me to explore how the Drupal bootstrap process works under the hood so I could understand if there'd be any potential issues.

It's not an easy concept to understand initially, especially since for Drupal 7 you have to enable a second module called "Dynamic Cache Bootfix" that hijacks the bootstrap process a second time to properly finish up the request! I don't want to go into much more detail, but the modules code is pretty slim and I encourage developers to take a look. It will help you get a greater understanding of the bootstrap process and the obstacles this module tries to overcome.

There's also a [core issue](http://drupal.org/node/322104) that is trying to address this problem of not being able to easily disable a cached page from being served. I also encourage you to read thru that to get a better understanding of what the problems are.

How I implemented it
--------------------

In my case, I found that the majority of traffic to the site was from users outside any of the intranets, so I decided to allow them to both trigger cache entries being generated and to be served those cached page entries. For everyone else (a small % of traffic), Drupal would always ignore whatever was in the cache for that page and would also not generate a cache entry:

    function my_module_boot() {
      $location = _my_module_visitor_network();
      if ($location != 'world') {
        # Prevent Drupal from serving a cached page thanks to help from the Dynamic Cache module
        $GLOBALS['conf']['cache'] = false;
        # Prevent Drupal from generating a cached page (standard Drupal function)
        drupal_page_is_cacheable(FALSE);
      }
    }

Note that Dynamic Cache relies on having a heavy module weight so it runs last - which allows me to disable the cache in my own `hook_boot`. Make sure you read the README that comes with the module so you set everything up properly.

Also note that I still called `drupal_page_is_cacheable(FALSE);`. Without this, Drupal may still _generate_ a cached paged based on what this user saw. With my code in place, anonymous users outside the networks I was checking would both generate page cache entries and be served page cache entries. Anonymous users within the networks/intranets would never trigger a cache generation and would never be served a cached page.

Final Thoughts
--------------

Ideally, I would be able to generate separate page caches for each "type" of visitor I had. I think this is possibly by creating your own cache store (which is not that difficult in Drupal 7) and changing the cache ID for the page to include the visitor type. I think the [boost module](https://drupal.org/project/boost) may also allow for this sort of thing.

For really high traffic sites, you're probably going to be using something like Varnish anyway - and completely disable Drupal's page caching mechanism. I don't know much about Varnish but I imagine you could put this similar type of logic in the Varnish layer and selectively let some users through and hit Drupal directly to get the dynamically generated page (especially since my check for visitor network is just based on IP address).

There you have it. Dynamic Cache is by no means an elegant module, but it gets the job done! If you're better informed than I and I made a mistake somewhere in this writeup, please let me know in the comments. I certainly don't want to spread misinformation!