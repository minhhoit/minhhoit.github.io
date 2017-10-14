---
layout: post
title: Queries in Drupal 8
comments: true
categories: [drupal 8]
tags: [drupal 8]
---
In this note i want to tell how to work with the queries to database in Drupal 8.
Below i have prepared serval examples how to get data of node through select queries.

# Select

## Get single value:

<pre>
<code>
$query = \Drupal::database()->select('node', 'n');
$query->addField('n', 'nid');
$query->condition('n.type', 'page');
$query->range(0, 10);
$nid = $query->execute()->fetchField();
</code>
</pre>

## Get single row

<pre>
<code>
$query = \Drupal::database()->select('node', 'n');
$query->addField('n', 'nid');
$query->condition('n.type', 'page');
$query->range(0, 10);
$nid = $query->execute()->fetchAssoc();
</code>
</pre>

## Using db like:

<pre>
<code>
$query = \Drupal::database()->select('node_field_data', 'nfd');
$query->addField('nfd', 'nid');
$query->condition('nfd.type', 'page');
$query->condition('nfd.title', $query->escapeLike('ca') . '%', 'LIKE');
$query->range(0, 10);
$nid = $query->execute()->fetchAllKeyed();
</code>
</pre>

## Get several rows with JOIN:
<pre>
<code>
$query = \Drupal::database()->select('node_field_data', 'nfd');
$query->fields('nfd', ['nid', 'title']);
$query->addField('ufd', 'name');
$query->join('users_field_data', 'ufd', 'ufd.uid = nfd.uid');
$query->condition('nfd.type', 'vegetable');
$vegetable = $query->execute()->fetchAllAssoc('nid');
</code>
</pre>

Below I have also prepared examples of queries for insert, update, upsert and delete. They can be useful for your custom tables. But think twice before use these queries for tables which are implemented by Drupal core or contrib modules. Because in drupal the most operations to add, update or delete of data should be done through objects and their methods but not through direct sql queries.

## Insert

<pre>
<code>
$query = \Drupal::database()->insert('flood');
$query->fields([
  'event',
  'identifier'
]);
$query->values([
  'My event',
  'My indentifier'
]);
$query->execute();
</code>
</pre>

You can call method values() several times to insert several rows at once.

## Update

<pre>
<code>
$query = \Drupal::database()->update('flood');
$query->fields([
  'identifier' => 'My new identifier'
]);

$query->condition('event', 'My event');
$query->execute();
</code>
</pre>
## Upsert

<pre>
<code>
$query = \Drupal::database()->upsert('flood');
$query->fields([
  'fid',
  'identifier',
]);
$query->values([
  1,
  'My indentifier for upsert'
]);
$query->key('fid');
$query->execute();
</code>
</pre>
Method key() have to be used to specify the name of field, which will be used to identify the existing row. This field should have unique values, otherwise the upsert operation will always work as insert .

## Delete

<pre>
<code>
$query = \Drupal::database()->delete('flood');
$query->condition('event', 'My event');
$query->execute();
</code>
</pre>
That's all. Maybe later I will add more examples here. Don't hesitate to ask for them.
