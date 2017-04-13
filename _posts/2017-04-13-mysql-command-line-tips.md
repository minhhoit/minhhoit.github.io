---
layout: post
title: Mysql Command Line Tips
categories: [mysql, linux]
tags: [mysql, linux]
---

One of the most useful, but under-used, tools a web developer has is the command line. The terminal often scares people away, so here's where we demonstrate some of the most useful day-to-day commands.

## The Basic

If you are new to the command line, you're going to want to know a few things to help find your way around.

### Create Database

Create a database with the following:

`mysql create <dbname> character set utf8 collate utf8_general_ci;`

* Replace `<dbname>` with the name of your database

### List all databases on the sql server

`mysql> show databases;`

### Switch a database

`mysql> use <database name>;

### See all the tables in the database

`mysql> show tables;`

### Show structure of the table

`mysql> show columns from tbl_name`
