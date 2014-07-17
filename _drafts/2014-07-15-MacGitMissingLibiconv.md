---
layout: post
title: Building Git on Mac missing libiconv
---

# Git and missing libiconv

```
$ sudo find -x / -name 'libiconv*' -print
/Applications/...
/usr/lib/libiconv.2.4.0.dylib
/usr/lib/libiconv.2.dylib
/usr/lib/libiconv.dylib
/usr/local/lib/libiconv.2.dylib
/usr/local/lib/libiconv.dylib
/usr/local/lib/libiconv.la
/usr/local/share/...
```

## Building Git

Install a recent version of libiconv to `/usr/local`, then...

When building gettext, use the following:

```
$ ./configure --with-iconv=/usr/local
$ make
$ sudo make install
```

And then build git...

```
$ ./configure --with-iconv=/usr/local
$ make
$ sudo make install
```

## Replacing or Removing libiconv

Must update the software compiled using libiconv if moving or removing the files.

* nokogiri
* git
