+++
author = "Brian Osborne"
title = "Why You Should Use A 3rd Party Comment Service"
date = 2011-12-02
tags = [
  "Drupal Planet",
]
+++

Drupal is a wonderful content management system. Any Drupal web developer will tell you that there seems to be a module for anything you can think of. For the most part, this is what makes Drupal so great. The community support is second to none and it's never too hard to find a module that suites your needs.

With that said, it's important to understand when there may be something _other_ than Drupal that you should be using for a particular functionality. Commenting is the best example of this.

Drupal has a comment module included in core that provides all of the standard features of commenting. You can restrict what content types allow commenting, control threading and anonymous commenting, and more. I don't think anyone would argue that the comment module is by any means useless or completely feature-lacking.

That said, I think it can be done better. The main issue with using a Drupal's (or any other CMS's) comment implementation is that users either have to register to comment, or comment anonymously. Commenting anonymously has a bunch of issues associated with it, one of which is that users often forget that they commented and won't come back to continue a conversation.

Sites that don't allow anonymous commenting will force users to register with a local account. If I want to comment on a random article I found on the web, and I have to register to leave a comment, I'm out of there. Users don't want to register with yet another website, remember the password, and give out their email address. And that's how it should be, right? There are a ton of websites that offer logging in using your Twitter or Facebook account as an alternative to registering separately - because no one wants another account to remember.

This is especially true if the only benefit of registration is the ability to leave comments. I feel it's an older way of approaching SPAM control and perhaps gathering email addresses for marketing. Modern SPAM prevention software and algorithms have greatly reduced the need to force user registration, and most users probably won't opt-in to marketing during registration.

There are a few services out there that do comments _really well_ that should be a serious consideration when starting up a new site. These services fix the above issue by allowing users to have one account that they use for commenting all over the web. Two big ones are [LiveFyre](http://livefyre.com) and [IntenseDebate](http://intensedebate.com). But the one I'm going to focus on is [Disqus](http://disqus.com).

Disqus is a proven service used by sites like CNN, IGN, and Engadget. At the time of writing, there are 58 _million_ ​users and over 1 million sites using the service. It's super easy to install on any website, and there is a [Drupal module](http://drupal.org/project/disqus) that provides integration with the service as well. Here's a few of the main features of Disqus:

*   One profile for all commenting - create one account and use it on every site that uses Disqus
*   Social integration - create a Disqus profile with one of your existing social site logins, and share comments with them
*   Liking - users (even anonymous ones) can "like" comments and you can then sort on the most liked comments
*   Spam - Disqus has an incredible Spam prevention engine
*   Moderation - login to Disqus' great control panel to moderate comments

There are a lot more features, but the idea here is that Disqus does _only commenting,_ whereas Drupal does not. As a result, Disqus has refined their offerings to provide the best possible commenting experience.

There is one major caveat worth mentioning though. Disqus stores the comments on _their_ server, unless you are a huge company that runs their own Disqus servers. This is a reason for concern for two reasons:

1.  You're tied to Disqus - when they go down, so do your comments.
2.  Comments are brought in dynamically via JavaScript - you don't get the SEO benefits of the comments.

The first problem shouldn't be that big of a deal. Disqus has a very solid infrastructure and they've been around for years. If you're worried about the comments being on Disqus forever, they let you export all of the comments via XML should you decided to leave the service.

The second problem is the big one. A lot of sites are hesitant to use the service because they lose the SEO benefits of serving comments server-side. Google will never see the comments when using Disqus, unless you use a synchronization feature. Such a feature uses the API to periodically bring comments from Disqus back into your local database. You could then show the comments server-side and just hide them using CSS.

The WordPress plugin already supports this. I've recently become a maintainer of the Disqus module for Drupal, and have been working on a similar feature for Drupal. Once that's completed, Drupal developers (including myself) will be more open to using it.

I haven't tried out LiveFyre or IntenseDebate, so they may be better for you depending on your situation. I've taken an interest in Disqus because I see it all over the web, and they have a great API that's getting bigger and more powerful. The point is, if you run a blog or news site, you want it to be as easy as possible for your users to get involved. Forcing users to register on your site they may only use a few times a year is not the correct way to go about it.
