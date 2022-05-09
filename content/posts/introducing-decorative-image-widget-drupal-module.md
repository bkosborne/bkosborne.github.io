+++
author = "Brian Osborne"
title = "Introducing the Decorative Image Widget Drupal module"
date = 2021-01-16
tags = [
  "Drupal Planet",
]
+++

## Drupal's Problem With Decorative Images
[Decorative images](https://www.w3.org/WAI/tutorials/images/decorative/) are those that should not be described to assistive technology like screen readers. A simple example is a fancy image of a decorative border that you insert at the end of a news article.

Supporting decorative images in Drupal is quite straight forward. Images fields can be set to collect alternative text but not require it. When left blank, Drupal will output an empty alt attribute for the image element, indicating to screen readers the image should be ignored. Editors that want to convey that an image is decorative can then simply leave the alt text blank.

The problem with this approach is that it might encourage editors to leave alt text blank even if the image is not really a decorative. It's easy to pass right by the alt text field without even thinking about it. This is especially true for those that are completely unfamiliar with how important alternative text is for accessibility.

There's a small effort underway to [resolve this problem in Drupal core](https://www.drupal.org/project/drupal/issues/2934405). After some great discussions, particularly with [Andrew Macpherson](https://www.drupal.org/u/andrewmacpherson), a leading voice in Drupal accessibility, we landed on a potential solution (thanks Andrew!).

## A Potential Solution

Instead of simply leaving the alt text field blank to the image is decorative, a checkbox labeled "Decorative" is introduced beneath the field. This checkbox *must be checked* if the editor wants to leave the alt text blank. This effectively makes the choice *explicit* rather than *implicit*, forcing the editor to pause for a second and consciously affirm that the image is truly decorative.

To let this solution incubate and evolve a bit, I contributed it as small module called [Decorative Image Widget](https://drupal.org/project/decorative_image_widget). It's quite flexible as instead of providing a brand new image widget, it modifies any image widget that is based on the default one provided by Drupal core.

Check it out and please provide feedback in the module's issue queue!