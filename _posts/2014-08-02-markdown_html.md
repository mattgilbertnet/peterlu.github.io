---
layout: post
date: 2014-08-02 0:00:00 UTC
title: Building a website from a few dozen lines of code
---

# Coding an entire website with only a few dozen lines of code

## Markdown: the basic text markup language

### Github-flavored Markdown

## Jekyll: Building a website

### Kramdown: converting Markdown to fully-formatted html5 + CSS and templates

### Liquid Templates

## Publishing the website

### Running Jekyll locally to test the site

As this website is hosted on http://www.github.com/peterlu/peterlu.github.io, it's easily cloned into a new repository:

~~~
git clone git@github.com:peterlu/peterlu.github.io.git
~~~

Installing Jekyll locally (assuming the system is in the state at the end of the [previous post on Linux and Latex]({% post_url 2014-08-01-linux_latex %}) is relatively straightforward:

~~~
sudo apt-get install ruby ruby-dev nodejs
sudo gem install bundle
~~~

In the main directory, that contains the 'Gemfile':

~~~
bundle install
bundle update
~~~

Then to build the website, and serve it using the built-in webserver:

~~~
bundle exec jekyll serve
~~~

If this is the only website on the machine being worked on, or all of the settings of all of the websites are the same, you can use:

~~~
jekyll serve
~~~

### Git repository on Github
