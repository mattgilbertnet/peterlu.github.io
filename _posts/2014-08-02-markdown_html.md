---
layout: post
date: 2014-08-02 0:00:00 UTC
title: Building a website from a few dozen lines of code
---

# Coding an entire website with only a few dozen lines of code

## Markdown: the basic text markup language

Take a look at the raw markdown source for the pages on this website, which are more informative than stepping through a lot of examples explictly. By comparing the raw markdown with the final generated html and webpage, it is clear how compact and efficient writing in markdown can be. Each post page starts with several lines delimited by three dashes; this is the yaml header, which gives configuration options, discussed later. Everything that is translated into material that appears on the generated webpages.

### Github-flavored Markdown

There is a standard version of Markdown, but Github has chosen a slightly extended version of the language with a few more formatting commands, and slightly different behavior. This dialect must be specified in the options for the Markdown converter in order for the text to be interpreted properly.

## Jekyll: Building a website

Ruby and its installed option are configured by `Gemfile` in the root directory of the site, which is really short in this case (just make the file and everything works; there is nothing else that needs to address this):

~~~
source 'https://rubygems.org'
gem 'github-pages'
~~~

Jekyll's configuration options are held in a configuration file, `config.yml`:

~~~
markdown: kramdown
kramdown:
   input: GFM
   default_lang: python
~~~

This specifies the software package `Kramdown` as the program (used by Github) that converts Markdown to html5 with CSS formatting, using the templates. `Kramdown` is configured to use the extended style conventions in Github-flavored Markdown, abbreviated GFM, and the default language for codeblocks, but at least at present, this doesn't really affect things as presented by Github. Kramdown takes the posts in the `_posts` subdirectory, and converts them to html. The posts themselves cannot determine their placement and indexing, which is controlled by other files and integrated by Kramdown.

### index.html

The site's main `index.html` is quite brief:

~~~html
---
layout: master
title: Colloid Physics on the International Space Station
---
{% include header.html %}

<article>
  <h1>BCAT and ACE: Colloid Physics in Space</h1>
  <ul class="posts">
    {% for post in site.posts %}
      <li><span>{{ post.date | date_to_string }}</span>: <a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
</article> 

{% include footer.html %}
~~~

The yaml header describes which layout to use, in this case a [master layout](/_layouts/master.html) that has the core html for each page on the site. This allows consistent formatting of pages throughout the site, using the concept of templates (specifically, Liquid Templates) for this purpose.

~~~html
<!DOCTYPE html>
<html>
  <head>
    <title>Peter J. Lu - {{ page.title }} </title>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8">
    <link href='http://fonts.googleapis.com/css?family=Source+Sans+Pro' rel='stylesheet' type='text/css'>
    <link rel="stylesheet" href="/css/master.css" type="text/css" media="screen" charset="utf-8"/>
  </head>

  <body>
      <div id='content'>
        {{ content }}
      </div>
  </body>
</html>
~~~

This specifies that each page uses html5, and gives the fonts and location of the CSS stylesheet, which is [very simple, standard CSS](/css/master.css) for the site in its present form. The header and footer are stored as separate files in the `_includes` directory.

The header includes the banner and its link:

~~~html
<header>
  <a href="http://www.peterlu.org">
    <img src="/images/peterlu_org_banner.png" />
  </a>
</header>
~~~

The footer includes the Creative Commons license:

~~~html
<footer>
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/4.0/80x15.png" /></a><br />This work by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.colloids.org" property="cc:attributionName" rel="cc:attributionURL">Peter J. Lu</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
</footer>
~~~

The code has a number of double braces, which allow some amount of control in programming, to insert various bits of small data from the original source files.

### Post files

Each markdown post has a yaml header that specifies using the `post` template:

~~~html
---
layout: master
---
{% include header.html %}

<article>
{{ content }}
</article>

{% include footer.html %}
~~~

Once again, the same headers and footers are used, with the main body specified by the word `content` inside the double braces. The magic of Jekyll as a static site generator is that it knows to put the contents of the converted markdown there, and preserve all of the rest of the formatting consistently for all pages.

## Publishing the website

The beautiful thing about this form us static site generator usage is that once the configuration files are set just once, they don't really have to be changed. All of the time spent is therefore focused on content generation, and testing.

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

If this is the only website on the machine being worked on, or all of the settings of all of the websites are the same, the simpler command will suffice:

~~~
jekyll serve
~~~

### Git repository on Github

Once the pages are working fine locally, they need to be pushed to the repository on <http://www.github.com>. Since we are not managing this in Eclipse, though in principle that would work and might have some advantages, it's easy [as before]({% post_url 2014-08-01-linux_latex %}) to do this from the command line.

1. Stage any modified files that have changed:

~~~
git add -A
~~~

2. Verify the files are the correct ones that have been modified:

~~~
git status
~~~

Sometimes extra temporary files get added, that are not supposed to be committed to the repository, and the status check will show these.

3. Commit the change

~~~
git commit -m 'description of the commit'
~~~

4. Finally, push the commits to the remote repository (which usually will prompt you for a password):

~~~
git push
~~~

This causes the hosted webpage to re-run Jekyll, and regenerate the entire website. When next loading the page, it should be updated with the latest committed changes.


