---
layout: post
title: Node API Hooks
comments: true
categories: ["drupal 7"]
tags: ["drupal 7", "php"]
---

### Node API Function
Functions to define and modify content types.

Each content type is maintained by a primary module, which is either node.module (for content types created in the user interface) or the module that implements hook_node_info() to define the content type.

During node operations (create, update, view, delete, etc.), there are several sets of hooks that get invoked to allow modules to modify the base node operation:

- *Node-type-specific hooks:* When defining a node type, hook_node_info() returns a 'base' component. Node-type-specific hooks are named base_hookname() instead of mymodule_hookname() (in a module called 'mymodule' for example). Only the node type's corresponding implementation is invoked. For example, poll_node_info() in poll.module defines the base for the 'poll' node type as 'poll'. So when a poll node is created, hook_insert() is invoked on poll_insert() only. Hooks that are node-type-specific are noted below.
- *All-module hooks:* This set of hooks is invoked on all implementing modules, to allow other modules to modify what the primary node module is doing. For example, hook_node_insert() is invoked on all modules when creating a poll node.
- *Field hooks:* Hooks related to the fields attached to the node. These are invoked from the field operations functions described below, and can be either field-type-specific or all-module hooks.
- *Entity hooks:* Generic hooks for "entity" operations. These are always invoked on all modules.
Here is a list of the node and entity hooks that are invoked, field operations, and other steps that take place during node operations:

#### Creating a new node (calling node_save() on a new node):
- field_attach_presave()
- hook_node_presave() (all)
- hook_entity_presave() (all)
- Node and revision records are written to the database
- hook_insert() (node-type-specific)
- field_attach_insert()
- hook_node_insert() (all)
- hook_entity_insert() (all)
- hook_node_access_records() (all)
- hook_node_access_records_alter() (all)
#### Updating an existing node (calling node_save() on an existing node):
- field_attach_presave()
- hook_node_presave() (all)
- hook_entity_presave() (all)
- Node and revision records are written to the database
- hook_update() (node-type-specific)
- field_attach_update()
- hook_node_update() (all)
- hook_entity_update() (all)
- hook_node_access_records() (all)
- hook_node_access_records_alter() (all)
#### Loading a node (calling node_load(), node_load_multiple() or entity_load() with $entity_type of 'node'):
- Node and revision information is read from database.
- hook_load() (node-type-specific)
- field_attach_load_revision() and field_attach_load()
- hook_entity_load() (all)
- hook_node_load() (all)
#### Viewing a single node (calling node_view() - note that the input to node_view() is a loaded node, so the Loading steps above are already done):
- hook_view() (node-type-specific)
- field_attach_prepare_view()
- hook_entity_prepare_view() (all)
- field_attach_view()
- hook_node_view() (all)
- hook_entity_view() (all)
- hook_node_view_alter() (all)
- hook_entity_view_alter() (all)
#### Viewing multiple nodes (calling node_view_multiple() - note that the input to node_view_multiple() is a set of loaded nodes, so the Loading steps above are already done):
- field_attach_prepare_view()
- hook_entity_prepare_view() (all)
- hook_view() (node-type-specific)
- field_attach_view()
- hook_node_view() (all)
- hook_entity_view() (all)
- hook_node_view_alter() (all)
- hook_entity_view_alter() (all)
#### Deleting a node (calling node_delete() or node_delete_multiple()):
- Node is loaded (see Loading section above)
- hook_delete() (node-type-specific)
- hook_node_delete() (all)
- hook_entity_delete() (all)
- field_attach_delete()
- Node and revision information are deleted from database
#### Deleting a node revision (calling node_revision_delete()):
- Node is loaded (see Loading section above)
- Revision information is deleted from database
- hook_node_revision_delete() (all)
- field_attach_delete_revision()
#### Preparing a node for editing (calling node_form() - note that if it is an existing node, it will already be loaded; see the Loading section above):
- hook_prepare() (node-type-specific)
- hook_node_prepare() (all)
- hook_form() (node-type-specific)
- field_attach_form()
#### Validating a node during editing form submit (calling node_form_validate()):
- hook_validate() (node-type-specific)
- hook_node_validate() (all)
- field_attach_form_validate()
#### Searching (calling node_search_execute()):
- hook_ranking() (all)
- Query is executed to find matching nodes
- Resulting node is loaded (see Loading section above)
- Resulting node is prepared for viewing (see Viewing a single node above)
- comment_node_update_index() is called.
- hook_node_search_result() (all)
#### Search indexing (calling node_update_index()):
- Node is loaded (see Loading section above)
- Node is prepared for viewing (see Viewing a single node above)
- hook_node_update_index() (all)

## References

* [https://api.drupal.org/api/drupal/modules%21node%21node.api.php/group/node_api_hooks/7.x](https://api.drupal.org/api/drupal/modules%21node%21node.api.php/group/node_api_hooks/7.x)