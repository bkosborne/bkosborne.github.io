+++
author = "Brian Osborne"
title = "Modifying FAPI output with Drupal 7's renderable arrays"
date = 2012-01-24
tags = [
  "Drupal Planet",
]
+++

I've been working on a project that requires the search block look a very specific way. The text input and submit button needed to be directly next to one another. I could probably get it done with just CSS, but all the extra markup in the search block was really bothering me.

I've heard great things about Drupal 7's new rendering system, but it was pretty mysterious to me. I remember attending a session about it @ DrupalCon Chicago but it was a bit over my head at the time. Now that I had to mess around with this search block, it was a great opportunity to research the drupal\_render(). FAPI adds what are called theme\_wrapper functions to every element before they get rendered. These are like "mini" theme functions that are applied, in order, to each element they are attached to.

For the search block, there are two elements that I wanted to remove extra, unnecessary markup from. The search input field is one, and has a theme\_wrapper function called "form\_element". When drupal\_render renders this form, it will see that this form element has a theme\_wrapper and pass the element data into it for theming. This is when the extra markup around the form element is added. All you have to do is clear out the theme\_wrapper before rendering to prevent that from happening. The same applies for the submit button.

But where do we clear it out? Turns out this _can't_ be done in a form\_alter. That's because the theme\_wrappers are not added by the time your module gets it's hands on the form. Thanks to Drupal 7's new rendering, you can use a [hook\_block\_view\_alter](http://api.drupal.org/api/drupal/modules--block--block.api.php/function/hook_block_view_alter/7) to unset the theme\_wrapper functions. Since a blocks $content variable is populated with a renderable array and not static content (like it was in Drupal 6!), we can modify with the fully populated form array using this hook. What's even greater is that you can use this hook in your theme's template.php file, which I don't believe was possible in Drupal 6.

Here's the code:

    function YOURTHEMENAME_block_view_alter(&$data, $block) {
      if ($block->module == 'search' && $block->bid == 'search-form') {
        unset($data['content']['actions']['#theme_wrappers']);
        unset($data['content']['search_block_form']['#theme_wrappers']);
      }
    }

And there you go. Now the extra markup around the form elements will be removed.