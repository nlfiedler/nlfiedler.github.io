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

The existing packages for [Git](http://git-scm.com) on [OpenIndiana](http://openindiana.org) are woefully out of date. Sure, they work, but they also pull in an ancient version of Python (2.4!). OpenIndiana already has much of what it needs, so building Git from source should be easy. To start with, install those packages needed for compiling and installing software from source.

```
$ pfexec pkg install developer/illumos-gcc
$ pfexec pkg install developer/gnu-binutils
$ pfexec pkg install system/header
$ pfexec pkg install system/library/math/header-math
$ pfexec pkg install developer/library/lint
$ export PATH=/opt/gcc/4.4.4/bin:$PATH
$ pfexec pkg install SUNWscp
```

And now we are ready to build and install Git itself.

```
$ ./configure
$ make
$ pfexec make install
```

## With the Documentation

To build Git's documentation from source, there is quite a bit of setup needed.

### AsciiDoc

Retrieve the latest tarball for [AsciiDoc](http://sourceforge.net/projects/asciidoc/) and build using the usual command sequence.

```
$ ./configure
$ make
$ pfexec make install
```

### gettext

Super easy.

```
$ pfexec pkg install text/gnu-gettext
```

### getopt

_Work in progress..._
_Does the SUNWcs package satisfy this requirement already?_

Download [getopt](http://software.frodo.looijaard.name/getopt/), extract the tarball, open the `Makefile` in an editor and add `-lintl` to the `LDFLAGS` variable, and then compile and install.

```
$ make
$ pfexec make install
```

### Docbook

* Install Docbook XSL and Docbook XML
* Install Docbook2X
    * perl -MCPAN -e 'install XML::SAX'

### xmlto

* Install xmlto

### Finally, Git's documentation

```
$ ./configure
$ make doc info
$ pfexec make install-doc install-html install-info
```
