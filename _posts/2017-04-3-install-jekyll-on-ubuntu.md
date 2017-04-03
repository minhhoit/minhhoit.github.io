---
layout: post
title: Install Jekyll on Ubuntu
categories: [linux, ubuntu, ruby, jekyll]
tags: [ubuntu, ruby, jekyll]
---

Jekyll is a static site generator with a templating system that can be adapted for many types of websites, including blogs. It can be run on a server, or run locally and the generated files uploaded to a server. It is the default software used by Github Pages.

## Install Prerequisites

Install ruby, the ruby development libraries, and the make command.

`sudo apt-get install ruby ruby-dev make gcc nodejs`

## Install Jekyll

Install the Jekyll gem system wide. For speed, we are excluding the extended documentation. To include all documentation, omit the `--no-rdoc --no-ri` switches.

`sudo gem install jekyll --no-rdoc --no-ri`

## Start Jekyll

Check that Jekyll has been successfully installed.

`jekyll -V`

## Recommended

Additional gems can add features to Jekyll, such the github-pages gem which bundles together several gems supported by Github Pages.

`sudo gem install github-pages --no-rdoc --no-ri`

## Get Website Content

Now that Jekyll is installed, we need content for it to serve. We can either use a current website, or set up a new site from scratch.

### Create New Site

For a new Jekyll site, use the new command to create a directory structure and config files.

<pre>
jekyll new my-awesome-site
cd my-awesome-site
</pre>

## Start Jekyll

Now that the basic config and layout are available, start Jekyll to generate the website HTML and start a local server.

`jekyll serve`

Then visit http://localhost:4000 in a web browser.

## Extra Options

Jekyll can watch the directory for changes and regenerate the website when files are modified.

`jekyll serve -w`

The default port 4000 can be changed, for example when running multiple Jekyll instances.

`jekyll serve --port 4001`

Then visit [http://localhost:4001]http://localhost:4001 in a web browser.

The `_config.yml` can also be specified. This is useful if you need different configs for a public site or when running locally. For example, when running vnnpal.com locally use:

`jekyll serve --config _config-local.yml`

The website can also be generated without starting a local server. The files are placed into the _site directory and can be uploaded to a web server.

`jekyll build`

## Updating Jekyll

ekyll can be updated similar to installation, by using the gem update command.

`sudo gem update jekyll --no-rdoc --no-ri`

The same command can be used to update additional gems.

`sudo gem update github-pages --no-rdoc --no-ri`

## References

* [http://stackoverflow.com/a/8113213/2043808]( http://stackoverflow.com/a/8113213/2043808)
* [https://github.com/jekyll/jekyll/pull/2362](https://github.com/jekyll/jekyll/pull/2362)
* [http://ubuntuforums.org/showthread.php?t=1464768&p=9188769#post9188769
](http://ubuntuforums.org/showthread.php?t=1464768&p=9188769#post9188769)
* [http://jekyllrb.com/docs/home/
](http://jekyllrb.com/docs/home/)
