+++
author = "Brian Osborne"
title = "Creating a primary and secondary horizontal menu in Drupal"
date = 2012-01-23
tags = [
  "Drupal Planet",
]
+++

This is a fairly common menu structure for Drupal developers to deal with, and depending on how it functions, it could be super easy. Here's the scenario: you need to display the 1st level of links in a menu horizontally, and the 2nd level in that same menu right beneath it.

There are at least two different ways that this can be implemented. The first is a static approach, where you do NOT need to show children of each primary link when hovering.

Consider this menu strucure:

\- About  
\--- Staff  
\--- Company  
\--- Employment  
\- Services  
\--- Our Industries  
\--- Our Packages

![](http://www.bkosborne.com/sites/default/files/node-images/Screen%20shot%202012-01-22%20at%209.39.52%20PM.png)The static approach means that 2nd level of horizontal links will only be populated with links from the active menu trail. So when you are on the "About" page, the second level is populated with Staff, Company, and Employment links. If you want to see sub-links of Services, you have to first browse to the Services page. To get this method working quickly in Drupal, make sure your menu settings are configured to populate the main links and secondary links variables properly (see screenshot). Then you can place them one on top of another and you're good with some CSS styling.

However, this approach could be considered undesirably if you want your visitors to find what they are looking for quickly.

That brings us to the second approach, which is using JavaScript to show the second level of links for each primary when hovering over any of the primary links. That means that if you hover over the "Services" link, the second horizontal menu is populated with Our Industires and Our Packages. When hovering over "About", the second level dynamically changes to those sublinks. You get the idea.

The problem? You need to pre-load all menu link for the 1st and 2nd level of the menu. Thanks to the [Menu Block](http://drupal.org/project/menu_block) module, this is very easy to do. Just add a new menu block and configure it to "expand all children of this tree" and set a maximum depth of 2. This should populate the menu block with every link in the 1st and 2nd level of the menu.

By default, your menu block is probably configured to display nicely as if it was in a sidebar or something. We don't want that. You want to use your super CSS skills to get the links displaying horizontally for both levels. Set each <li> in the menu block to float to the left or display inline instead of block. The hard part here is creating the second level of links to display properly. Because a the 2nd level of links are displayed as a <ul> within the parent link's <li>, you have an issue here. This can be solved by setting all of the nested <ul>'s (second level) to position: absolute, and giving them a "top" value of enough spacing to push them below the primary links. You'll probably need to give the <ul>'s for both the 1st and 2nd level a fixed width and height as well. Don't forget to set position: relative to a <div> that encapsulates all of the menu links as well.

Obviously each implementation will be different here, but this is a pretty good approach given how menu block outputs it's menu tree in HTML. After you get the CSS coded out, you'll want to add some basic jQuery to hide/show the second level <ul>'s based on where the user is hovering.

This approach should work for all major browsers, including \*gasp\* IE6.
