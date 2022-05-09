+++
author = "Brian Osborne"
title = "Installing ruby-debug in Ruby 1.9.3"
date = 2012-03-06
+++

**Update:** You can now use the `debugger` gem to circumvent this issue. Or better yet, use `pry`

I've been working in a development environment that required an earlier verison of Ruby (1.8.7) that worked well with the [ruby-debug](http://rubygems.org/gems/ruby-debug) gem. This gem is widly used and has some nice "get to know it" text in [the rails guides](http://guides.rubyonrails.org/debugging_rails_applications.html).

However, as I moved to a better environment to develop some playground applications with the latest and greatest Ruby and Rails, I ran into a snag when attempting to use ruby-debug.

As is turns out, the original ruby-debug gem is not compatible with Ruby 1.9.x, and you must instead use ruby-debug19. However, ruby-debug19's dependencies are not quite up to speed on 1.9.3 quite yet and you need to perform a workaround.

This [stack overflow question](http://stackoverflow.com/questions/8087610/ruby-debug-with-ruby-1-9-3) and it's top answer hold the solution (mostly). The problem is that the gems files of two dependencies of ruby-debug19, linecache19 and ruby-debug-base19, aren't yet compatible with 1.9.3, at least not their current public facing versions. You have to download and install the latest version from rubyforge manually.

Here are the steps:

1.  Install **ruby-debug19** (gem install ruby-debug19). This will also download & install its dependencies, but you'll need to download updated versions of two of them first.
2.  Download **linecache19-0.5.13.gem** and **ruby-debug-base19-0.11.26.gem** from the URL [listed here](https://raw.github.com/gist/1333785)
3.  Install both of those gems (gem install linecache19-0.5.13.gem ruby-debug-base19-0.11.26.gem -- --with-ruby-include=$rvm\_path/src/ruby-1.9.3-p0/) BUT MAKE SURE TO REPLACE THE RUBY PATCH LEVEL WITH YOURS. See your exact version with \`ruby -v\`. The original bash commands were created when 1.9.3 first came out, but it has since received several patch releases
4.  You should now have two versions of linecache19 and ruby-debug-base19 installed and ready for use.
5.  Now you can include the 'ruby-debug19' gem in your project's Gemfile:  
    gem 'ruby-debug19', :require => 'ruby-debug'
6.  Make sure to use \`bundle install\` or \`bundle update\` to refresh Gemfile.lock to use the proper, latest versions of our two Gemfile dependencies.

That should be it! To make sure it's working, try starting Mongrel with \`rails s --debugger\`. It should let you know that the server was started with debugging capabilities.

If I missed anything important or misinformed, please let me know in the comments.