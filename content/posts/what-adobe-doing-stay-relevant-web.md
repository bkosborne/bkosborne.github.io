+++
author = "Brian Osborne"
title = "What Adobe is Doing to Stay Relevant on the Web"
date = 2012-01-27
+++

I recently attended a [PANMA](http://www.panma.org/) event in Philly focusing on HTML5 & Adobe. [Terry Ryan](http://www.terrenceryan.com/) from Adobe gave a stellar presentation on a few things the company is doing to stay relevant in the rapidly changing web. It's no secret that Flash as we know it is dying off. A few months ago, Apple officially said they will not support it on iOS devices, and whatever you may think about that, the general consensus is that Flash will not be a player for much longer. For quite a while, Adobe was reluctant to give in on the technology that represents such a large part of their company, but they finally switched from defensive mode to "hey, how can we adapt as a company?" mode.

Terry included four main topics in his 1.5 hour presentation:

1.  [TypeKit](http://typekit.com)
2.  [PhoneGap](http://phonegap.com)
3.  [Adobe Edge](http://labs.adobe.com/technologies/edge/)
4.  [CSS Shaders](http://www.adobe.com/devnet/html5/articles/css-shaders.html)

I won't get much into TypeKit because that technology has been around for quite a while. I've used it a few times and it's really great at allowing you to bring in non-traditional web fonts on your site. The service is great, cheap, works well, and developers should really consider using it or one of its many alternatives.

PhoneGap
---------

PhoneGap provides an easy way for web developers to create native apps without getting too involved in the specifics for each platform. As many know, there are really two types of "apps" that can be created. Web apps are mobile-optimized websites that are limited in their functionality on a device but often mimic the appearance and function of a native app. Native apps integrate directly with a mobile platform and require a developer program in the native language of the platform (like Objective-C for iOS), providing complete access to the phones native features.

What PhoneGap aims to do is make it as easy as possible to create native apps, while programming them as if they aren't. The service provides a great framework for writing apps in HTML5 & JavaScript (great for web developers), and then easily compiles your code into a native app. You chose what platforms you want to develop on, download some basic shell code, open up the IDE for the platform, and code away in what you know best: HTML and JavaScript. The code becomes portable and can be tranferred to the native "shell" code for each platform you want an app for. The big benfit of using PhoneGap is that you get access to all the features that only native apps get, like the Camera, GPS, accelerometer, and more. This is possible because PhoneGap handles communicating with the native device for you and exposes the functionality through their JavaScript API. It also works well with [jQuery Mobile](http://jquerymobile.com) & [Sencha Touch](http://www.sencha.com/products/touch) (which you'll likely want for the UI at the very least) and has a great plugins system.

Adobe did the right thing. Since acquiring PhoneGap's parent company, they are keeping the popular software open source, and have instead decided to try and monetize the software by offering a paid service to compliment it. This service, named "PhoneGap Build," automatically does all that cross-compiling on different platforms for you in the cloud. Terry did a quick demo of this... basically you can maintain all of your code in GitHub, push changes, and PhoneGap Build will pick up those changes and automatically work behind the scenes to output new app files for each of the platforms. I believe the service is actually free if your GitHub project is open source.

This software is really picking up speed and now has full support from Adobe. The developer community seems to be fairly strong, and the documentation is easy to understand and utilize. It's certainly work a look.

Adobe Edge
----------

Adobe Edge seems to be Adobe's answer to getting Flash-style animations in the browser... without Flash. There are a lot of things can be done with Flash that are very difficult or tedious to do using web technologies. A pro Flash developer can turn around advanced animations relatively quickly, whereas it would take ages to do the same animation using purely JavaScript and CSS by hand. Sencha has had software similar to Edge for a bit now ([Sencha Animator](http://www.sencha.com/products/animator/)), and now Adobe is stepping in with their own version.

Edge basically takes the best of what Flash is, drops what isn't great, and makes it work around HTML5 instead of Flash. That means you can do tweens, animations, key frames, etc... but instead of outputting to a SWF, you get HTML, JavaScript, and CSS. The software isn't \*quite\* there yet, but at least Adobe is headed in the right direction. Edge lets you do some really great things, and opens the door for web developers to get into animation, without having to learn a dying technology. While the 1.0 release isn't yet released, developers can toy around with the software for free at the moment.

CSS Shaders
-----------

Even with Edge, there is still a ton of stuff that can be done in Flash that cannot be done in a browser without plugins. Adobe aims to change that by pushing some of their IP into the browsers. CSS Shaders allow developers to achieve some really great visual effects in the browser and really push the limits of browsers. Terry showed us some live demos using Chromium (the "hot off the press" open source version of Chrome) and everyone in the lecture hall was pretty blown away. Check out [this page](http://www.adobe.com/devnet/html5/articles/css-shaders.html) for some of the demos he showed off. Don't get too excited, as these are still in development and haven't been adopted yet, but it's a neat look at the future potential of browsers.

\---

The web is rapidly evolving, with new web technologies emerging constantly. Adobe has really decided to embrace where the web is going and they are doing it right. PhoneGap makes creating native apps easier than ever, Edge provides an outlet to Flash professionals to get into a new emerging technology, and shaders allow for great visual effects right in the browser without a third party plugin like Flash. Time will tell to see how Adobe's new investments and projects will pay off, but they seem to be headed in the right direction.
