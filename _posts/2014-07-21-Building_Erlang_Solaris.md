---
layout: post
title: Building Erlang/OTP on OpenIndiana
date: 2014-07-19 23:21:03
tags:
- solaris
- openindiana
- erlang
comments: true
description: Building Erlang/OTP from source on OpenIndiana using mostly system packages.
---

Building [Erlang](http://www.erlang.org) on [OpenIndiana](http://openindiana.org) isn't really all that difficult, but does involve a few steps, so worth documenting in order to save time in the future. Why build Erlang? Because it [rocks!](http://erlangotp.com)

_Update_: A [Fabric](http://www.fabfile.org) [script](https://github.com/nlfiedler/tanuki/blob/master/vagrant/fabfile.py) for performing everything you see here has been written. If you like using Fabric, feel free to copy code as you see fit. One difference is that the `fabfile.py` does not copy Apache FOP anywhere, it simply uses it temporarily to perform the build.

## Installing Dependencies

### Developer Tool Chain

Basic tools for compiling software on OpenIndiana. You might already have half of these, but on a bare-bones OpenIndiana install, these will be needed to compile just about anything.

{% highlight sh %}
$ pfexec pkg install developer/illumos-gcc
$ pfexec pkg install developer/gnu-binutils
$ pfexec pkg install system/header
$ pfexec pkg install system/library/math/header-math
$ pfexec pkg install developer/library/lint
$ export PATH=/opt/gcc/4.4.4/bin:$PATH
{% endhighlight %}

### Java Development Kit

For OpenIndiana, the provided JDK package will suffice.

{% highlight sh %}
$ pfexec pkg install developer/java/jdk
{% endhighlight %}

### Apache FOP

Get the [binary](http://xmlgraphics.apache.org/fop/download.html) for the latest release and extract somewhere, such as `/opt`. Be sure to add the `fop` binary to your `PATH`, perhaps by adding a symlink from `/usr/local/bin` (just as an example).

## Erlang

Once those are in place, building Erlang/OTP is easy.

{% highlight sh %}
$ ./configure
$ make
$ export FOP_OPTS="-Xmx512m"
$ make docs
$ pfexec make install
$ pfexec make install-docs
{% endhighlight %}

And now to test it out:

{% highlight sh %}
$ erl +V
Erlang (ASYNC_THREADS,HIPE) (BEAM) emulator version 6.1
{% endhighlight %}

If that works, terrific. If not, send me email or leave a comment, and I'll see what I can do.
