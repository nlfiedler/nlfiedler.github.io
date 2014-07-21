---
layout: post
title: Building Erlang/OTP on OpenIndiana
date: 2014-07-19 23:21:03
tags:
- solaris
- openindiana
- erlang
comments: true
---

# Building Erlang on OpenIndiana

Building [Erlang](http://www.erlang.org) on [OpenIndiana](http://openindiana.org) isn't really all that difficult, but does involve a few steps, so worth documenting in order to save time in the future.

## Installing Dependencies

### Developer Tool Chain

Basic tools for compiling software on OpenIndiana:

```
$ pfexec pkg install developer/illumos-gcc system/library/math/header-math
```

### Java Development Kit

Download and install the [Java SE](http://www.oracle.com/technetwork/java/javase/downloads/) Development Kit. I choose JDK 6 since I know that version is sufficient and significantly smaller than the more recent releases. Create symbolic links in `/usr/local/bin` that point to the executables in the JDK `bin` directory (at a minimum: `java`, `javac`, `javadoc`, and `jar`); naturally `/usr/local/bin` must be in your `PATH` _before_ `/usr/bin` for this to have any effect.

### Apache FOP

Get the [binary](http://xmlgraphics.apache.org/fop/download.html) for the latest release and extract somewhere, such as `/usr/local`. As with Java, ensure a link to the `fop` binary is added to `/usr/local/bin`.

## Erlang

Once those are in place, building Erlang/OTP is easy.

```
$ ./configure
$ make
$ make docs
$ pfexec make install
$ pfexec make install-docs
```

And now you can test it by loading up a man page: `erl -man mnesia`

If that works, terrific. If not, send me email or leave a comment, and I'll see what I can do.
