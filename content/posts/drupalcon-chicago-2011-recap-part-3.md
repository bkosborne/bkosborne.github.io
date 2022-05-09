+++
author = "Brian Osborne"
title = "DrupalCon Chicago 2011 Recap - Part 3"
date = 2011-04-21
tags = [
  "Drupal Planet",
]
+++

I had a great time at [DrupalCon](http://chicago2011.drupal.org/). This was the last day of presentations before I had to catch my flight back to Philly. Because of my flight time I had to miss the last session track and any meet ups that night, but such is life. My favorite presentation of the conference was on this day: [Drupal Commerce](http://drupal.org/project/commerce). I look forward to exploring all of these modules, ideas, and technologies in the upcoming months!

[The Render API in Drupal 7](http://chicago2011.drupal.org/sessions/render-api-drupal-7)
----------------------------------------------------------------------------------------

This was actually the first session I went to that focused completely on Drupal 7. The Render API is what allows you to easily remove HTML (markup) from code. I absolutely love everything about it, and was excited to learn more. Franz Heinzmann gave a great and informative presentation on the subject.

The basic concept of the Render API is that everything you want to be rendered, you place in a multi-dimensional array indicating how you want your data to be output. Rendering data this way allows other modules to easily hook into the pipeline and mess around with whatever they want. Render API unifies ways modules render data throughout the sites code base, using the Drupal render function. This is available in Drupal 6, but it didn't work that well and was full of limitations. I also believe it was used primarily for form outputs.

Now in Drupal 7, just about everything can be rendered using the API. I'm not sure how far along the documentation is yet and I haven't had time to check it out. Franz gave a pretty in depth look at how the Render API works though, and he did a pretty good job at it. Things got a bit technical in a few areas but I was able to follow along. Hopefully as I get more time to code for Drupal 7 I can blog more about the API.

[Drupal Commerce: Setting up Shop on Drupal 7](http://chicago2011.drupal.org/sessions/drupal-commerce-setting-shop-drupal-7)
----------------------------------------------------------------------------------------------------------------------------

Arguably the most anticipated session at DrupalCon. Ryan Szrama, the man being Ubercart and now [Drupal Commerce](http://drupal.org/project/commerce), demonstrated how to get a simple shop started in Drupal 7. He covered as much as he could in the hour he had for the session, but the audience was craving more.

As having worked on several eCommerce sites in Drupal 6 using Ubercart, I couldn't be more excited to see Ryan's demo site up and running. Every 5 minutes Ryan was explaining how a new feature in DC knocked out 6 contrib modules for Ubercart. Ryan stated that he has yet to be stumped by a developer wishing for a specific feature. The backbone of Drupal Commerce consists of Entities, Views, and Rules. I am so ecstatic that conditional actions are gone and replaced with Rules, and that every single admin form and user facing table is now handled in Views.

I have yet to get started on building a site using Drupal Commerce, but can't wait to get started. My agency often utilizes a gateway that is not commonly used throughout the industry, so a good way for me to get started is to write a payment module. I'm glad to see that Ryan has completely rethought and re-programmed everything from the ground up when working on Drupal Commerce. His goal is to have it be the #1 e-Commerce solution on the web, and I really believe it is possible.

[Drupal Module Coding: Leaner Techniques for Faster Websites](http://chicago2011.drupal.org/sessions/drupal-module-coding-leaner-techniques-faster-websites)
------------------------------------------------------------------------------------------------------------------------------------------------------------

Irakli Nadareishvili and Erik Summerfield gave a presentation on how to make Drupal as efficient as possible. Drupal is known throughout the industry as being a rather large memory hog and gets a bad rep for it. There is always a debate going on in the community around making the site efficient vs making the site practical and useful out of the box. Drupal 7 "ate" somewhere around 50 contrib modules which are now in core and more are in consideration (views?).

The session focused on how to write modules and code to focus on performance. While many of the large scale Drupal sites run on dedicated servers with great software support (memcached is a big one), a bigger portion run on shared hosting. Drupal has always had some problems running on shared hosting, most notably because of the PHP memory it requires. I've worked on sites that demand 128M or more, which is certainly a lot. Some shared hosting providers only allow you 32M or less, making Drupal 7 very difficult to work with.

Writing modules that can scale and utilize the server's resources efficiently is extremely important. I was surprised to find out that an average page load on Drupal runs over 100 queries to the database. This is of course attributed to the fact that Drupal is extremely modular and many modules run the same or similar queries to get the information they need. The presenters acknowledge this and suggest utilizing frameworks like [CTools](http://drupal.org/project/ctools) while writing code.

The session gave a great overview on the challenges that module developers are presented with and how to address them in a sensible way. I personally don't do to much heavy module development, but it's great to have a good understanding on how to write them the correct way!