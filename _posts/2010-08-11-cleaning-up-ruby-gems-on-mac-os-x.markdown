---
author: nlfiedler
comments: true
date: 2010-08-11 14:21:36+00:00
layout: post
slug: cleaning-up-ruby-gems-on-mac-os-x
title: Cleaning up Ruby Gems on Mac OS X
wordpress_id: 155
tags:
- gems
- mac
- ruby
---

This has come up a few times for me and searching the Internet failed to turn up any satisfying solutions. In particular, I wanted to update the Ruby Gems on my Mac and subsequently remove the old versions. I found that certain gems would not go away when the `gem cleanup` command was invoked.

    
    # gem list
    *** LOCAL GEMS ***
    actionmailer (2.3.5, 2.2.2, 1.3.6)
    actionpack (2.3.5, 2.2.2, 1.13.6)
    actionwebservice (1.2.6)
    activerecord (2.3.5, 2.2.2, 1.15.6)
    ...
    # gem cleanup
    Cleaning up installed gems...
    Attempting to uninstall fastthread-1.0.1
    Unable to uninstall fastthread-1.0.1:
    	Gem::InstallError: cannot uninstall, check `gem list -d fastthread`
    ...


It seems that (Snow) Leopard comes bundled with a plethora of Ruby gems, all of which are installed in `/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/lib/ruby/gems/1.8`. Meanwhile, the `gem install` command puts new gems in `/Library/Ruby/Gems/1.8`, as shown with the `-d` option to `gem list`:

    
    # gem list -d fastthread
    *** LOCAL GEMS ***
    fastthread (1.0.7, 1.0.1)
        Author: MenTaLguY
        Rubyforge: http://rubyforge.org/projects/mongrel
        Installed at (1.0.7): /Library/Ruby/Gems/1.8
                     (1.0.1): /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/lib/ruby/gems/1.8
        Optimized replacement for thread.rb primitives


Some folks have said to simply leave the old gems in place, while others have suggested deleting the old gems from the `/System/Library` location using `rm`. Neither solution is all that appealing to me.

The solution I was looking for, it seems, is rather simple and involves nothing more than setting the `GEM_HOME` environment variable before running the `gem cleanup` command.

    
    # export GEM_HOME=/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/lib/ruby/gems/1.8
    # gem cleanup
    ...
    Attempting to uninstall rake-0.8.3
    Executables and scripts will remain installed.
    Successfully uninstalled rake-0.8.3
    Clean Up Complete
    # unset GEM_HOME


And that will do it, all of the outdated gems are removed and I didn't have to perform file system surgery. Yay!
