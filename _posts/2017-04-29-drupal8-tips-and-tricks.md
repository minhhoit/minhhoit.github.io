---
layout: post
title: Drupal 8 Tips and Tricks
comments: true
categories: [drupal 8]
tags: [drupal 8]
---

## Block

### Disable the cache

You can control the cache in the render array with the #cache element.
<pre>
$build['#cache']['max-age'] = 0;
</pre>

In this case the block will not be cached for logged in users or anonymous users with a session.
