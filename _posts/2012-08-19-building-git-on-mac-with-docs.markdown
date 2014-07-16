---
author: nlfiedler
comments: true
date: 2012-08-19 01:47:30+00:00
layout: post
slug: building-git-on-mac-with-docs
title: Building Git on Mac, with docs!
wordpress_id: 188
categories:
- Compiling
- Computing
- HowTo
tags:
- git
- mac
- SCM
---

If you have built [Git](http://git-scm.org/) from source on Mac OS X you probably didn't have much trouble. That is, unless you were trying to also build the documentation, in which case you probably ran into problems, right? I'm guessing it happened somewhere around getopt, gettext, or some other library or tool you didn't have installed. And after several attempts to get the nth dependency to build you gave up. Look no further, as these steps will build Git _and_ its documentation on Mac OS X, at least as of today.

This guide was mostly inspired by [Setting up the Git documentation build chain on Mac OS X Leopard](https://wincent.com/wiki/Setting_up_the_Git_documentation_build_chain_on_Mac_OS_X_Leopard) which was mostly correct at the time it was written, I assume. As of today it's missing a couple of steps and it deserves an update for Lion and the latest versions of the libraries.

So without further ado, here are the steps.

A. Install [AsciiDoc](http://sourceforge.net/projects/asciidoc/files/asciidoc/) using the usual `configure`, `make`, `sudo make install`, which you probably did already since this one is easy.

B. Download [gettext](http://mirrors.kernel.org/gnu/gettext/), extract the tarball and open the file `gettext-tools/gnulib-lib/stpncpy.c` in an editor. Find the `#define __stpncpy stpncpy` line and comment it out (hat tip to [biow0lf](https://github.com/mxcl/homebrew/issues/4540)). Now the usual `configure`, `make`, `sudo make install` will suffice.

C. Download [getopt](http://software.frodo.looijaard.name/getopt/download.php), extract the tarball, open the `Makefile` in an editor and add `-lintl` to the `LDFLAGS` variable (as described in the original Wincent wiki), at which point `configure`, `make`, `sudo make install` should work.

D. Install [Docbook XSL](http://sourceforge.net/projects/docbook/files/docbook-xsl/) and [Docbook XML](http://www.oasis-open.org/docbook/xml/4.5/), which is a bit more involved. This is a streamlined version of what you will find on the Wincent wiki.



	
  1. `sudo mkdir -p /usr/local/share/docbook`

	
  2. `cd /usr/local/share/docbook`

	
  3. `sudo tar jxf ~/Downloads/docbook-xsl-1.77.1.tar.bz2`

	
  4. `sudo unzip ~/Downloads/docbook-xml-4.5.zip -d docbook-xml-4.5`

	
  5. `sudo mkdir /etc/xml`

	
  6. `sudo xmlcatalog --noout --add nextCatalog '' file:///usr/local/share/docbook/docbook-xsl-1.77.1/catalog.xml --create /etc/xml/catalog`

	
  7. `sudo xmlcatalog --noout --add nextCatalog '' file:///usr/local/share/docbook/docbook-xml-4.5/catalog.xml --create /etc/xml/catalog`


E. Install [Docbook2X](http://sourceforge.net/projects/docbook2x/files/docbook2x/) which apparently wasn't necessary when the Wincent page was written.



	
  1. `./configure`

	
  2. `make`

	
  3. `sudo make install`

	
  4. `cd /usr/local/bin`

	
  5. `sudo ln -s docbook2texi docbook2x-texi`

	
  6. `sudo xmlcatalog --noout --add nextCatalog '' file:///usr/local/share/docbook2X/xslt/catalog.xml --create /etc/xml/catalog`


F. Install [xmlto](https://fedorahosted.org/releases/x/m/xmlto/) by first ensuring that `/usr/local/bin` is in your `PATH` before `/usr/bin` (`sudo vi /etc/paths` may help). Now the usual `configure`, `make`, `sudo make install` and you are nearly there.

G. Install Git, finally!



	
  1. `git clone https://github.com/git/git.git`

	
  2. `git checkout v1.7.11.5` (or whatever the latest release happens to be)

	
  3. `make prefix=/usr/local all doc info`

	
  4. `sudo make prefix=/usr/local install install-doc install-html install-info`


At this point you should have a freshly built Git in `/usr/local` along with all of the documentation. Hurray! If that doesn't work for you, feel free to write to me or post a comment. I would like to keep these instructions up to date as best I can.
