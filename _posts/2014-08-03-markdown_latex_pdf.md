---
layout: post
disqus_comments: true
date: 2014-08-03 0:00:00 UTC
title: Converting the website to documents
---

# Generating publication-quality documents from the same website source

The beauty of using text markup languages is that the original source documents can be converted into multiple formats while retaining all of the tagging and structure. [As described previously]({% post_url 2014-08-02-markdown_html %}), the markdown source is easily converted with Jekyll into a fully-formatted, html5-compliant website, which is small, fast, secure and loads on almost any device connected to the internet. 

However, there are plenty of situations where a different format of the same information may be preferred, including documentation progress for archival, historical, legal or compliance reasons, and making the information available when not connected online, or (gasp) printing it out on paper. One could just print out each webpage, but this tends not to format properly, looks terrible, and is rather inconvenient.

Therefore, we would like to generate a formatted document for printing in PDF form, which captures the site at a given moment in time (thus documenting our progress) and can be transmitted easily through email, etc. Fortunately, markdown was designed in large part to handle this task, and thus we will convert our markdown source to a PDF. This allows us, in principle, to start out with a single set of source files (markdown text files, images) and with a little bit of clever programming generate both the complete website (already described) and a properly formatted PDF document.

Here I will describe a number of different approaches, again using all open-source, free tools:

1. Convert Markdown to LaTeX with PanDoc, then use existing installed LaTeX tools (e.g. from TexLive) to generate the PDF, in one step.

2. Convert Markdown to PDF directly, using a non-LaTeX engine

3. Take the formatted html5 output from Jekyll / Kramdown which are already generated, and make a PDF directly without LaTeX.

4. Convert Markdown to LaTeX, then assemble the LaTeX files with the existing toolset (Eclipse, git, TexLive) and build the PDF

Some of these appeared promising initially, but didn't quite pan out; I will briefly describe the problems and their causes, then move onto the tools that have worked successfully, often impressively so. For these, I will give example code where relevant, as well as instructions on how to set up these tools, assuming a system setup following the procedures for [Linux, LaTeX and git]({% post_url 2014-08-01-linux_latex %}) and [Jekyll and Ruby]({% post_url 2014-08-02-markdown_html %}). 

## Jekyll plugin from Markdown to PDF via Pandoc LaTeX

