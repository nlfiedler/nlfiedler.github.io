---
layout: post
title: Building Git on OpenIndiana
date: 2014-07-19 20:58:03
tags:
- solaris
- openindiana
- git
comments: true
---

# Building Git on OpenIndiana

The existing packages for [Git](http://git-scm.com) on [OpenIndiana](http://openindiana.org) are woefully out of date. Sure, they work, but they also pull in an ancient version of Python (2.4!). OpenIndiana already has nearly everything it needs, so building Git from source should be easy.

```
$ pfexec pkg install developer/illumos-gcc
$ ./configure
$ make
$ pfexec make install
```

## With the Documentation

_A work in progress..._

```
$ pfexec pkg install asciidoc
$ pfexec pkg install gettext
$ pfexec pkg install gnu-getopt
```

* Install Docbook XSL and Docbook XML
* Install Docbook2X
    * perl -MCPAN -e 'install XML::SAX'
* Install xmlto

```
$ ./configure
$ make all doc info
$ pfexec make install install-doc install-html install-info
```
