+++
author = "Brian Osborne"
title = "Client-side content inclusion in Drupal"
date = 2012-02-04
tags = [
  "Drupal Planet",
]
+++

At [DrupalCamp NJ](http://www.drupalcampnj.org/) (the first ever, glad NJ is getting some love!), Jesse Beach from [Acquia](http://acquia.com) presented her thoughts how content is served in Drupal, and how to fix some problems that have surfaced over the years of web development. The traditional method for serving content to browsers is to have the server send off the entire DOM all at once. This was okay when websites were much simpler and developers created them using individual HTML pages, but websites are much more complicated now and often behind a complex CMS like Drupal.

The problems
------------

To help illustrate this problem, lets take a small Drupal 7 site configuration into consideration. When Drupal serves a page, it does _a lot_ of work for that one request. Drupal needs to load and process every module that is enabled on your website all at once, which is part of the Drupal "bootstrap" process. This uses up a ton of memory on your the web server... A Drupal 7 site with ~20 contrib modules enabled could use between 40-50MB of RAM every time a page is requested! To alleviate this, there are a ton of ways you can cache data to prevent the server from working too hard. With anonymous page caching, APC, and CSS & JS caching enabled, you can reduce that memory usage to around 5 MB per anonymous page request. Big difference!

Even with all of the advances in caching techniques, there is a large push to alter the traditional way that pages are served. Jesse's presentation focused on what's called client-side content inclusion. All this really means is loading content _as it's needed_ and not all at once. Most users only care about the primary page content, especially on mobile devices where bandwidth is a big concern.

How it's done
-------------

So how is this done? Instead of having the server work to serve everything that can possible be on the page, have it instead just load the menu and main content area. In addition, instead of having the server handling templating the data, have the client do it instead. Web browsers and JavaScript are good at working with the DOM, and web servers are best at processing code and sending off data.

There's are two main ways this is handled. The first is providing links to load secondary content, like a sidebar containing related articles, or comments for a blog post. Once a user clicks these links, JavaScript intercepts the click and sends out a POST request to the server for the additional content. Once the server responds, JavaScript injects the content into the DOM dynamically.

The second approach is similar, but instead of loading the content when a user takes an action, the content is automatically loaded _after_ the main content has already been retrieved. This is not a new concept by any means, but it's not something that's easily achieved today. However, this approach is not ideal for Drupal because every request bootstraps the whole CMS.

Ideally the bits of requested data from the server is presented in a raw format like JSON. Then you can use a JavaScript templating plugin to inject the data into an HTML template before then placing that content in the page DOM. This approach takes significant load off of the web server and hands it off to the web browser. Browsers like Chrome and Firefox are constantly releasing new versions with faster and faster JavaScript engines in them that help to improve this workflow.

Thoughts & concerns
-------------------

One big concern with doing client side content inclusion is how it effects SEO. Since search engine crawlers don't load JavaScript, they won't see your secondary content that isn't served with the primary page. To get around this, there are some "best practices" you can use for both SEO and accessibility (screen readers don't load JS either). Have each "loader link" for secondary content actually go to a real page with the content on it. That Google and screen readers will follow those links just fine and index the content, but you can disable that linking using JavaScript for normal page views. Jesse also mentioned that Google recently blogged about how they are going to begin processing JavaScript on pages though, so that's something to keep an eye on. I couldn't find the blog post she mentioned, but did [find this](http://support.google.com/webmasters/bin/answer.py?hl=en&answer=81766).

As for Drupal, work is being done to make it easier to include content without doing massive Drupal bootstraps each time. Check out the [Content API](http://drupal.org/project/contentapi) module (in active development). There is also a fairly large initiative for Drupal 8 called [WSCCI](http://groups.drupal.org/wscci) that is attempting to make Drupal more of a "service" with a CMS on top of if. That will make it much much easier to do content includes dynamically.
