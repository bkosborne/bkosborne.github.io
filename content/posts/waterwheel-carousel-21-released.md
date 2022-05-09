+++
author = "Brian Osborne"
title = "Waterwheel Carousel 2.1 released"
date = 2013-03-03
+++

Managed to get out three updates to the Waterwheel Carousel this weekend. I'm working towards getting this carousel to replace the Feature Carousel - it doesn't make sense for me to maintain both of them when they are already fairly similar.

Changes:
*   Bug fix to the image preloader - hopefully this fixes the issues with IE
*   The images can now move to a hidden position behind the center, instead of fading to nothingness at the edge. This is now enabled by default, but can be turned off
*   Images of different proportions can be added now

Thanks to some refactoring in 2.0, these changes were relatively easy. In the next update, the plan is to add caption div support. It's a little more complicated than I'd like - but once that is done, I think I can officially deprecate the Feature Carousel and recommend this one instead.