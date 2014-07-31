---
layout: post
title: Building PyPy on OpenIndiana
date: 2014-07-26 20:30:00
tags:
- solaris
- openindiana
- python
- pypy
comments: true
---

# Building PyPy on OpenIndiana

Building [PyPy](http://pypy.org/) on [OpenIndiana](http://openindiana.org) is a _work in progress..._

## Installing Dependencies

### Developer Tool Chain

Basic tools for compiling software on OpenIndiana. You might already have half of these, but on a bare-bones OpenIndiana install, these will be needed to compile just about anything.

~~~
$ pfexec pkg install developer/illumos-gcc
$ pfexec pkg install developer/gnu-binutils
$ pfexec pkg install system/header
$ pfexec pkg install system/library/math/header-math
$ pfexec pkg install developer/library/lint
$ export PATH=/opt/gcc/4.4.4/bin:$PATH
~~~

### Other Dependencies

bzip2-devel      => compress/bzip2
expat-devel      => library/expat
libffi-devel     => library/libffi
libgc-dev        => ???                       (Boehm garbage collector)
libsqlite3-dev   => database/sqlite-3
ncurses-devel    => system/header
openssl-devel    => library/security/openssl
pkgconfig
python-devel     => runtime/python-26
python-greenlet
python-sphinx
zlib-devel       => library/zlib
