---
layout: post
title: Building Git with docs on OpenIndiana
date: 2014-07-24 07:07:33
tags:
- solaris
- openindiana
- git
comments: true
description: Building Git and its documentation from source on OpenIndiana.
---

The existing packages for [Git](http://git-scm.com) on [OpenIndiana](http://openindiana.org) are woefully out of date. Sure, they work, but they also pull in an ancient version of Python (2.4!). OpenIndiana already has much of what it needs, so building Git from source should be easy. To start with, install those packages needed for compiling and installing software from source.

```
$ pfexec pkg install developer/illumos-gcc
$ pfexec pkg install developer/gnu-binutils
$ pfexec pkg install system/header
$ pfexec pkg install system/library/math/header-math
$ pfexec pkg install developer/library/lint
$ export PATH=/opt/gcc/4.4.4/bin:$PATH
$ pfexec pkg install compatibility/ucb
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

The non-GNU version of getopt is provided in Solaris via the `SUNWcs` package. However, xmlto needs the GNU version that supports the `--longoptions` argument.

Download [getopt](http://software.frodo.looijaard.name/getopt/download.php), extract the tarball, open the `Makefile` in an editor and make the following changes:

* Change `LIBCGETOPT` to `0`
* Change `WITHOUT_GETTEXT` to `1`
* Change `INSTALL` to `ginstall`

At this point `make` and `pfexec make install` should work, despite some suspicious compiler warnings.

### Docbook

Another easy one. Thank you to whomever builds these packages.

```
$ pfexec pkg install data/docbook
```

### Docbook2X

[Docbook2X](http://sourceforge.net/projects/docbook2x/) converts DocBook documents to man and Texinfo format which is how we get the git man pages. Before you can build it, however, you must install the `XML::SAX` Perl module.

```
$ pfexec perl -MCPAN -e 'install XML::SAX'
$ ./configure
$ make
$ pfexec make install
$ cd /usr/local/bin
$ pfexec ln -s docbook2texi docbook2x-texi
$ pfexec xmlcatalog --noout --add nextCatalog '' \
    file:///usr/local/share/docbook2X/xslt/catalog.xml --create /etc/xml/catalog
```

### xmlto

Compile and install [xmlto](https://fedorahosted.org/releases/x/m/xmlto/) by first ensuring that `/usr/local/bin` is in your `PATH` before `/usr/bin` (so it finds the `getopt` we just installed). Now the usual steps will suffice and we are nearly at our goal.

```
$ ./configure
$ make
$ pfexec make install
```

### Finally, Git's documentation

Hallelujah, we're finally ready to build Git's documentation! Just one little modification to a makefile and we're ready to go. Edit the `Documentation/Makefile` and add `--skip-validation` to `XMLTO_EXTRA`; mine had trouble fetching the Docbook DTD for some unknown reason.

And now to build the docs. Prepare to wait a while, it is a slow process.

```
$ make doc info
$ pfexec make install-doc install-html install-info
```

Now you can type `git help checkout` and finally get the man page. Woohoo!
