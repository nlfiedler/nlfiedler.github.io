---
author: nlfiedler
comments: true
date: 2013-05-12 14:13:09+00:00
layout: post
slug: symbolic-links-with-trailing-newlines
title: Symbolic links with trailing newlines?
wordpress_id: 279
tags:
- symlinks
- macosx
---

Recently I encountered a weird situation where the software we were developing was producing symbolic links that didn't link.

{% highlight sh %}
$ ls -l
total 40
-rw-r--r--  1 nfiedler  staff  101 Feb 27 13:53 a1
-rw-r--r--  1 nfiedler  staff  101 Feb 27 13:53 a2
-rw-r--r--  1 nfiedler  staff  101 Feb 27 13:53 a3
lrwxr-xr-x  1 nfiedler  staff    3 Feb 27 13:53 link1 -> a1?
lrwxr-xr-x  1 nfiedler  staff    2 Feb 27 13:58 link2 -> a1
{% endhighlight %}

Right, so the length is different, and the link apparently points to something that starts with "a1", but what's going on? I used the `readlink` command to dump the contents of the two links.

{% highlight sh %}
$ readlink link1
a1
$ readlink link2
a1
{% endhighlight %}

Uh, yeah, so what gives? I read through the readlink man page and found the `-n` option, which tells `readlink` not to print a newline after the link contents. Lo and behold, there was now indeed a difference. To be precisely sure what the difference was, I dumped the `readlink` output to a file and ran `hexdump -C` on that.

{% highlight sh %}
$ readlink -n link1 > foo1
$ hexdump -C foo1
00000000  61 31 0a                                          |a1.|
00000003

$ readlink -n link2 > foo2
$ hexdump -C foo2
00000000  61 31                                            |a1|
00000002
{% endhighlight %}

Finally, now we can see it was indeed a newline at the end of the symbolic link. Don't ask how that got there, that requires a whole 'nother story.
