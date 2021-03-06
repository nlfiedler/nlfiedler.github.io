---
author: nlfiedler
comments: true
date: 2012-08-19 01:47:30+00:00
layout: post
slug: building-git-on-mac-with-docs
title: Building Git on Mac, with docs!
wordpress_id: 188
tags:
- git
- mac
- SCM
description: Building Git and its documentation from source on Mac OS X.
---

_[Updated July 2014]_

If you have built [Git](http://git-scm.org/) from source on Mac OS X you probably didn't have much trouble. That is, unless you were trying to also build the documentation, in which case you probably ran into problems, right? I'm guessing it happened somewhere around getopt, gettext, or some other library or tool you didn't have installed. And after several attempts to get the nth dependency to build you gave up. Look no further, as these steps will build Git _and_ its documentation on Mac OS X, at least as of today.

This guide was mostly inspired by [Setting up the Git documentation build chain on Mac OS X Leopard](https://wincent.com/wiki/Setting_up_the_Git_documentation_build_chain_on_Mac_OS_X_Leopard) which was mostly correct at the time it was written, I assume. As of today it's missing a couple of steps and it deserves an update for the latest Mac OS X and its libraries.

So without further ado, here are the steps.

## AsciiDoc

Install [AsciiDoc](http://sourceforge.net/projects/asciidoc/files/asciidoc/) using the usual `configure`, `make`, `sudo make install`, which you probably did already since this one is easy.

## gettext

Installation of [gettext](http://mirrors.kernel.org/gnu/gettext/), used to be difficult, but now the usual `configure`, `make`, `sudo make install` works. Hurray for progress!

## getopt

Download [getopt](http://software.frodo.looijaard.name/getopt/download.php), extract the tarball, open the `Makefile` in an editor and add `-lintl` to the `LDFLAGS` variable (as described in the original Wincent wiki), at which point `make` and `sudo make install` should work.

## Docbook

Install [Docbook XSL](http://sourceforge.net/projects/docbook/files/docbook-xsl/) and [Docbook XML](http://www.oasis-open.org/docbook/xml/4.5/), which is a bit more involved. This is a streamlined version of what you will find on the Wincent wiki.

{% highlight sh %}
$ sudo mkdir -p /usr/local/share/docbook
$ cd /usr/local/share/docbook
$ sudo tar jxf ~/Downloads/docbook-xsl-1.78.1.tar.bz2
$ sudo unzip ~/Downloads/docbook-xml-4.5.zip -d docbook-xml-4.5
$ sudo mkdir /etc/xml
$ sudo xmlcatalog --noout --add nextCatalog '' \
    file:///usr/local/share/docbook/docbook-xsl-1.77.1/catalog.xml --create /etc/xml/catalog
$ sudo xmlcatalog --noout --add nextCatalog '' \
    file:///usr/local/share/docbook/docbook-xml-4.5/catalog.xml --create /etc/xml/catalog
{% endhighlight %}

## Docbook2X

Install [Docbook2X](http://sourceforge.net/projects/docbook2x/files/docbook2x/) which apparently wasn't necessary when the Wincent page was written.

{% highlight sh %}
$ ./configure
$ make
$ sudo make install
$ cd /usr/local/bin
$ sudo ln -s docbook2texi docbook2x-texi
$ sudo xmlcatalog --noout --add nextCatalog '' \
    file:///usr/local/share/docbook2X/xslt/catalog.xml --create /etc/xml/catalog
{% endhighlight %}

## xmlto

Install [xmlto](https://fedorahosted.org/releases/x/m/xmlto/) by first ensuring that `/usr/local/bin` is in your `PATH` before `/usr/bin` (`sudo vi /etc/paths` may help). Now the usual `configure`, `make`, `sudo make install` and you are nearly there.

## Git

Install Git, finally!

{% highlight sh %}
$ ./configure
$ make all doc info
$ sudo make install install-doc install-html install-info
{% endhighlight %}

At this point you should have a freshly built Git in `/usr/local` along with all of the documentation. Hurray! If that doesn't work for you, feel free to write to me. I would like to keep these instructions up to date as best I can.

## Epilogue

If you happen to have installed a version of iconv in `/usr/local`, you may experience some problems along the way when building Git and its documentation. If so, check out post from July 2014 that attempts to resolve this issue.
