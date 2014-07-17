---
layout: post
title: Installing Nokogiri on Mac OS X
---

# Installing Nokogiri on Mac OS X

## OpenSSL

Installing Ruby gems requires having a build of Ruby with SSL support. [OpenSSL](http://www.openssl.org) works well enough. I configured it for my system thusly.

```
$ ./Configure darwin64-x86_64-cc --prefix=/usr/local --openssldir=/usr/local/openssl
$ make
$ make test
$ sudo make install
```

## Ruby

Build and install a version of [Ruby](https://www.ruby-lang.org/en/) later than 1.9.3 (I used 2.1.x) in order to satisfy Nokogiri's requirements (or was it Jekyll's?).

```
$ ./configure --with-openssl
$ make
$ sudo make install
```

## Fixing libz.dylib

This is the all-important step that is so completely non-intuitive it requires sheer genius to discover. Thanks to Sheldon Finlay for figuring this out and sharing in his [miracle post](http://www.refresherate.com/2010/01/08/fixing-ld-warning-in-usrlocalliblibz-dylib-file-is-not-of-required-architecture/), so named by a contributor of [StackOverflow](http://stackoverflow.com/questions/5528839/why-does-installing-nokogiri-on-mac-os-fail-with-libiconv-is-missing). Note that I am removing the `libz.dylib` file since it is merely a symbolic link to the actual library file. The replacement file depends on your version of Mac OS X, so take care to find the correct one.

```
$ sw_vers -productVersion
10.7.5
$ find -x / -name libz.dylib
...
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.7.sdk/usr/lib/libz.dylib
...
$ cd /usr/lib
$ sudo rm libz.dylib
$ sudo cp /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.7.sdk/usr/lib/libz.dylib .
```

## Installing Nokogiri

TODO
