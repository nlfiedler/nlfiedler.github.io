---
layout: post
title: Building Git on Mac but missing libiconv?
date: 2014-07-25 20:48:00
tags: darwin mac git libiconv compiling
comments: true
description: Building Git on Mac OS X and seemingly missing libiconv library.
---

Recently I had attempted to install one or another plugin for [Vagrant](http://www.vagrantup.com) and ran into some issues with `libiconv` that I had trouble resolving. At one point I compiled and installed the latest version in `/usr/local`. And with the default installation in `/usr/lib`, I had two different versions of iconv installed. And despite this, the Ruby gem that was required for the Vagrant plugin still could not find the library. Argh!

{% highlight sh %}
$ mdfind -name libiconv | sort
...
/usr/lib/libiconv.2.dylib
/usr/local/lib/libiconv.2.dylib
/usr/local/lib/libiconv.la
...
{% endhighlight %}

The fallout from this mess was I found that I had trouble building Git from source. If I understand correctly, when compiling gettext (in order to satisfy requirements for building the Git documentation) it tends to find the new iconv binary but the old iconv library. To get around this, I would build gettext like so:

{% highlight sh %}
$ ./configure --with-iconv=/usr/local
$ make
$ sudo make install
{% endhighlight %}

And then I would try to build git...

{% highlight sh %}
$ ./configure --with-iconv=/usr/local
$ make
$ sudo make install
{% endhighlight %}

Now failed with another linker error like the one below:

{% highlight sh %}
...
    LINK git-daemon
Undefined symbols for architecture x86_64:
  "_locale_charset", referenced from:
      _git_setup_gettext in libgit.a(gettext.o)
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
make: *** [git-daemon] Error 1
{% endhighlight %}

To resolve this issue, simply reconfigure git again with `LDFLAGS` set so that the compiler can find the libraries in `/usr/local`, like so:

{% highlight sh %}
$ make distclean
$ autoconf
$ LDFLAGS='-L/usr/local/lib' ./configure --with-iconv=/usr/local
$ make
$ sudo make install
{% endhighlight %}

Et voila! And now if you like, you can build the docs for Git as well; see my earlier blog post on that rather long and tedious process.
