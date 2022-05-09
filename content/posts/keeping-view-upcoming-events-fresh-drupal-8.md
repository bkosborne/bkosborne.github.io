+++
author = "Brian Osborne"
title = "Keeping a view of upcoming events fresh in Drupal 8"
date = 2017-05-18
tags = [
  "Drupal Planet",
]
+++

Imagine you have a view that lists upcoming events on your Drupal 8 site. There's a date filter that filters out any event who's start date is less than the current date. This works great until you realize that the output of the view will be cached in one or many places (dynamic page cache, internal page cache, varnish, etc). Once it's cached, views doesn't execute the query and can't compare the date to the current time, so you may get older events sticking around.

One way of fixing this is to [assign a custom cache tag](https://www.drupal.org/project/views_custom_cache_tag) to your view, and then run a cron task that purges that cache tag at least once a day, like so:

```
/**
 * Implements hook_cron().
 */
function YOUR_MODULE_cron() {
  // Invalidate the events view cache tag if we haven't done so today.
  // This is done so that the events list always shows the proper "start"
  // date of today when it's rendered. If we didn't do this, then it's possible
  // that events from previous days could be shown.
  // This relies on us setting a custom cache tag "public_events_block" on the
  // view that lists the events via the views_custom_cache_tag module.
  $state_key = 'events_view_last_cleared';
  $last_cleared = \Drupal::state()->get($state_key);
  $today = date('Y-m-d');
  if ($last_cleared != $today) {
    \Drupal::state()->set($state_key, $today);
    \Drupal::service('cache_tags.invalidator')->invalidateTags(['public_events_block']);
  }
}
```

Assuming you have cron running just after midnight, this will refresh the cache of the view's block and the page at an appropriate time so that events from the previous day are not shown.