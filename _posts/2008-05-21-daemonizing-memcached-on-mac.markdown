---
author: nlfiedler
comments: true
date: 2008-05-21 06:45:00+00:00
layout: post
slug: daemonizing-memcached-on-mac
title: Daemonizing memcached on Mac
wordpress_id: 65
tags:
- launchd
- mac
- memcached
---

As a follow-up to the previous entry on running Apache using the Mac OS X launchd service, here are the basic steps for daemonizing [memcached](http://www.danga.com/memcached/), the distributed memory-sensitive cache.



	
  1. Start by installing [libevent](http://www.monkey.org/%7Eprovos/libevent/): ./configure, make, sudo make install

	
  2. Install memcached in the same way: ./configure, make, sudo make install

	
  3. Create a /Library/LaunchDaemons/com.danga.memcached.plist file with the contents shown below.

	
  4. sudo launchctl load /Library/LaunchDaemons/com.danga.memcached.plist


At this point memcached will be running, listening on the default port (11211). Below is a working plist file for memcached when installed using the default options.


<blockquote><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
<key>Label</key>
<string>com.danga.memcached</string>
<key>ProgramArguments</key>
<array>
<string>/usr/local/bin/memcached</string>
<string>-d</string>
<string>-u</string>
<string>root</string>
</array>
<key>RunAtLoad</key>
<true/>
</dict>
</plist></blockquote>
