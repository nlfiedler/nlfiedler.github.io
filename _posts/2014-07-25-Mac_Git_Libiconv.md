---
layout: post
title: Building Git on Mac missing libiconv
---

Recently I had attempted to install one or another plugin for [Vagrant](http://www.vagrantup.com) and ran into some issues with `libiconv` that I had trouble resolving. At one point I compiled and installed the latest version in `/usr/local`. And with the default installation in `/usr/lib`, I had two different versions of iconv installed. And despite this, the Ruby gem that was required for the Vagrant plugin still could not find the library. Argh!

```
$ mdfind -name libiconv | sort
...
/usr/lib/libiconv.2.dylib
/usr/local/lib/libiconv.2.dylib
/usr/local/lib/libiconv.la
...
```

The fallout from this mess was I found that I had trouble building Git from source. If I understand correctly, when compiling gettext (in order to satisfy requirements for building the Git documentation) it tends to find the new iconv binary but the old iconv library. To get around this, I would build gettext like so:

```
$ ./configure --with-iconv=/usr/local
$ make
$ sudo make install
```

And then I would try to build git...

```
$ ./configure --with-iconv=/usr/local
$ make
$ sudo make install
```

Now failed with another linker error like the one below:

```
...
    LINK git-daemon
Undefined symbols for architecture x86_64:
  "_locale_charset", referenced from:
      _git_setup_gettext in libgit.a(gettext.o)
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
make: *** [git-daemon] Error 1
```

To resolve this issue, simply reconfigure git again with `LDFLAGS` set to that the compiler can find the libraries in `/usr/local`, like so:

```
$ make distclean
$ autoconf
$ LDFLAGS='-L/usr/local/lib' ./configure --with-iconv=/usr/local
$ make
$ sudo make install
```

Et voila! And now if you like, you can build the docs for Git as well; see my earlier blog post on that rather long and tedious process.
