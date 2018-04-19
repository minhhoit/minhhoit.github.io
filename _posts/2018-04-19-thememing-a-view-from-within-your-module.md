---
layout: post
title: Theming a view from within your module
comments: true
categories: ["drupal 7"]
tags: [drupal, "drupal 7"]
---

The views module has a number of template files (.tpl.php files) which determine how each field will be output, how each row will be output and how the overall display will be output. If you want to customize any of these template files, or make changes to the variables available to template files through a preprocess function, then you will have to theme your view. You can do this within a custom theme, but if your module is creating a view, you might want the module to have control over how that view will look. This helps keep everything related to your module nicely packaged together. A custom theme can still override the theming provided by your module. (To override a template file from within a theme, simply drop the template file into the theme folder and Drupal will find and use it. You can also put a preprocess function in the theme's template.php file.) To theme a view from within your module you can follow these steps:

## Determine which template file you want to use and create it.

Go to your view's UI and click on 'Theme: information' under 'Basic settings'. This lists all of the possible template files which might be used. There is a list of possible templates for each level of output – display (overall view), row and individual fields – as well as one for the style. In your module (in a folder named 'templates' if you wish) create a file with the exact name of one of the suggested templates. (You can copy and paste directly, dashes remain as dashes.)

## Register the template and preprocess functions using hook_theme().

In your [module_name].module file, use the following hook_theme(). This will let Drupal know about any template files and preprocess functions that your module is implementing.

<pre>
/**
 * Implementation of hook_theme().
 */
function mymodule_theme($existing, $type, $theme, $path) { 
 
  // Replace each dash with an underscore in the hook name.
  $themes['views_view_fields__myview__page_1'] = array(
    // Drop the .tpl.php extension on the template name here.
    'template'  => 'templates/views-view-fields—myview--page-1',
    // The original hook must be declared.
    // This will be the first one on the list in views UI Theme:information page.
    'original hook' => 'views_view_fields',
    // You need this whether you declare your own preprocess function or not.
    // Always list the first two and optionally list your own function last.
    'preprocess functions' => array(
      // This is the global preprocess function used by all templates.
      'template_preprocess',
      // This one is specific to the type of template, 
      // named 'template_preprocess_' followed by 'original hook' name.
      'template_preprocess_views_view_fields',
      // This one will be defined in mymodule.
      'mymodule_preprocess_views_view_fields__myview__page_1',
    ),
    // Arguments are specific to the type of template.
    // These are the arguments for row style output.
    'arguments' => array('view' => NULL, 'options' => NULL, 'row' => NULL),
  );

  // This theme hook is for a display output type template.
  // It does not register a custom preprocess function.
  // Note the arguments array for this template type, different from above.  
  $themes['views_view__myview__block_4'] = array(
    'template'  => 'templates/views-view--myview--block-4',
    'original hook' => 'views_view',
    'preprocess functions' => array(
      'template_preprocess',
      'template_preprocess_views_view',
    ),
    'arguments' => array('view' => NULL),
  );
  
  return $themes;
}
</pre>

To check whether you have done this step correctly, go back to your view's UI and click on 'Theme: information' again. Click on 'Rescan template files' (bottom of page). Your registered template file should now appear in bold red. It will also say (File not found, in folder ./) which you can ignore.

## Create a preprocess function if you need one.

If your theme hook registered a custom preprocess function, then create the function in your [module_name].module file. From the example above, we would need:

<pre>
  function mymodule_preprocess_views_view_fields__myview__page_1(&$vars) {

    // See your view's UI Theme:information page for field names.
    // Or use dpm() (need devel module) or var_dump() 
    // to see what's available in the $vars array.
    dpm($vars);
    $my_var = $vars['fields']['field_my_field']->content;

    // Expose whatever custom variables you want to your template file.
    $vars['new_var'] = 'Some text ' . $my_var; 
    
  }
</pre>

## References

* [https://www.appnovation.com/blog/theming-view-within-your-module](https://www.appnovation.com/blog/theming-view-within-your-module)