What appeared easiest was to install an existing Jekyll plugin designed to convert entire websites to PDF. The static site generator calls `Pandoc`, perhaps the best-known and most widely-used markup text converter, which can read / write many different formats. The plugin is even featured on the Jekyll webpage, and is called [`jekyll-pandoc-multiple-formats`](https://github.com/fauno/jekyll-pandoc-multiple-formats). The installation is pretty trivial with `gem` and `bundle`, and involves just a few slight changes to the website's configuration files.

The plugin is installed in the `Gemfile`:

```ruby
source 'https://rubygems.org'
# gem 'github-pages'

gem 'jekyll'

group :plugins do
  gem 'jekyll-pandoc-multiple-formats', github: 'fauno/jekyll-pandoc-multiple-formats', branch: 'develop'
end
```

The references to `kramdown` are commented out in the new `_config.yml`:

```ruby
# markdown: kramdown
# kramdown:
#   input: GFM
#   default_lang: python

pandoc:
  skip: false
  impose: false
  output: ./tmp
  flags: '--smart'
  site_flags: 
  outputs:
    pdf: '--latex-engine=pdflatex --toc --include-in-header=./_includes/latex_header.tex  -V geometry:margin=1in -V header'
    epub:
    markdown: 
```
With these few lines of text, the static site generator launches Pandoc, instead of Kramdown (default of Jekyll on GitHub, and thus what I run locally), as the markdown converter, which then generates:

+ formatted html5 for the website, as with Kramdown
+ LaTeX for each page, which then calls `pdflatex`, `xelatex` or `lualatex` to generate the PDF.
+ Epub, for electronic readers.

All of these operations are performed when `jekyll serve` is called, and thus is a very easy, very convenient solution.

*Unfortunately, it doesn't really work.*

There are several major problems, all interrelated:

1. The markdown that Pandoc converts is not exactly the same as the github-flavored markdown (GFM) used by Github and thus by me for this website.

2. The path for each image is broken, and because the PDF is made without allowing any intermediate modification of the LaTeX, there is no way to fix it. Therefore, either the images are all broken in the markdown, or they are all broken in the PDF with this approach. The short answer is that I have images stored in the `/images` directory, relative to the root directory of the site---which makes perfect sense when you deploy. Pandoc (and, Kramdown, as we will see) just pass the string on to LaTeX, which then thinks that this is a folder in the machine's root directory, obviously a terrible place to store any images and not what ever is intended. I googled for a while and was unable to find any simple solution. *So I had to move the entire `images` folder has to be moved to the filesystem root, literally in `/images`, to generate a proper pdf*

3. The image positions and pagination (page breaks) can't be controlled.

A proper [LaTeX-based PDF file](/images/2014_08_03_site_pdfs/jekyll_pandoc_sample_layout.pdf) is created, complete with the table of contents, because the LaTeX is immediately converted to PDF, there is no way to correct the problems above, even manually.

## Markdown to PDF without LaTeX

An alternative is to skip the LaTeX intermediate stages, and do a direct conversion and rendering of the markdown 

### [`markdown-pdf`](https://www.npmjs.org/package/markdown-pdf)

This is a Node-js based plugin that converts markdown to html5, styles it with something called boilerplate, then makes a PDF. I ran the command:

```
markdown-pdf _posts/test_2014-03-06-ace_m2_sample_layout.md -s css/master.css -f letter -b 1in 

```

As with Pandoc, the path to the images is broken. Even after manually moving the files, unfortunately, in the [generated PDF file](/images/2014_08_03_site_pdfs/markdown-pdf_sample_layout.pdf) the YAML header and Liquid tags are transmitted as plain text, and the fonts somehow do not get used (even though they are specified in the CSS file), so the text itself is not typeset properly. 

### [`gimli`](https://github.com/walle/gimli)

This Ruby plugin has similar functionality to `markdown-pdf`. I ran the basic command:

```
gimli -f _posts/2014-03-06-ace_m2_sample_layout.md -y -s css/master.css  -w '--page-size Letter --default-header'
```

On a positive note, the `-y` option allows `gimli` to account properly for the YAML header in the [generated PDF file](/images/2014_08_03_site_pdfs/gimli_sample_layout.pdf), and the correct fonts are used (if not at exactly the right sizes), but the path issue remains, the Liquid tags are still there, and the table is cutoff (whereas `markdown-pdf` sizes them properly to fit within the page).

## Direct html5 to PDF conversion without LaTeX

A better approach for representing the webpage is to let Kramdown handle the Markdown to html5 conversion, implementing first the liquid tags and css, then convert to PDF.

### PanDoc html5 to PDF

```
pandoc --smart http://peterlu.github.io/2014/03/06/ace_m2_sample_layout.html 
--latex-engine=pdflatex --toc --include-in-header=./_includes/latex_header.tex 
-V geometry:margin=1in -o pandoc_html_ace_m2_sample_layout.pdf
```

### wkhtmltopdf

```
wkhtmltopdf --page-size Letter 
--footer-left [title] --footer-right [webpage] --footer-font-size 8 --footer-line 
--margin-top 30 --margin-left 25 --margin-right 25 --margin-bottom 15 
http://peterlu.github.io/2014/03/06/ace_m2_sample_layout.html wkhtmltopdf_sample_layout.pdf
```

## Mark LaTeX conversion

### Kramdown: Markdown to LaTeX

```
kramdown _posts/2014-03-06-ace_m2_sample_layout.md --output latex > 140306.tex
```

### Some cleanup required

Unfortunately, Kramdown doesn't convert to LaTeX exactly as I prefer

1. Removing the YAML headers

2. Fixing the image paths

3. Converting any animated .gif images to .png

```
gif2png -0 -1 *.gif
```

### LaTeX setup, formatting and manual tweaking




