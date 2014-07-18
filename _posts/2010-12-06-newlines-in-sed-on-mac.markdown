---
author: nlfiedler
comments: true
date: 2010-12-06 00:43:47+00:00
layout: post
slug: newlines-in-sed-on-mac
title: Newlines in sed on Mac
wordpress_id: 163
tags:
- mac
- sed
---

For whatever reason, this is harder than it should have been. All I wanted to do was replace a particular expression with a newline character (`0x10`). My preference is typically to script such tasks, and the `sed` command is the perfect fit. This would be simple on just about any system, except for Mac OS X, where apparently all the standard advice is difficult to apply. Worse, the sed man page leads you to believe it's a very simple matter of putting a newline in the replacement string. Of course, there's no explanation as to how you are expected to do that. And not being a bash expert, I was at a loss.

Luckily enough I found a [blog post](http://www.culmination.org/2008/02/10/sed-on-mac-os-x-105-leopard/) that discussed, among other things, how to inject a newline using sed on Mac. Although the example is rather complicated given that he's solving a different problem, the crux of the matter is this expression: `$'\n/g'`

    $ echo 'foo bar baz quux' | sed -e 's/ /\'$'\n/g'
    foo
    bar
    baz
    quux

All that is really doing is taking advantage of the bash `extquote` option where `$'string'` quoting is performed on the enclosed string. In this case it's a `\n` which expands to a newline character, followed by `/g` which goes through as-is. The baskslash (`\`) before the `$'\n/g'` tells sed to escape the newline character in the replacement string. I'm no expert here, but my understanding is that the argument to sed consists of two parts, the `s/ /\` and the `[newline]/g`, where the latter resulted from the `$'\n/g'` evaluated by bash. Together this forms the sed expression `s/ /\[newline]/g`. How the `\'` doesn't escape the quote and go through as-is to sed I'm not sure.

In the original posting of this blog entry on Wordpress.com, commenter Юрий Пухальский suggested using the standard POSIX way, the escaped newline. I have to admit, I did not encounter that anywhere when looking for a solution, but seems a good alternative. Here's his/her example:

    echo "a b c" | sed 's/ /\
    /g'

Thanks for reading!
