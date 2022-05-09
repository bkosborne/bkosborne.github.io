+++
author = "Brian Osborne"
title = "Preventing Drupal 8 from applying image styles to GIFs to preserve animation"
date = 2016-12-09
tags = [
  "Drupal Planet",
]
+++

I'm working on a site where the editorial staff may occasionally produce animated GIFs and place them in an article. Image styles and animated GIFs in Drupal don't play nice out of the box. Drupal's standard image processing library, GD, does not preserve GIF animation when it processes them, so any image styles applied to the image will remove the animation. The ImageMagick image processing library is capable of preserving animation, but I believe the only way is to first coalesce the GIF which dramatically increases the output size which in unacceptable for this project (my sample 200kb GIF ballooned to nearly 2mb). For anyone interested in this approach anyway, the Drupal [ImageMagick](https://www.drupal.org/project/imagemagick) contrib module has a seemingly stable alpha release, but it would require a minor patch to get it working to retain animation. 

I'm mostly interested in somehow getting Drupal to just display the original image when it's a GIF to prevent this problem. On this site, images are stored in an image field that's part of an Image [Media Bundle](https://www.drupal.org/project/media_entity). This media bundle supports JPEGs and PNGs as well, and those are typically uploaded in high resolution and need to have image styles applied to them. So the challenge is to use the same media bundle and display mode for GIFs, JPEGs, and PNGs, but always display the original image when rendering a GIF.

After some digging and xdebugging, I created an implementation of [hook_entity_display_build_alter](https://api.drupal.org/api/drupal/core!lib!Drupal!Core!Entity!entity.api.php/function/hook_entity_display_build_alter/8.2.x) which lets you alter the render array used for displaying an entity in all view modes. I use this hook to *remove the image style* of the image being rendered.

    /**
     * Implements hook_entity_display_build_alter().
     */
    function my_module_entity_display_build_alter(&$build, $context) {
      $entity = $context['entity'];
    
      // Checks if the entity being displayed is a image media entity in the "full" display mode.
      // For other display modes it's OK for us to process the GIF and lose the animation.
      if ($entity->getEntityTypeId() == 'media' && $entity->bundle() == 'image' && $context['view_mode'] == 'full') {
        /** @var \Drupal\media_entity\Entity\Media $entity */
        if (isset($build['image'][0])) {
          $mimetype = $mimetype = $build['image'][0]['#item']->entity->filemime->value;
          $image_style = $build['image'][0]['#image_style'];
          if ($mimetype == 'image/gif' && !empty($image_style)) {
            $build['image'][0]['#image_style'] = '';
          }
        }
      }
    }

So now whatever image style I have configured for this display mode will still be applied to JPEGs and PNGs but will not be applied for GIFs.

**However, as a commenter pointed out, this would be better served as an image field formatter so you can configure it to be applied to any image field and display mode. I've created a [sandbox module](https://www.drupal.org/sandbox/bkosborne/2834511) that does just that. The code is even simpler than what I've added above.**
