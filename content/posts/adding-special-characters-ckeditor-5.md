+++
author = "Brian Osborne"
title = "Adding Additional Special Characters to Drupal's CKEditor 5 Module"
date = 2024-03-28
tags = [
"Drupal Planet",
]
+++

CKEditor 5 has a plugin called [Special Characters](https://ckeditor.com/docs/ckeditor5/latest/features/special-characters.html)
that Drupal's CKEditor 5 module makes available to use. The plugin provides a toolbar button that can be used
to insert characters that may not be easy to type manually using a keyboard, especially for people who are not used
to using them frequently.

It comes with a list of default special characters, organized by category. One such category is for
Latin characters and includes characters such as Ā, Ć, and Ē. However, it lacks some other
characters that were found in the same plugin for CKEditor 4 such as Á, Ç, and É.  

I'm not sure why the same character set from the CKEditor 4 plugin was not included in CKEditor 5.
Thankfully, the plugin provides an easy way to extend the list of available special characters:

```javascript
import { SpecialCharacters, SpecialCharactersEssentials } from '@ckeditor/ckeditor5-special-characters';

function SpecialCharactersExtended( editor ) {
    editor.plugins.get( 'SpecialCharacters' ).addItems( 'Mathematical', [
        { title: 'alpha', character: 'α' },
        { title: 'beta', character: 'β' },
        { title: 'gamma', character: 'γ' }
    ] );
}

ClassicEditor
    .create( document.querySelector( '#editor' ), {
        plugins: [
            SpecialCharacters, SpecialCharactersEssentials, SpecialCharactersExtended,

            // More plugins.
            // ...
        ],
        toolbar: [ 'specialCharacters', /* ... */ ],
    } )
    .then( /* ... */ )
    .catch( /* ... */ );
```

## Drupal Integration

The code above assumes you have access to the JS code when the CKEditor 5 instance is created.
Drupal's module does all this for us, and while there's an alter hook you can use to modify the
editor configuration data, creating a simple custom plugin may be the best solution.

Drupal has a robust framework for creating custom CKEditor 5 plugins and [there's a module called CKEditor 5 Dev](https://www.drupal.org/project/ckeditor5_dev) to help people get started.

Use the plugin starter kit example module from that module to get started, which will walk
you through the necessary configuration files to register a CKE5 plugin.

Once you have the sample plugin working and being recognized by Drupal (to get comfortable with
how it all works), you can create a new plugin for adding additional special characters.

Create a file in your module called `js/ckeditor5_plugins/additionalSpecialCharacters/src/index.js` with
the following contents:

```javascript
import { Plugin } from "ckeditor5/src/core";

/**
 * Registers additional special characters.
 */
class AdditionalSpecialCharacters extends Plugin {
  /**
   * @inheritDoc
   */
  static get pluginName() {
    return 'AdditionalSpecialCharacters';
  }

  /**
   * @inheritDoc
   */
  init() {
    if (this.editor.plugins.has('SpecialCharacters')) {
      this.editor.plugins.get('SpecialCharacters').addItems( 'Latin', [
        {character: "Á", title: "Latin capital letter a with acute accent"},
        {character: "á", title: "Latin small letter a with acute accent"},
        {character: "À", title: "Latin capital letter a with grave accent"},
        {character: "à", title: "Latin small letter a with grave accent"},
        {character: "Â", title: "Latin capital letter a with circumflex"},
        {character: "â", title: "Latin small letter a with circumflex"},
        {character: "Ä", title: "Latin capital letter a with diaeresis"},
        {character: "ä", title: "Latin small letter a with diaeresis"},
        {character: "Å", title: "Latin capital letter a with ring above"},
        {character: "å", title: "Latin small letter a with ring above"},
        {character: "Æ", title: "Latin capital ligature ae"},
        {character: "æ", title: "Latin small ligature ae"},
        {character: "Ç", title: "Latin capital letter c with cedilla"},
        {character: "ç", title: "Latin small letter c with cedilla"},
        {character: "Ð", title: "Latin capital letter eth"},
        {character: "ð", title: "Latin small letter eth"},
        {character: "É", title: "Latin capital letter e with acute accent"},
        {character: "é", title: "Latin small letter e with acute accent"},
        {character: "È", title: "Latin capital letter e with grave accent"},
        {character: "è", title: "Latin small letter e with grave accent"},
        {character: "Ê", title: "Latin capital letter e with circumflex"},
        {character: "ê", title: "Latin small letter e with circumflex"},
        {character: "Ë", title: "Latin capital letter e with diaeresis"},
        {character: "ë", title: "Latin small letter e with diaeresis"},
        {character: "Í", title: "Latin capital letter i with acute accent"},
        {character: "í", title: "Latin small letter i with acute accent"},
        {character: "Ì", title: "Latin capital letter i with grave accent"},
        {character: "ì", title: "Latin small letter i with grave accent"},
        {character: "Î", title: "Latin capital letter i with circumflex"},
        {character: "î", title: "Latin small letter i with circumflex"},
        {character: "Ï", title: "Latin capital letter i with diaeresis"},
        {character: "ï", title: "Latin small letter i with diaeresis"},
        {character: "Ñ", title: "Latin capital letter n with tilde"},
        {character: "ñ", title: "Latin small letter n with tilde"},
        {character: "Ó", title: "Latin capital letter o with acute accent"},
        {character: "ó", title: "Latin small letter o with acute accent"},
        {character: "Ò", title: "Latin capital letter o with grave accent"},
        {character: "ò", title: "Latin small letter o with grave accent"},
        {character: "Ô", title: "Latin capital letter o with circumflex"},
        {character: "ô", title: "Latin small letter o with circumflex"},
        {character: "Ö", title: "Latin capital letter o with diaeresis"},
        {character: "ö", title: "Latin small letter o with diaeresis"},
        {character: "Ø", title: "Latin capital letter o with stroke"},
        {character: "ø", title: "Latin small letter o with stroke"},
        {character: "Ú", title: "Latin capital letter u with acute accent"},
        {character: "ú", title: "Latin small letter u with acute accent"},
        {character: "Ù", title: "Latin capital letter u with grave accent"},
        {character: "ù", title: "Latin small letter u with grave accent"},
        {character: "Û", title: "Latin capital letter u with circumflex"},
        {character: "û", title: "Latin small letter u with circumflex"},
        {character: "Ü", title: "Latin capital letter u with diaeresis"},
        {character: "ü", title: "Latin small letter u with diaeresis"},
        {character: "Ý", title: "Latin capital letter y with acute accent"},
        {character: "ý", title: "Latin small letter y with acute accent"},
        {character: "Þ", title: "Latin capital letter thorn"},
        {character: "þ", title: "Latin small letter thorn"},
        {character: "ß", title: "Latin small letter sharp s"},
      ] );
    }
  }
}

export default {
  AdditionalSpecialCharacters
}
```

Now run `yarn build` from your module's directory to have the `js/build/additionalSpecialCharacters.js`
file created by webpack.

Register a Drupal library for the JS file in your module's `*.libraries.yml` file:

```
ckeditor5_additional_special_characters:
  js:
    js/build/additionalSpecialCharacters.js: { minified: true }
  dependencies:
    - core/ckeditor5
```

And finally, register it as a CKEditor 5 plugin. Doing this will have the plugin automatically included
in all CKEditor 5 instances on your site. Edit your modules `*.ckeditor5.yml` file:

```
ps_core_additional_special_characters:
  ckeditor5:
    plugins:
      - additionalSpecialCharacters.AdditionalSpecialCharacters
  drupal:
    label: Additional Special Characters
    library: your_module_name/ckeditor5_additional_special_characters
    conditions: []
    elements: false
```

After clearing caches, the plugin should be loaded, and you should see the additional set of Latin
characters in the special characters drop-down (assuming your editor is configured with the
Special Characters plugin, of course).

## Going Further
Someone could take this further and make a Drupal UI for configuring the list of extra special
characters. Drupal's CKEditor 5 plugin framework has the tools available to create
such a plugin configuration form. This would prevent the need to hard-code the list of additional
characters in the plugin file.
