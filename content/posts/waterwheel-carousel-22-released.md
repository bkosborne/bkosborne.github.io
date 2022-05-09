+++
author = "Brian Osborne"
title = "Waterwheel Carousel 2.2 released"
date = 2013-03-11
+++

This is a small release. A lot of people have been having trouble with the carousel not loading properly, and I'm sure this is because of the image preloader. The carousel has always worked by first attempting to forcefully load all of the images before initializing. This is a well known difficulty within the javascript community. I thought I had a pretty solid preloader finally figured out, but alas people continue to have issues with it.

Rather than fight the constant new browser releases and possible different handling of image loading, I've added an option to disable it completely and instead force a width and height for the images. The main point of preloading the images is so that I can work with the actual dimensions of the images. Since most people use the same size images, there's no need for this really. The image dimensions can be added using CSS for each image, or they can be specified globally with the new config options `forcedImageWidth` and `forcedImageHeight`. The global specification obviously won't work if you have images that are different dimensions. In that case, don't use these options and define the dimensions for each image using CSS.

There was also a small bugfix for autoplay where the carousel would continue to rotate even after user interaction should have caused it to stop.