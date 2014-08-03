---
layout: post
disqus_comments: true
date: 2014-08-01 0:00:00 UTC
title: Collaborative writing with Linux, LaTeX, Eclipse and git
---

# Open-source, free tools for writing, collaboration and publishing

As we are starting to have some interesting results, it's the right time to think about the science, and how to communicate it. As I mentioned at the outset, this site is a bit of an experiment itself in being open from start to finish. That philosophy extends to the tools that we use throughout the process, favoring those that are open-source and can therefore be distributed, re-used, and modified at will. 

Using these free tools is partly philosophical, in terms of supporting the freedom to create and use content. But a large part is also practical; I work in a very heterogeneous computing environment, with multiple flavors of UNIX (Linux, Solaris / Illumos) plus Windows (and many working with me use Mac). Standardizing on closed tools means picking a single or small handful of vendors, none of which support all of the platforms I am using every day. Because open-source software can be modified, it can be fixed to run on a large number of different kinds of computers, which is critical toward actually getting real work done.

Free software can often leave the user a bit spoiled for choice. That is, sometimes there really are ten ways to do the same thing, and it's hard to know what's best. So what I am summarizing here is but *one* way to pick good tools to generate research publications in asynchronous collaboration with researchers spread all over the world. I have tried to select those tools which are generally useful and have a large user base, so that the time invested in a particular package won't disappear because either the software is no longer maintained and updated to reflect new technologies and operating systems; or (in the case of closed software), the manufacturer goes out of business or decides to change something fundamental.

Thus I am giving a set of step-by-step instructions, starting with an absolutely empty / blank computer, and putting all of the tools on that we actually use to write, distribute, submit and publish actual papers in scientific journals.

## Linux

Most open-source tools are designed to work with Linux first and foremost, usually with reasonable Mac OS X support (though none of this is tested on that platform). Windows support is generally less developed, and frankly instead of using the Windows-Linux hybrid tools, it's much easier to just install VirtualBox and run everything in an Linux virtual machine.

### Linux Mint 17 Qiana, XFCE edition

Based on Ubuntu 14.04 LTS, but with fewer kernel updates; XFCE desktop is fast, easy to use, and widespread. Either bare-metal install or virtual machine (e.g. VirtualBox) is fine for this demo.

1. Download `.iso` or `.usb` from http://www.linuxmint.com/download.php
2. Make bootable DVD / USB stick; in Linux, there is a program called `Startup Disk Creator`. 

2. Run updates and restart.

### Synaptic package manager

In **Synaptic Package Manager** (`apt-get`), install packages:

+ git
+ pcmanfm
+ alacarte

_Optional packages that I find useful_ 

+ nfs-common
+ laptop-mode-tools
+ powerstat

## LaTeX

Everyone should use LaTeX for all scientific and technical publications. It is the default / native format for most good scientific journals, and can be converted directly from other text markup languages, allowing the distribution of the same content through multiple channels.

### TexLive 2014

This is a large, modern, updated and full-featured distribution, with a great deal of flexibility. Although you can install it through Synaptic, for some reason the packages that Ubuntu releases are always way, way out of date. Instead:

1. Download the latest from http://tug.org/texlive/
2. Extract the archive, either using the desktop or command-line. 
3. Run the installation script with root permissions:

```
sudo ./install_tl
```

This launches the text-based installer. A good minimum installation (about 700 MB) uses the **scheme-basic** scheme, letter size instead of A4, and adds the following packages:

+ recommended fonts
+ Latex recommended packages

_For Asian names_:
+ Chinese 
+ Chinese/Japanese/Korean (base)

The installer will download all of the packages you specify, so you must be online, and depending on the server traffic, can be very slow.

4. After installation completes, add the RevTeX-4.1 packages for _Physical Review_ journals.

```
/usr/local/texlive/2014/bin/x86_64-linux $ sudo ./tlmgr install revtex
```

## Eclipse

Eclipse is a large software integrated development environment (IDE), open-sourced by IBM. Current version is Eclipse 4.4 "Luna". To install:

1. download from http://eclipse.org/downloads/ 
2. extract archive, which places all files in single folder
3. for convenience, move to a system directory

```
sudo mv eclipse /opt/
sudo chmod 777 /opt/eclipse
```

### Texlipse

LaTeX integration for Eclipse, which controls the document build process. When you modify any file and save it, Eclipse will automatically execute the right programs to rebuild the document. To install in Eclipse:

