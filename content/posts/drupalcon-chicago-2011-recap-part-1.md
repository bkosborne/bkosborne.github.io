+++
author = "Brian Osborne"
title = "DrupalCon Chicago 2011 Recap - Part 1"
date = 2011-03-18
tags = [
  "Drupal Planet",
]
+++

Hello all... I've just returned from my first [DrupalCon](http://chicago2011.drupal.org/) this past week, and it was a great time & experience. It was also the first time I was on a commercial flight, but all went well. I've been developing sites in Drupal for about a year now and couldn't have been more excited to head to Chicago to check out the bi-annual conference. Chicago is certainly a beautiful city!

There were certainly over 3,000 people at the Sheraton Towers in downtown Chicago, and it was a bit mind-boggling to see the insane amount of people equally obsessed with Drupal as I am. A large focus of the conference was the recent release of Drupal 7 what to look forward to for Drupal 8. It's going to be a few more months before I adapt Drupal 7 in a site, but I'm eager to get started on some small projects.

The conference started off with memorable keynote speech by Dries Buytaert and lasted for three days straight, with some small activity and events before and after. I'm going to go through some of the memorable sessions I attended - both as a record for me and a good read for you.

[Media](http://chicago2011.drupal.org/sessions/media)
-----------------------------------------------------

This was the first session I attended at DrupalCon. The two speakers, Aaron Winbor and Jacob Singh, are actively involved in the development of the new multimedia workhorse of Drupal 7. It's my understanding that this module is the logical replacement of the wonderful [emfield](http://drupal.org/project/emfield) module, and it looks promising. The module is not quite ready yet but the speakers assured it was close. A few large sites were already using it.

What really excites me about [Media](http://drupal.org/project/media) is the new unified interface to adding and managing media throughout the site. This means a new overlay interface with lists of all multimedia content in one place, and shareable on any node. There are some nice bells & whistles that will please content managers who are used to working with the clunky IMCE interface.

The module has been in development for while now and has a growing issue queue. Seems to be rather buggy at the moment, but they are working their asses off to get Media production ready. This is a pretty major module I'll be keeping an eye on for Drupal 7 development.  
 

[Rockin’ HTML5 with Drupal](http://chicago2011.drupal.org/sessions/rockin-html5-drupal)
---------------------------------------------------------------------------------------

Jenn Simmons gave a great, well delivered session on the topic of HTML5 and Drupal. As a web developer, it's impossible not to get swept up in the buzz surrounding the new standard. It's a bit hard to get super exited about it however; several of the projects I work on require compliance with older browsers that are far from supporting it). Jenn assured the audience that HTML5 degrades nicely for the most part, and there is a JavaScript plugin which adds support otherwise.

Something to take away from this session was the great new features introduced with the standard. Creating forms is going to be a helluva lot easier moving forward, with added support for specific field types like phone numbers. Semantics are a huge focus in HTML5, with new elements such as "header", "footer", and "aside" which provide a nice alternative to the typical Drupal theme utilizing a huge amount of div markup. Looks like a dream come true for SEO. Offline in-browser storage, amount a slew of other new features, is now introduced as well. I'm not quite sure how to utilize this yet and Jenn didn't get into much detail.

A major issue discussed was the current lack of support for HTML5 in Drupal 6 & 7. Jenn seemed pretty flustered by the fact that is it still pretty difficult get modules and themes to output HTML5 (without some significant code diving). A related discussion touched upon in the Dries keynote was having Drupal 8 un-coupled from HTML completely. This would allow data to be output via any format, such as XML, HTML, JSON, etc. This allows for Drupal to serve as a service architecture and wonderful news for mobile developers using Drupal as a platform.

There is a module called [HTML5 Tools](http://drupal.org/project/html5_tools) and a base theme called [HTML5 Base](http://drupal.org/project/html5_base) that a worth a look.  

[Crafting App Interfaces with jQuery](http://chicago2011.drupal.org/sessions/crafting-app-interfaces-jquery)
------------------------------------------------------------------------------------------------------------

jQuery is a huge part of Drupal so I thought I would check this session out. It was a bit less than I had hoped for, but still took away some good info. The presenter was an HP employee Nathan Smith who certainly knew his way around jQuery. He showed off his baby, an incredible [desktop interface](http://desktop.sonspring.com/) made completely in jQuery. I believe he created it just to mess around and learn new things, but it sure turned out great. It really demonstrates why jQuery is such a great and powerful JavaScript library.

Part of the session was dedicated to how to write jQuery in a way that doesn't hurt site performance. He showed off some interesting code and gave away a few tips. To be honest, most of what he discussed was stuff I've already learned and been through. Biggest tip I can give anyone writing jQuery: target elements using an ID!  
 

[Views for Hackers](http://chicago2011.drupal.org/sessions/views-hackers)
-------------------------------------------------------------------------

I was really excited to check this session out but left a bit disappointed. The session was slow to start and get rolling, and the presenter didn't go into as much detail as I had hoped. He tried to demonstrate how to extend and customized views to do what you want, but it just didn't flow that well. He gave a very good overview of how views works and how it builds a query, but there was a beginners session scheduled the next day or that demonstrated this. The questions asked at the end were obnoxious at best, with several people asking basic Views questions that could have been googled. I suppose I'll just have to wade through the documentation (or lack there of) of extending views a bit more.

* * *

That's all for day 1. Check back soon for an overview of the sessions I attended on Wednesday.
