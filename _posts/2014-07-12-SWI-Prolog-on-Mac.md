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

## Lunzip

Install lunzip so that you can uncompress the GMP tarball, which is required for SWI Prolog.

```
$ tar zxf lunzip-1.5.tar.gz
$ cd lunzip-1.5
$ ./configure
$ make
$ sudo make install
```

## GMP

Install GMP, which is needed for multi-precision numbers in Prolog.

```
$ lunzip gmp-6.0.0a.tar.lz
$ tar xf gmp-6.0.0a.tar
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
