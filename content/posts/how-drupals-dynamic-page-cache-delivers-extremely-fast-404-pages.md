+++
author = "Brian Osborne"
title = "How Drupal's Dynamic Page Cache Delivers Extremely Fast 404 Pages"
date = 2020-12-20
tags = [
  "Drupal Planet",
]
+++

Lately I've been working to improve the response time of 404 pages on some Drupal 8 sites I help maintain. Depending on the complexity of the site and the status of Drupal's various cache layers, Drupal can be quite slow to generate a full page response, even for a 404 page. These sites I maintain are occasionally subjected to aggressive security penetration scans which generate a ton of 404 responses for completely unique URLs. The scanner can send tens of requests per second like this which can quickly exhaust the resources of a web server.

While the best way to protect against scans like this is to use a web application firewall, making sure your site makes efficient use of its caching capability goes a long way.

## Caching & Dynamic Page Cache

Drupal has a simple full page cache called Internal Page Cache. This module caches requests based entirely on the request URL and is only used for anonymous traffic to your site. Unfortunately, this type of cache does not help at all when delivering 404 pages for unique URLs, even though the contents of the page are likely identical for all of them. However, Drupal 8 also comes with the innovative Dynamic Page Cache module, a much more intelligent page cache. 

As the "dynamic" name implies, the Dynamic Page Cache caches as much as the full page response as it can and creates placeholders for things that are too dynamic to cache and fills them in later. This alone makes it incredibly useful, and in many cases, this module creates a cache entry for a page that's nearly as complete as the entire page response. But there's also another subtle advantage which is of special interest to 404 pages: The same cached response can be used for multiple different unique URLs.

To illustrate how this works, understand that all caches use an ID that uniquely identifies that cache entry. This ID is used to lookup cache entries to see if there is a cache hit. Most traditional page caches like Drupal's Internal Page Cache and Varnish use the complete URL of the requested page as this cache ID. There's really not much else they _can_ use, as they have to make a decision about what cache entry to look for based entirely on the request data. These caches assume that each unique URL produces a different output, so a request for https://sample.com/some-fake-page and https://sample.com/some-other-fake-page are treated as unique cache entries, _even if their output is identical_. These caches are just not intelligent enough to _know_ that these two requests result in the same output.

However, instead of using the URL of the request as the cache ID, Dynamic Page Cache uses a combination of all [cache contexts](https://www.drupal.org/docs/drupal-apis/cache-api/cache-contexts) for that page. In Drupal terminology, cache contexts indicate the parameters by which the content of a page varies. For example, a page with the "user.roles:authenticated" cache context indicates that some content of that page will be different depending on if the user is logged in or not. As such, the Dynamic Page Cache module would create a unique cache entry for logged in and logged out users.

While there is a cache context for "url" (which would cause every cache entry to be vary based on the URL, just like the Internal Page Cache module), many pages do not require it.

## Dynamic Page Cache & 404 Pages

The power of Dynamic Page Cache is that it lets developers get much more granular and specific in determining the uniqueness of a pages output via these cache contexts. As it turns out, for most pages on your Drupal site, the _route and route parameters_ (via the "route" context) are the primary indicator of page uniqueness, not the URL.

Consider how 404 page responses are delivered by Drupal. A site admin configures what page on your site to display when it can't find any real page that matches the request. Many site admins will create a basic page node and use that as the 404 page. Drupal ultimately resolves this path down to its route and route parameters, which in this example may be "entity.node.canonical" with a parameter of "123" for the node ID. _This will always be the same for every 404 path on your site_. Requests to your site for "/fake-page-1" and "/fake-page-2" both resolve to that same route.

While traditional page caches always result in cache misses for "/fake-page-2" even if there's a cache entry for "/fake-page-1", Dynamic Page Cache knows that both of these requests resolve to the same route and both of these requests use the same cache ID, so it can re-use the cache entry for both requests. This has huge implications for performance of serving 404 pages since Drupal can skip so much of the rendering process when delivering that second response for "/fake-page-2".

Another example to illustrate this: Imagine you have a "/contact" page on your site. Traditional page caches cannot use the same cache entry for requests for "/contact" and "/contact?test=123" because they assume that the query parameter varies the output. But unless your "/contact" page tells Drupal that it should use the "url.query_args" cache context, Dynamic Page Cache doesn't care about that useless query parameter and will use the same cache entries for both requests.

The Dynamic Page Cache module can therefore dramatically improve the response time of delivering 404 pages to your visitors (or attackers and security scanners) for unique URLs because it can reuse the cache entries for multiple unique request paths.

## How to Screw It Up

While it's true that most pages on your site likely don't vary specifically by the URL, some certainly do. Here's some examples:

* A special 404 page that displays some helpful links depending on what URL the user was trying to access that no longer exists.
* Blocks with visibility conditions based on the request path
* A views block that appears on every page of your site with a URL based argument configured

In all of these situations, the "url" cache context (or its more specific children like "url.path") will be added to your page's cache metadata. This tells the Dynamic Page Cache module that the response actually does vary based on the URL of the request. If your 404 page has this cache context, Dynamic Page Cache won't be beneficial! Every request for a page that doesn't exist will bypass Drupal's largest front-line caching layers.

While this "url" cache context is not added by default in simple site configurations, you can see from the examples above it's not too difficult to get in a situation where this context is added to every page, thereby tanking the performance of your 404 pages.

Finding the reasons that the "url" or "url.path" cache contexts are added to your pages is key to seeing if you can remove them. For example, if you have a block with a path-based visibility condition that simply lists a single page and not some wildcard path, consider embedding the block on that page directly using a different method. If you have a views block with a contextual filter (argument) configured, it may be adding the url cache context, [even if the block doesn't actually vary based on the entire URL](https://www.drupal.org/project/drupal/issues/3092715). Consider using the [Views Advanced Cache](https://www.drupal.org/project/views_advanced_cache) module to override this and provide the more specific cache contexts you need. If you have custom code that adds the "url" or "url.path" cache contexts, consider if there's a more specific context you can use instead. Perhaps you really just depend on the presence of a query string parameter and not the full path?

## Summary
Drupal 8's unique Dynamic Page Cache module can give your site huge performance gains and add more resiliency to simple denial of service attacks and security penetration scans. However, it requires diligence when building and developing your site so that you are aware of the various cache contexts that your pages use. This is especially true of 404 pages, where you really want eliminate the very "expensive" cache contexts like "url" to ensure the biggest performance gains.