---
layout: post
title: Setting up your GitHub Pages site locally with Jekyll
categories: [ruby, jekyll]
tags: [ruby, jekyll]
---

You can set up a local version of your Jekyll GitHub Pages site to test changes to your site locally. We highly recommend installing Jekyll to preview your site and help troubleshoot failed Jekyll builds.

In this article:

1. [Requirements](#Requirements)
2. [Step 1: Create a local repository for your Jekyll site](#step-1-create-a-local-repository-for-your-jekyll-site)
3. [Step 2: Install Jekyll using Bundler](#step-2-install-jekyll-using-bundler)
4. [Step 3 (optional): Generate Jekyll site files](#step-3-optional-generate-jekyll-site-files)
5. [Step 4: Build your local Jekyll site](#step-4-build-your-local-jekyll-site)
6. [Keeping your site up to date with the GitHub Pages gem](#keeping-your-site-up-to-date-with-the-github-pages-gem)
7. [Next steps: Configuring Jekyll](#next-steps-configuring-jekyll)
8. [Further reading](#further-reading)

## Requirements
---

We recommend using Bundler to install and run Jekyll. Bundler manages Ruby gem dependencies, reduces Jekyll build errors, and prevents environment-related bugs. To install Bundler, you must install Ruby.

+ Open Terminal.
+ Check whether you have Ruby 2.1.0 or higher installed:

<pre>
ruby --version
ruby 2.X.X
</pre>

* If you don't have Ruby installed, install Ruby 2.1.0 or higher.
* Install Bundler:

<pre>
gem install bundler
# Installs the Bundler gem
</pre>

* If you already have a local repository for your Jekyll site, skip to Step 2.

## Step 1: Create a local repository for your Jekyll site
---

+ Open Terminal.
+ On your local computer, initialize a new Git repository for your Jekyll site:

<pre>
git init JEKYLL-SITE-REPOSITORY-NAME
Initialized empty Git repository in /Users/octocat/my-site/.git/
# Creates a new file directory on your local computer, initialized as a Git repository
</pre>

+ Change directories to the new repository you created:

<pre>
cd JEKYLL-SITE-REPOSITORY-NAME
# Changes the working directory
</pre>

+ If your new local repository is for a Project pages site, create a new gh-pages branch:

<pre>
git checkout -b gh-pages
Switched to a new branch 'gh-pages'
# Creates a new branch called 'gh-pages', and checks it out
</pre>

## Step 2: Install Jekyll using Bundler
---

To track your site's dependencies, Ruby will use the contents of your Gemfile to build your Jekyll site.

Check to see if you have a Gemfile in your local Jekyll site repository:

<pre>
ls
Gemfile
</pre>

If you have a Gemfile, skip to step 4. If you don't have a Gemfile, skip to step 2.
If you don't have a Gemfile, open your favorite text editor, such as Atom, and add these lines to a new file:

<pre>
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
</pre>

Name the file Gemfile and save it to the root directory of your local Jekyll site repository. Skip to step 5 to install Jekyll.

If you already have a Gemfile, open your favorite text editor, such as Atom, and add these lines to your Gemfile:

<pre>
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
</pre>

Install Jekyll and other dependencies from the GitHub Pages gem:

<pre>
bundle install
Fetching gem metadata from https://rubygems.org/............
Fetching version metadata from https://rubygems.org/...
Fetching dependency metadata from https://rubygems.org/..
Resolving dependencies...
</pre>

## Step 3 (optional): Generate Jekyll site files
---

To build your Jekyll site locally, preview your site changes, and troubleshoot build errors, you must have Jekyll site files on your local computer. You may already have Jekyll site files on your local computer if you cloned a Jekyll site repository. If you don't have a Jekyll site downloaded, you can generate Jekyll site files for a basic Jekyll template site in your local repository.

If you want to use an existing Jekyll site repository on GitHub as the starting template for your Jekyll site, fork and clone the Jekyll site repository on GitHub to your local computer. For more information, see "Fork a repo."

`Note: As of Jekyll 3.2, the default Jekyll site contains a Gemfile that locks Jekyll to the Gem version you build it with. To instead lock it to the version used by GitHub Pages, you'll uncomment the gem "github-pages", group: :jekyll_plugins line in the steps below.`

If you don't already have a Jekyll site on your local computer, create a Jekyll template site in a new directory:

<pre>
jekyll _3.3.0_ new NEW-JEKYLL-SITE-REPOSITORY-NAME
New jekyll site installed in /Users/octocat/NEW-JEKYLL-SITE-REPOSITORY-NAME.
</pre>

Navigate into your new site directory:

<pre>
cd NEW-JEKYLL-SITE-REPOSITORY-NAME
</pre>

Edit your Gemfile and remove the following line:

<pre>
"jekyll", "3.3.0"
</pre>

In the Gemfile, delete the # at the beginning of this line:

gem "github-pages", group: :jekyll_plugins
Initialize your site directory as a Git repository.

<pre>
git init
Initialized empty Git repository in /Users/octocat/name-of-your-directory
</pre>

Connect your remote repository on GitHub to your local repository for your GitHub Pages site.

<pre>
git remote add origin https://github.com/username-or-organization-name/your-remote-repository-name
</pre>

To edit the Jekyll template site, open your new Jekyll site files in a text editor. Make your changes and save them in the text editor. You can preview these changes locally on your computer without committing your changes using Git by running a Jekyll command to build your site.

Add or stage your changes.

<pre>
git add .
</pre>

Commit your changes with a comment.

<pre>
git commit -m "updated site"
</pre>

To publish your changes on your GitHub Pages site, push your changes to your remote repository on GitHub.

<pre>
git push -u origin master
</pre>

For more information on using this Git workflow, see "Good Resources for Learning Git and GitHub" or see this Git cheat sheet.

## Step 4: Build your local Jekyll site

+ Navigate into the root directory of your local Jekyll site repository.
+ Run your Jekyll site locally:

<pre>
bundle exec jekyll serve
Configuration file: /Users/octocat/my-site/_config.yml
           Source: /Users/octocat/my-site
      Destination: /Users/octocat/my-site/_site
Incremental build: disabled. Enable with --incremental
     Generating...
                   done in 0.309 seconds.
Auto-regeneration: enabled for '/Users/octocat/my-site'
Configuration file: /Users/octocat/my-site/_config.yml
   Server address: http://127.0.0.1:4000/
 Server running... press ctrl-c to stop.
</pre>
+ Preview your local Jekyll site in your web browser at http://localhost:4000.


## Keeping your site up to date with the GitHub Pages gem

Jekyll is an active open source project and is updated frequently. As the GitHub Pages server is updated, the software on your computer may become out of date, resulting in your site appearing different locally from how it looks when published on GitHub.

Open Terminal.
Run this update command:
1. If you followed our setup recommendations and installed Bundler, run bundle update github-pages or simply bundle update and all your gems will update to the latest versions.
2. If you don't have Bundler installed, run gem update github-pages

## Next steps: Configuring Jekyll

To configure your pages site further, see "Configuring Jekyll." To set up a project pages site, see Jekyll's official documentation on project pages URLs.

## Further Reading

+ "Troubleshooting GitHub Pages Builds"
+ "Using Jekyll as a static site generator with GitHub Pages"
+ Jekyll's official GitHub Pages documentation
+ Jekyll commands

## References

+ [https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)