1. ```help->install``` new software, with path ```http://texlipse.sourceforge.net```

2. ```Window->Preferences->Texlipse->Builder Settings```, set path: ```/usr/local/texlive/2014/bin/x86_64-linux```

## Git

Git is the version-control system used to manage the Linux kernel, and was written by Linux Torvalds himself (the creator of Linux).  

### Configure git for first usage

```
git config --global user.name "Peter J. Lu"
git config --global user.email plu@fas.harvard.edu
```

### SSH keys 
In order to manage identities and security, git (like many modern Linux tools) requires authentication, and does so via SSH keys. This will allow modifications from your machine to change a remote repository in the cloud, and replaces login credentials. To generate the SSH keys to allow you to `push` to remote git repository:

```
ssh-keygen -t rsa -C "plu@fas.harvard.edu"
```

You will be prompted for a passphrase, the password that you will use to unlock this key every time you want to use it.

### Add SSH key to bitbucket.org repository 

1. login to https://www.bitbucket.org
2. In upper right corner, drop down "manage account", SSH keys option on left: add text from id_rsa.pub

## Working with Git, Eclipse and LaTeX

The first step is to clone a repository from the remote location to the local machine and then import into a new Eclipse project. In the workspace directory (typically `~/workspace`):

```
git clone git@bitbucket.org:peterjlu/aot_xl.git
````

In Eclipse: file->import->general->existing projects into workspace

This will create a new project that Eclipse is aware has a remote git-repository. Eclipse has great integration with git, so that you can use either the command-line or the internal GUI-based tools, with the same results and no conflicts. 

### Updating a git repository: command-line

```
git add -A
git commit -m 'name of update'
git push
```

You will be prompted for the passphrase; once you enter it successfully, it automatically uploads and updates the remote repository. To confirm this, you can check online at http://www.bitbucket.org.

### Updating a git repository: inside Eclipse

Right click on the project name in the _Project Explorer_, Team->commit. Enter the text that describes the update (the equivalent of the text entered with the command-line `git commit`), then press the "Commit and Push" button to do the commit and upload in one step.

## JabRef

LaTeX has a simple, elegant way to store reference data, using text files to store data, which are then processed by a program called BibTeX. BibTeX is installed as part of any normal LaTeX distribution, TeXLive included, and will automatically be called at the appropriate time by Texlipse when building in Eclipse. Hence, there is no special installation procedure.

BibTeX stores references as records in a simple text file, which makes management of the files a bit cumbersome when more than a few references are involved. A number of free tools exist to help with this problem, and one of the more popular ones is JabRef, which works with the native BibTeX text files as the fundamental database format. 

JabRef is a java-based program, so there are several ways to run it, either from the command-line as a local java program, or directly from the browser.

### Install and run JabRef locally

1. Download the latest version from http://jabref.sourceforge.net
2. From the command-line, run:

```
java -jar JabRef-2.10.jar
```

This should run automatically. For information on the version of Java installed, run `java -version`, and if that doesn't work, check the directory `/usr/bin/java` to see if the program is installed. Otherwise, use Synaptic to install it.

### Launch JabRef from the web browser

On the main JabRef page at http://jabref.sourceforge.net, click the button marked "Web Start, run JabRef instantly", which will then start the (second most recent) version. You *must* give the application full access to your machine in order for it to work.

Once launched and a BibTeX file is opened, you get a list of all of the records, which can then be edited by double-clicking. There are several tabs that then open up, so you can either edit the appropriate `Required Fields`, or get the direct text listing in the tab marked `BibTeX source`.

### Importing references via the DOI number, ISBN, keywords etc.

What is truly useful about JabRef is the ability to auto-import complete references from various sources:

1. Click on Search->Web search to bring up a new tab on the left, 
2. There is a drop-down box marked `ACM Portal`. Clicking on this brings up a list of sources from which to import data. Select 'DOI to BibTeX'.
3. From a paper, web page, etc. type or paste the DOI number into the white box, and press Fetch. That brings up a new dialog with the paper, which you then click to import. Do the same thing for ISBN number or other search information.

This automatically adds a completed reference to the list, but usually requires some modification:

1. Change the `Bibtexkey` to whatever key system you use for the rest of your references.

2. The page numbers have a funny character (an en dash) instead of a hyphen; replace this with a regular `-` hyphen.

Save the database / BibTeX file. Now when you add that key in a `~\cite{}` command in your LaTeX document, and then save it, Eclipse will build all of the relevant files and generate a document with the updated reference.
