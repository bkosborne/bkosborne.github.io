+++
author = "Brian Osborne"
title = "HTML5 - What a Project Manager Should Know"
date = 2011-11-29
+++

HTML5 has become quite the buzz word lately, and it's important for people in both developer roles and project management roles to understand what it really is. The term has been around long enough that clients are aware of it, and may begin to ask "so how will you incorporate HTML5 into our website?" Not knowing how to answer this could be disastrous when attempting to gain the trust of a potential client.

I'm going to start with what I feel is the biggest problem. A quick Google search of "HTML5 websites" reveals endless lists of _really neat_ sites that apparently utilize HTML5. The reality is nearly all of these websites are barely using HTML5, or not even at all! In fact, Apple has a [dedicated page](http://www.apple.com/html5/) with a showcase of HTML5 examples. The problem? Only two of the examples are actually using HTML5 - the Video and Audio examples. The rest are a combinations of CSS3 and JavaScript. However, Apple in it's infinte wisdom has mislead visitors into believing that with the power of HTML5, you can do all of the neat things in the examples. Everything seen in those examples can be done without _any_ HTML5 at all.

So what is HTML5 then? For the most part, HTML5 adds more tools to the toolbox for _developers_. I feel the most important addition of HTML5 are the new semantic markup elements. For years and years, developers have been using `<div>` elements to define the structure of their website (and before that, horrendous tables). A problem with doing this is that there is no true semantic value in throwing hundreds of div tags on a website. How does Google know where your site's main navigation is? How about where the caption for a picture is? Is this page a listing of a bunch of news items? HTML5 introduces really helpful elements like `<nav>`, `<header>`, and `<article>` that help enrich the semantic value of a web page, which will eventually provide SEO benefits.

In addition, there are the new `<video>` and `<audio>` tags which help serve multimedia content across multiple platforms (especially mobile) in a very easy way. The controls for the player can be easily styled using CSS, and developers no longer have to worry about getting a decent flash player, customizing it, and making sure there's a fallback for mobile devices.

_Most_ of the additions and changes HTML5 bring should not impact how a website looks or functions. With that said, I feel I must mention the new `<canvas>` element. This literally allows developers to "draw" and animate objects on a canvas on a web page. Many people have seen what Google and Arcade Fire [teamed up to create](http://thewildernessdowntown.com/) last year. Those small flying birds that respond to your mouse movement utilize the canvas element and loads of JavaScript... but like most HTML5 "examples", most of what is done on that website is really just JavaScript.

There are some other nice enhancements that come along with HTML5, but these mostly benefit developers and shouldn't be a concern of clients or managment.

**What you should know as a project manager:**  
HTML5 is not completely embraced yet. It's certainly getting there, but the W3C isn't expected to officially recommend it's use for several more years. Every major browser supports it, but users typically have to have the latest version of the browser. Internet Explorer 8 and down do not even recognize the new HTML5 tags as existing, and JavaScript must be injected to trick the browser into seeing them. IE8 and below represents a very sizable chunk of web traffic, and will for quite some time. For this reason, any developer that utilizes HTML5 elements needs to ensure that there are graceful fallback solutions in place to handle these older browsers.

Most of what can be done in HTML5 can be done without it, and most of what people think is HTML5 is actually not. Really neat things can be done with CSS3 (like box shadows and curved borders) and developers and designers are becoming more and more creative in how they build sites. However, it's important to know the distinction between HTML5, CSS, and JavaScript.

For the curious, here's a nice list of the major features of HTML5: [http://net.tutsplus.com/tutorials/html-css-techniques/25-html5-features-tips-and-techniques-you-must-know/](http://net.tutsplus.com/tutorials/html-css-techniques/25-html5-features-tips-and-techniques-you-must-know/)

And here is a great article that goes much more into detail of what HTML5 really is: [http://radar.oreilly.com/2011/07/what-is-html5.html](http://radar.oreilly.com/2011/07/what-is-html5.html)