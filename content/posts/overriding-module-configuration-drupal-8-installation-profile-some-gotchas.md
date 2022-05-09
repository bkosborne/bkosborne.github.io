+++
author = "Brian Osborne"
title = 'Overriding module configuration in a Drupal 8 installation profile (with some "gotchas")'
date = 2018-03-23
tags = [
  "Drupal Planet",
]
+++

I'm working in creating a Drupal 8 installation profile and learning how they can override default configuration that its modules provide at install time.

All Drupal 8 modules can provide a set of configuration that should be installed to the site when the module is installed. This configuration is placed in the module's `config/install` or `config/optional` directory. The only difference is that the configuration objects placed in the `config/optional` directory will only be installed if all of their dependencies are met. For example, the core "media" module has a config file `config/optional/views.view.media.yml` which will install the standard media listings view, but only if the views module is available on your site at the time of install.

The power of installation profiles is that they can provide *overrides* for any configuration objects that a module would normally provide during its installation. This is accomplished simply by placing the config object file in the installation profile's `config/install` or `config/optional` directory. This works because when Drupal's `ConfigInstaller` is installing any configuration object, it checks to see if that config object exists in your installation profile, and uses that version of it if it exists.

However, overriding default configuration that a module would normally provide is a double edged sword and brings up some interesting challenges.

If you dramatically alter a configuration object that a module provides, what happens when that module releases a new version that includes an update hook to modify that config? The module maintainers may write the update hook assuming that the config object that's installed on your site is identical to the one that it provided out-of-the-box during install time. I think this falls on the module maintainer to write update hooks that first check to make sure that the config object is mostly what it expects it to be before modifying it. If not, fatal errors could be thrown.

Another challenge that I ran into recently is more complicated. My installation profile was overriding an [entity browser](https://www.drupal.org/project/entity_browser) config object provided by the [Lightning Media](https://drupal.org/project/lightning_media) module. Entity browsers use views to display lists of entities on your site that an editor can choose from. My override changed this config object to point to a custom view that my installation profile provided (placed in its `config/install` directory), but it didn't work. When installing a site with the profile, I was met with an UnmetDependenciesException which claimed that the entity browser override I provided depended on a view that didn't exist. Well, it *did* exist, it's right there in the install folder for the profile! After some debugging, this is happening because the Drupal doesn't install config from the installation profile until all of the modules your install profile depends are installed first. So to summarize, it's not possible for a module's default config objects to depend on config that is provided by an install profile.
