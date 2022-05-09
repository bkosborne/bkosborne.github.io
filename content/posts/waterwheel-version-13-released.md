+++
author = "Brian Osborne"
title = "Waterwheel version 1.3 released!"
date = 2011-05-23
+++

Finally got the time to put out the next release. I'm pretty happy with this one - I tied up a lot of loose ends and common feature requests. The most important feature is the circular support I added in. A lot of people desired to have a true infinite ciruclar effect (so there are no edges or walls), and I was planning on that for v1.4. However I was messing around with some ideas and in the meantime added in support for resetting the carousel to the opposite side very quickly when an edge is hit. Check it out! Here is the changelog:

* three new callbacks: movingToCenter, movingFromCenter, and movedFromCenter  
* 'active' class now added to item in center, configureable thru options  
* keyboard arrow nav can now be used to move the carousel  
* allow for zero opacity, just set opacity option to 1  
* autoplay only moves carousel when animations completed  
* circular is supported now, tho not infinite. see new option 'edgeReaction'  
* general code cleanup  
* updated jQuery to 1.6.1

Hope this satisfies everyone for now. I may be working on the Feature carousel next, but I may stick to this one for a bit more.
