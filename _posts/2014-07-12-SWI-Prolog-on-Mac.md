---
layout: post
title:  "Building SWI Prolog on Mac"
date:   2014-07-12 20:50:36
tags:   darwin mac prolog compiling
---

# Building SWI Prolog on Mac

Getting [SWI Prolog](http://www.swi-prolog.org) to build on Mac OS X (Darwin) is no small feat. Yes, one could use the [MacPorts](http://www.macports.org) or [Homebrew](http://brew.sh) packaging systems, and that would certainly make things easier. Frankly, after using Solaris for many years, I'm burned out on third-party packaging systems. Especially when there are multiple competing systems; it makes the [Dependency hell](http://en.wikipedia.org/wiki/Dependency_hell) that much worse.

Below, I'll quickly go over each of the steps for compiling `swipl` and its dependencies. This assumes you have installed [Xcode](https://developer.apple.com/xcode/) since it tends to make compiling software from source code much easier.

## GNU readline

Install GNU readline, which is really easy so I won't go into details.

```
$ tar zxf readline-master.tar.gz
$ cd readline-master
$ ./configure
$ make
$ sudo make install
```

## GMP

Install GMP, which is needed for multi-precision numbers in Prolog.

```
$ tar jxf gmp-6.0.0a.tar.bz2
$ cd gmp-6.0.0/
$ ./configure
$ make
$ make check
$ sudo make install
```

## SWI Prolog

Compiling SWI Prolog itself on the Mac requires a few additional steps. To start with, edit the `build.templ` file at the top of the pl-x.y.z directory such that the `LIBRARY_PATH` and `CPATH` values for __Darwin__ have the `/usr/local` equivalents _before_ the default paths (I have no `/opt` to speak of, so I have removed them in the example below).

```
export LIBRARY_PATH=/usr/local/lib:/usr/lib
export CPATH=/usr/local/include:/usr/include
```

Now you are ready to build from the command line.

```
$ export LDFLAGS=-L/usr/local/lib
$ export CFLAGS=-I/usr/local/include
$ ./configure
$ make
$ sudo make install
```

## Finale!

And now to fire up SWI Prolog.

```
$ swipl
Welcome to SWI-Prolog (Multi-threaded, 64 bits, Version 6.6.6)
Copyright (c) 1990-2013 University of Amsterdam, VU Amsterdam
SWI-Prolog comes with ABSOLUTELY NO WARRANTY. This is free software,
and you are welcome to redistribute it under certain conditions.
Please visit http://www.swi-prolog.org for details.

For help, use ?- help(Topic). or ?- apropos(Word).

?-
```

Trying to quit is fun. Turns out `Ctrl-d` works, as does `halt(0).` Once you have that operational, take a look at Bernardo Pires [introduction](https://bernardopires.com/2013/10/try-logic-programming-a-gentle-introduction-to-prolog/) to Prolog.
