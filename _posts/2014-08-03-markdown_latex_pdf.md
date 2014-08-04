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

Some of these appeared promising initially, but didn't quite pan out; I will briefly describe the problems and their causes, then move onto the tools that have worked successfully, often impressively so. I will show the conversions with the different tools of [the post on the sample layout]({% post_url 2014-03-06-ace_m2_sample_layout %}), which has sections, images and a table of images, to illustrate differences in generated output. I will give some example code where relevant, as well as some comments on how to set up these tools (good documentation is generally easily found with google), assuming a system setup following the procedures for [Linux, LaTeX and git]({% post_url 2014-08-01-linux_latex %}) and [Jekyll and Ruby]({% post_url 2014-08-02-markdown_html %}). 

## Jekyll plugin from Markdown to PDF via Pandoc LaTeX

What appeared easiest was to install an existing Jekyll plugin designed to convert entire websites to PDF. The static site generator calls [`Pandoc`](http://johnmacfarlane.net/pandoc/), perhaps the best-known and most widely-used markup text converter, which can read / write many different formats. The plugin is even featured on the Jekyll webpage, and is called [`jekyll-pandoc-multiple-formats`](https://github.com/fauno/jekyll-pandoc-multiple-formats). The installation is pretty trivial with `gem` and `bundle`, and involves just a few slight changes to the website's configuration files.

The plugin is installed in the `Gemfile`:

~~~ ruby
source 'https://rubygems.org'
# gem 'github-pages'

gem 'jekyll'

group :plugins do
  gem 'jekyll-pandoc-multiple-formats', github: 'fauno/jekyll-pandoc-multiple-formats', branch: 'develop'
end
~~~

The references to `kramdown` are commented out in the new `_config.yml`:

~~~ ruby
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
    pdf: '--latex-engine=pdflatex --toc --include-in-header=./_includes/latex_header.tex 
	 -V geometry:margin=1in -V header'
    epub:
    markdown: 
~~~

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

~~~
markdown-pdf _posts/test_2014-03-06-ace_m2_sample_layout.md -s css/master.css -f letter -b 1in 
~~~

As with Pandoc, the path to the images is broken. Even after manually moving the files, unfortunately, in the [generated PDF file](/images/2014_08_03_site_pdfs/markdown-pdf_sample_layout.pdf) the YAML header and Liquid tags are transmitted as plain text, and the fonts somehow do not get used (even though they are specified in the CSS file), so the text itself is not typeset properly. 

### [`gimli`](https://github.com/walle/gimli)

This Ruby plugin has similar functionality to `markdown-pdf`. I ran the basic command:

~~~
gimli -f _posts/2014-03-06-ace_m2_sample_layout.md -y -s css/master.css  
	-w '--page-size Letter --default-header'
~~~

On a positive note, the `-y` option allows `gimli` to account properly for the YAML header in the [generated PDF file](/images/2014_08_03_site_pdfs/gimli_sample_layout.pdf), and the correct fonts are used (if not at exactly the right sizes), but the path issue remains, the Liquid tags are still there, and the table is cutoff (whereas `markdown-pdf` sizes them properly to fit within the page).

## Direct html5 to PDF conversion without LaTeX

A better approach for representing the webpage is to let Kramdown handle the Markdown to html5 conversion, implementing first the liquid tags and css, then convert to PDF. One major advantage is that the input is just a URL, which can even be the publicly-accessible one, since that already has all of the images and tables. Thus, generating a PDF does not require access to the markdown source (this is mostly a matter of convenience, because everything on this website is in a public repository, so the direct URL input just saves a few steps). 

### [`Pandoc`](http://johnmacfarlane.net/pandoc/) html5 to PDF

[`Pandoc`](http://johnmacfarlane.net/pandoc/) has direct conversion of an html URL to PDF, and uses an intermediate LaTeX file, as is clear from the [generated PDF file](/images/2014_08_03_site_pdfs/pandoc_html_sample_layout.pdf). Arguments to the LaTeX compiler can be passed to Pandoc, providing control over selecting which LaTeX engine, basic page format control, and the ability to add LaTeX commands (i.e. for packages):

~~~
pandoc --smart http://peterlu.github.io/2014/03/06/ace_m2_sample_layout.html 
	--latex-engine=pdflatex --toc --include-in-header=./_includes/latex_header.tex 
	-V geometry:margin=1in -o pandoc_html_sample_layout.pdf
~~~

To demonstrate the basic capability, I created a `latex_header.tex` file with some basic packages for font management:

~~~
\usepackage{fourier,sourcesanspro,graphicx,listings,xcolor}
~~~

Overall, this works better than Pandoc's direct markdown-to-pdf conversion, as the Liquid tags have been properly implemented and the images linked, all by the markdown processor (Kramdown) that is set up with github and locally. The PDF is quite reasonable, with a proper LaTeX implementation of all of the major features: tables, images, auto-generated table of contents. If you want a quick way to get a LaTeX-generated representation of a web page *without editability*, then this is a great way to do it. The problem is that, for example if I want to combine the LaTeX source files of several pages into one document, I can't do it with this approach, since the LaTeX is already compiled and a PDF generated. Obviously this is not a bug or missing feature in Pandoc, but better seen as a tradeoff for the convenience of making a PDF in one step.

### [`wkhtmltopdf`](http://wkhtmltopdf.org/)

Pandoc goes through a LaTeX intermediate, but is not always desirable. Several other tools exist to do the webpage conversion directly to PDF (much as `markdown-pdf` does from markdown source) from html 5 + css. Again, the advantages are being able to have a separate markdown processor do the conversion with the liquid tags and image imposition. One popular tool is `wkhtmltopdf`(http://wkhtmltopdf.org/) (perhaps better parsed as wh-html-to-pdf), which is a simple library based on `WebKit` (the rendering engine in `Chrome` and other browsers). The program takes a number of [command-line arguments](http://wkhtmltopdf.org/usage/wkhtmltopdf.txt) to control layout, margins and can autogenerate headers and footers with various information:

~~~
wkhtmltopdf --page-size Letter 
	--footer-left [title] --footer-right [webpage] --footer-font-size 8 --footer-line 
	--margin-top 30 --margin-left 25 --margin-right 25 --margin-bottom 15 
	http://peterlu.github.io/2014/03/06/ace_m2_sample_layout.html wkhtmltopdf_sample_layout.pdf
~~~

Of all of the programs, `wkhtmltopdf` does the best job of faithfully reproducing the original format---including the fonts---of the [original web page]({% post_url 2014-03-06-ace_m2_sample_layout %}), as seen in the [generated PDF file](/images/2014_08_03_site_pdfs/wkhtmltopdf_sample_layout.pdf). I therefore generate a PDF of each webpage, and then use Adobe Acrobat in Windows 7 (admittedly not free, but what I use anyway) to concatenate into a [single PDF that represents the entire website](/images/2014_08_03_site_pdfs/colloids_org_140731.pdf).

However, the layout and formatting are (obviously) optimized for a webpage, with the css tags, etc. For a printed document, LaTeX is usually a better choice, with more controls over layout, numbering of figures, tables, and references, none of which can be easily built from just the html source, because that document structure is in no way specified.

## Generating LaTeX from the Markdown source for manual integration

To build a proper printed document from the content of the website, I integrate multiple web pages into a single LaTeX document. I could use Pandoc, specifying LaTeX as the output as LaTeX instead of PDF, with similar syntax as above. However, since Kramdown is already used within Jekyll and GitHub to build the website (for which the markdown is already optimized), and Kramdown supports LaTeX export, I will use that. From the command-line:

### Kramdown: Markdown to LaTeX, some cleanup required

```
kramdown _posts/2014-03-06-ace_m2_sample_layout.md --no-auto-ids --output latex > 140306.tex
```

This generates a LaTeX `.tex` file for each page. (By comparison, Pandoc generates a similar file, but the LaTeX it creates is a bit less clean, and especially for code blocks, Kramdown uses a `lstlisting` environment whereas the Pandoc-generated LaTeX formats the lines directly. So Kramdown turns out to be a better choice from a document readability and maintainability standpoint.) Nevertheless, Kramdown doesn't convert to LaTeX exactly as I prefer, and the generated files require manual cleanup:

1. **Removing the YAML headers**: this text just passes through and remains at the top of each file, which is annoying and must be removed manually.

2. **Fixing the image paths**: as with all of the other markdown converters, the path to the images is just translated directly (as above), looking for `/images`. So I have to change these manually in each resulting LaTeX file, with search-and-replace.

3. **Converting any animated .gif images to .png**: This is not actually Kramdown's fault, but the animated .gif images will not work with LaTeX. They must be converted to `.png` or another format that LaTeX and its graphics libraries process properly. There are some schemes to do this with a graphics conversion rule, but I couldn't get these to work, and I have only a few .gif files on the site, so it was just easier to convert them manually. I could just save them directly in Photoshop, or use a conversion program `gif2png` that can be added in the Synaptic Package Manager, then run from the command line:

```
gif2png -0 -1 *.gif
```

4. Internal links to other posts, which get converted to proper html links with Liquid / Jekyll, here stay to the markdown file, which of course doesn't exist after conversion. So these `\href` tags must be removed manually.

### LaTeX setup, formatting and manual tweaking

Kramdown converts each individual page to LaTeX, but this doesn't include the base structure that defines the document, formatting options, or packages. What I find easiest is to make a separate base LaTeX file, and use the `\input` command to include the files generated by Kramdown and modified as described. This ensures consistent formatting, and allows generation of a full-document table of contents, index, reference list, etc. Here I base the document on RevTeX, with the Review of Modern Physics formatting, with standard title, abstract, and auto-generated table of contents:

~~~latex
\documentclass[twocolumn,rmp,aps,10pt]{revtex4-1}

%\usepackage{charter}
%\usepackage{kpfonts}
%\usepackage{mathpazo}
%\usepackage{tgtermes}
%\usepackage{ebgaramond}
%\usepackage{dejavu}
%\usepackage{bera}
%\usepackage{droid}
\usepackage{fourier}
\usepackage{sourcesanspro}
\usepackage{graphicx,CJK,listings,xcolor,longtable} 
\usepackage[hidelinks]{hyperref}

\lstset{
    frame=single,
    breaklines=true,
    postbreak=\raisebox{0ex}[0ex][0ex]{\ensuremath{\color{red}\hookrightarrow\space}}
}

\begin{document}
\begin{CJK*}{UTF8}{}

\title{ACE-M2 Progress and Preliminary results, Part 1}
\author{Peter J.~Lu (\CJKfamily{bsmi}陸述義)}
\affiliation{Department of Physics and SEAS, Harvard University, Cambridge, Massachusetts 02138, USA}
\author{David A. Weitz}
\affiliation{Department of Physics and SEAS, Harvard University, Cambridge, Massachusetts 02138, USA}

\date{31 July 2014}
\begin{abstract}
We describe and comment on the progress of preparing, loading and launching
samples for the Advanced Colloid Experiment (ACE-M2), in the Light Microscopy
Module (LMM) within the Fluids Integration Rack (FIR) aboard the International
Space Station (ISS). We have completed several rounds of experiments in June and July,
2014. Here we summarize our findings so far, primarily several operational
developments and lessons learned, with scientific results still largely
forthcoming.
\end{abstract}
\maketitle
\end{CJK*}

\tableofcontents

\clearpage

\input{./posts/140101.tex}
\input{./posts/140305.tex}
\input{./posts/140306b.tex}
\input{./posts/140604a.tex}
\input{./posts/140604b.tex}
\input{./posts/140617.tex}
\input{./posts/140701.tex}
\input{./posts/140702.tex}
\input{./posts/140714.tex}
\input{./posts/140715.tex}
\input{./posts/140727.tex}
\end{document}
~~~ 

This should then compile a complete document, which I then make the last few tweaks to:

+ **Image size and placement adjustment:** kramdown doesn't specify the image width, so for each figure I usually add `[width=\columnwidth]` to the `\includegraphics` command. Depending on where it falls in the layout, I also tend to add a `[h]` option to the `\begin{figure}` command, to place the image closely with the text, as in the webpage version.

+ **Table adjustment**: kramdown (and pandoc) use the `longtable` environment, while I am more used to the `tabular` and `table` floating environments. These are pretty easily adjusted back and forth without much syntax change.

+ **fonts**: there are several fancy ways to do this, but I just installed a large set of [font packages in TeXLive](http://tex.stackexchange.com/questions/59403/what-font-packages-are-installed-in-tex-live), compiled the LaTeX document to see what it looked like, and picked the combination (Fourier / Adobe Utopia, and Adobe Source Sans Pro) that I liked the best. The rest of the packages handle Asian languages, links and references, code listings, etc.

The final LaTeX document, with the images and all subfiles, has its own github repository at <https://github.com/peterlu/colloids_org_latex>. The PDF file is linked both [there](https://github.com/peterlu/colloids_org_latex/blob/master/nasa_ace_140731.pdf) and as part of this website [here](/images/2014_08_03_site_pdfs/nasa_ace_140731.pdf)

