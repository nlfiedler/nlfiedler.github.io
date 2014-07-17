---
layout: post
title: Installing Nokogiri on Mavericks
---

# Notes on Nokogiri on Mavericks

## Well Known Problem

If you search the nokogiri-talk list [^3] you will find many people have the same issue. See also issue [#1111](https://github.com/sparklemotion/nokogiri/issues/1111) on GitHub.

## Homebrew

Many people are already using Homebrew and still having problems, so that is _not_ the answer. The problem seems to be a compiler issue.

## Included Libraries

Many of the libraries are already installed on Mavericks with Xcode, no need to install anything new. More than that, it appears that Nokogiri ships with its own versions of libxml2 and libxslt, so all the fuss over those seems unwarranted.

## Exactly my problem

http://stackoverflow.com/questions/24735534/nokogiri-install-fails-iconv-doesnt-work-osx
https://github.com/rails-sqlserver/tiny_tds/issues/104
http://stackoverflow.com/questions/23550897/nokogiri-not-installing-in-ror-with-rvm-freebsd

## Install Xcode Commandline Tools

Good idea, but made no difference.

```
$ xcode-select --install
```

## Ruby mkmf is broken

Looks like Ruby's `mkmf` has some issues detecting the necessary functions on the Mac [^1]. Probably something to do with compiler flags. I'd love to know the real answer, but I haven't coded in C for a very long time.

```
$ irb
irb(main):001:0> require "mkmf"
=> true
irb(main):002:0> have_library('iconv')
checking for main() in -liconv... yes
=> true
irb(main):003:0> have_func('iconv_open', '/usr/include/iconv.h')
checking for iconv_open() in /usr/include/iconv.h... no
=> false
irb(main):004:0> have_func('iconv_open', '/usr/local/include/iconv.h')
checking for iconv_open() in /usr/local/include/iconv.h... no
=> false
```

Contents of those header files:

```
$ grep iconv_open /usr/include/iconv.h
iconv_t iconv_open (const char* /*tocode*/, const char* /*fromcode*/);
```

```
$ grep iconv_open /usr/local/include/iconv.h
#define iconv_open libiconv_open
extern iconv_t iconv_open (const char* tocode, const char* fromcode);
#define iconv_open_into libiconv_open_into
extern int iconv_open_into (const char* tocode, const char* fromcode,
```

It very well _is_ there, but mkmf is failing because the number of arguments is incorrect.

## Try older Xcode tools

One of the answers to a similar question [^2] suggested using an older version of the Apple Developer tools. As the poster said, I tried the late October 2013 tools. That, however, did not make any difference.

## Try older Nokogiri

For now, at least, can get Nokogiri installed by selecting an older version. This does _not_ solve the problem, merely postpones it for as long as I can stick to this version.

```
$ sudo gem install nokogiri -v '1.6.1'
```

[^1]: https://www.ruby-forum.com/topic/1354865
[^2]: http://stackoverflow.com/questions/19643153/error-to-install-nokogiri-on-osx-10-9-maverick
[^3]: https://groups.google.com/forum/#!searchin/nokogiri-talk/libiconv
