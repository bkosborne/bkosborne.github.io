+++
author = "Brian Osborne"
title = "Looking at the new FullCalendar module for Drupal"
date = 2012-02-13
tags = [
  "Drupal Planet",
]
+++

Last weekend @ DrupalCamp New Jersey, [Tim Plunkett](http://drupal.org/user/241634) presented a wonderful alternative of the [Calendar](http://drupal.org/project/calendar) module for Drupal, called [FullCalendar](http://drupal.org/project/fullcalendar). I could be wrong, but I believe this was the first presentation given on the module which is very close to a stable release for Drupal 7.

Many who have wanted to create calendars in Drupal have used the time-tested Calendar module that's been around for years. I haven't used that module in about a year, and I don't know what sort of advancements have been made in the Drupal 7 version of the module (also close to a stable release it seems). This blog post will focus on FullCalendar and its features, and not compare it to the Calendar module.

Side note: this post provides a very general overview of how to use the module and it's features. I urge anyone looking to use it to read over the [documentation](http://drupal.org/node/1045174), and visit the project page for a more comprehensive overview.

### Set up

Creating visual calendars in Drupal should be a fairly easy thing to do - and that's what FullCalendar sets out to do. After installation, all you have to do is create a view, set the format to FullCalendar, and add a date field. With just the base module installed, the style format settings allow you to do a good amount of customization to the Calendar. The whole module is based on the [FullCalendar jQuery plug-in](http://arshaw.com/fullcalendar/) created by Adam Shaw, which itself is based on Google Calendar. So just about anything that can be done using that plugin is also possible to do using the Drupal module.

You can have the calendar display Month, Week, or Day and allow the user to toggle the displays without reloading the page or generating a new Views Display. There will likely be support for a Year view down the road as well, but that's dependent on the maintainer of the jQuery plug-in itself and not Tim. There are options to control how the header of the calendar looks, change the time & date formats (for _everything_), and control which fields in the view correspond to the functional components of the calendar.

If you want more control over the plugin, enable the FullCalendar Options sub-module. This will present you a ton more options when editing the display format settings in the view. Casual users won't need these options, thus the reason for them being placed in an additional module.

### Styling/Theming the Calendar

Out of the box, the module utilizes jQuery UI to theme the calendar. This presents a beautiful looking calendar that can change to match your site's theme quite easily using [ThemeRoller](http://jqueryui.com/themeroller/). If you aren't satisfied with the display options you have with jQuery UI, you can disable the inclusion of it so you don't have to fight against all of the complex styles that are added by utilizing it. Instead, you can look at the markup that is produced from the module and style it as you see fit. It will take longer, but you'll have much more control over how it looks.

If you want to embed the calendar into a smaller block on the site - that's easy enough. Just create a block display and the calendar will adapt to fit the area the best it can. For whatever the calendar style settings can't do, you can override using CSS.

If you are using the Colorbox module, you can also have events open up in a Colorbox on click instead of bringing you to the event page. You can specify which HTML element from the event you want to bring in and display in the colorbox. This is a really great feature to have. And of course you can change the styling of the colorbox using that module or your own CSS.

### The power of FullCalendar

Because the module uses jQuery so heavily, you can do some really powerful things with it. Right out of the box, users have the ability to drag events to new days and even change their time. This is done simply by dragging the events from one day to another or expanding the box they take up on the calendar to change their times. This can be disabled if that's not what you need the Calendar for, or a module can hook into the access permissions for doing this for fine control.

Enable the FullCalendar Colors sub-module (and it's dependency, the Colors module) to bring in coloring for events based on Node Types, Taxonomy Term, and User Roles. You can control the weight at which the colors are applied to each event as well!

By default all results from the view are loaded, even if the user may never view them. FullCalendar supports Views AJAX to only load the results that are needed for the display type - so that's a relief for performance junkies.

Many many be asking what happens when JavaScript is turned off? FullCalendar creates the calendar dynamically using JavaScript so when it's turned off, it displays just a flat list of the events and nothing more. I think that this is a good compromise. All of the events and their date/times are still viewable, just not with the great calendar view.

But the true power of FullCalendar is that it's _really_ good at one thing - displaying calendars with your events in them. jQuery is utilized heavily to create a really slick and customizable appearance that will leave site builders in awe at the pure ease of creating a simple calendar display for a site their working on.

### Closing Comments

FullCalendar is seeing a ton of active development at the moment. Tim stated during the presentation that bug reports are features are usually implemented really quickly - in part because the other maintainer lives in Europe so one of them is typically available. I can't see a reason why someone would opt to use the Calendar module instead of FullCalendar. The only thing that FullCalendar doesn't seem to have that Calendar does have is Year view, but like I said earlier, that may eventually make it in.

Something that was stressed by Tim is that FullCalendar is for _display only_. There are a few functional aspects of the module like dragging around events to change their time/date, but not much more. There's some recent code in the FullCalendar Create module that allows admin's to create events using the calendar interface to select the date to create the event under, so that's something to keep an eye on... But for the most part, FullCalendar doesn't attempt to do more than just display a calendar - and it's _very_ good at doing it!