---
layout: post
title: Create a custom Twig filter in Drupal 8
comments: true
categories: [drupal 8]
tags: [drupal 8]
---

Twig can be explanded in many ways, you can add extra tags, filters, tests, oprators, global variables, and functions. You can event extend the parser itself with node visitors.

I am going to show you how to create new custom twig filter in drupal. For example we are going to create a filter to remove a numbers from string, will explain with `hello_world` module.

Create `hello_world` folder in `modules/custom/folder` with following files,

1. `hello_world.info.yml` it would contains normal module `.info.yml` file values. Check [here](https://www.drupal.org/node/2000204) for more detailes.
2. `hello_world.services.yml` it would contain following line

<pre>
services:
  hello_world.twig_extension:
    arguments: ['@renderer']
    class: Drupal\hello_world\TwigExtension\RemoveNumbers
    tags:
      - { name: twig.extension }
</pre>

3. `src/TwigExtension/RemoveNumbers.php` it would contain following in that.

<pre>
namespace Drupal\hello_world\TwigExtension; 
 
class RemoveNumbers extends \Twig_Extension {    
  /**
   * Generates a list of all Twig filters that this extension defines.
   */
  public function getFilters() {
    return [
      new \Twig_SimpleFilter('removenum', array($this, 'removeNumbers')),
    ];
  }
 
  /**
   * Gets a unique identifier for this Twig extension.
   */
  public function getName() {
    return 'hello_world.twig_extension';
  }
 
  /**
   * Replaces all numbers from the string.
   */
  public static function removeNumbers($string) {
    return preg_replace('#[0-9]*#', '', $string);
  }
}
</pre>

Enable `hello_world` module and clear the cache, then you could use the `removenum` filters in your twig file.

`{{ twig-value-with-numbers | removenum }}`

It would remove the all numbers from the string, enjoy with your custom filters!

## References
1. [http://leopathu.com/content/create-custom-twig-filter-drupal-8](http://leopathu.com/content/create-custom-twig-filter-drupal-8)
