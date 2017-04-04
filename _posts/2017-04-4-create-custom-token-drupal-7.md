---
layout: post
title: Create custom token Drupal 7
categories: ["drupal 7"]
tags: [drupal, "drupal 7"]
---

For creating custom token we will use three token api function.

* [hook_token_info](http://api.drupal.org/api/drupal/modules--system--system.api.php/function/hook_token_info/7)
* [hook_tokens($type, $tokens, array $data = array(), array $options = array())](http://api.drupal.org/api/drupal/modules--system--system.api.php/function/hook_tokens/7)
* [token_replace($text, array $data = array(), array $options = array())](http://api.drupal.org/api/drupal/includes--token.inc/function/token_replace/7)

<pre>
/**
 * @file
 * Provides special token to use with pathauto.
 *
 * @see httx://drupal.org/node/1308488
 */

/**
 * Provide information about our custom placeholder/token.
 *
 * @see httx://api.drupal.org/api/drupal/modules--system--system.api.php/function/hook_token_info/7
 * @see httx://api.lullabot.com/token_example_token_info/7
 * @return array
 *   An associative array of available tokens and token types.
 */
function MODULE_NAME_token_info() {
  $info['tokens']['node']['pathauto'] = array(
    'name' => t('Pathauto'),
    'description' => t('Title ready for use with pathauto.'),
  );
  return $info;
}

/**
 * Provide replacement values for placeholder tokens.
 *
 * @see httx://api.drupal.org/api/drupal/modules--system--system.api.php/function/hook_tokens/7
 * @see httx://api.lullabot.com/token_example_tokens/7
 * @param string $type
 *   The machine-readable name of the type (group) of token being replaced, such
 *   as 'node', 'user', or another type defined by a hook_token_info()
 *   implementation.
 * @param array $tokens
 *   An array of tokens to be replaced. The keys are the machine-readable token
 *   names, and the values are the raw [type:token] strings that appeared in the
 *   original text.
 * @param array $data (optional)
 *   An associative array of data objects to be used when generating replacement
 *   values, as supplied in the $data parameter to token_replace().
 * @param array $options (optional)
 *   An associative array of options for token replacement; see token_replace()
 *   for possible values.
 * @return array
 *   An associative array of replacement values, keyed by the raw [type:token]
 *   strings from the original text.
 */
function MODULE_NAME_tokens($type, $tokens, array $data = array(), array $options = array()) {
  $replacements = array();
  $sanitize = !empty($options['sanitize']);

  if ($type == 'node' && !empty($data['node'])) {
    $node = $data['node'];

    foreach ($tokens as $name => $original) {
      switch ($name) {
        case 'pathauto':
          $replacements[$original] = str_replace(
            array('&', '&'),
            'and',
            $sanitize ? filter_xss($node->title) : $node->title
          );
          break;
      }
    }
  }

  return $replacements;
</pre>

## References

* [https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_tokens/7.x](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_tokens/7.x)
