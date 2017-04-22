---
layout: post
title: How to set up ssh so you are not asked for a password
categories: [linux]
tags: [linux]
---

Your create a RSA authentication keys to be able to login a remote site from your account, without having to type your password.

Note that once you are set this up, if an intruder breaks into your account/site, they are given access to the site you are allowed in without a password, too! For this reason, this should never be done from root.

+ Run `ssh-keygen(1)` on your machine, an just hit enter when asked for a password. This will generate both a private and a public key. With older SSH versions, they will be stored in `~/.ssh/indentity` and `~/.ssh/indentity.pub` with newers ones, they will be stored in `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`
+ Next, add the contents of the public key file into `~/.ssh/authorized_keys` on the remote site (the file should be mode 600).

You should then be able to use ssh to log in to the remote server without being asked for a password.
