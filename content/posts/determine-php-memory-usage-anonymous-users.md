+++
author = "Brian Osborne"
title = "Determine PHP memory usage for anonymous users"
date = 2012-02-16
tags = [
  "Drupal Planet",
]
+++

In case you've been living under a rock for the past two years: Drupal 7 is known to use quite a bit of PHP memory everytime a page is loaded. I won't get into anything why that is and why it's such a big jump from Drupal 6 (I'm in no position to comment on that), but what you need to know is that Drupal 7 can easily use 40-50 MB per page load for a small to mid size website, and much much more for larger websites. Turn on some sort of PHP opcode cache like APC and you can probably get that small to mid site down to 10MB of usage or lower. With this number in mind, as well as an idea of how much traffic your site will see per day, you can come up with a ballpark server specification that will handle your website.

How do I know that? The Devel module of course! There's an option in the settings for Devel that will report to you (at the very bottom of your page) the total memory that Drupal used from bootstrap until the page was completely built and served to the browser. If you've never done this before, go try it out for yourself. There are also a bunch of other performance reporting options in there that are worth playing around with as well. 

But let's get back to the memory usage itself... that 10MB per page load I was talking about actually only relates to _authenticated_ users on your Drupal site. It's most likely that the vast majority of your visitors will be anonymous, non logged in users. This is very important to understand. If you are building a content-driven website with no community portal or user login area, that 10MB is not an accurate representation of the memory usage of your website... What you really want to know is how much memory Drupal uses when serving pages for _anonymous_ users.

Assuming you have anonymous page caching turned on (you do, _right_?), Drupal will use far less memory and system resources to build page requests for anonymous users than it does for authenticated users. Cool, so let's just go to the Devel permissions and turn give anonymous users access to see devel info.

Nope. For reasons I don't completely understand at the moment (I'm not an expert on Drupal page caching), the devel summary information is not printed out for cached pages. So I did some exploring and found the function in devel.module that gathers the memory usage and prints it out. Turns out the code is still executed, but the text it generates is not printed out. For Drupal 7, the code is in the function "devel\_shutdown\_memory" on the file devel.module. This is a sort of helper function that is called when Drupal is just about completely done doing it's thing, and all it's code has been processed and memory has been used. Here's that function:

    function devel_shutdown_memory() {
      global $memory_init;

      if (variable_get('dev_mem', FALSE)) {
        $memory_shutdown = memory_get_usage();
        $args = array('@memory_boot' =&gt; round($memory_init / 1024 / 1024, 2), '@memory_shutdown' =&gt; round($memory_shutdown / 1024 / 1024, 2), '@memory_peak' =&gt; round(memory_get_peak_usage(TRUE) / 1024 / 1024, 2));
        $msg = ' Memory used at: devel_boot()=<strong>@memory_boot</strong> MB, devel_shutdown()=<strong>@memory_shutdown</strong> MB, PHP peak=<strong>@memory_peak</strong> MB.';
        // theme() may not be available. not t() either.
        return t_safe($msg, $args);
      }
    }

**This function is still called for anonymous page views**, so this is where we will put our dirty hack. That return statement sends off the built string with the memory usage in it which eventually gets printed out; but like I said, this is not visible for cached pages. Instead, lets write the memory shutdown variable the PHP error log. This is just a temporary adjustment to the code so we can get reports of how much memory Drupal is using. Once we get what info we need, we can just remove the error\_log line.

Add this just before the return statement:

```
error_log(round($memory_shutdown / 1024 / 1024, 2));
```

Then you can tail your error log as you browse your website as an anonymous user. Now you'll be able to see the memory usage of anonymous page visits on various pages of your site.

I wrote this blog post because over the summer I was unable to find any information on how to easily find memory usage for anonymous page views, and I was in a situation where I needed to know that. Working on a site where memory usage for logged in users (with APC) was reaching 30MB, I was happy to find out that anonymous users were only using 4-6MB. This allowed me to tweak Apache's settings and find a good cloud hosting environment for the website.
