---
author: nlfiedler
comments: true
date: 2008-05-15 16:28:00+00:00
layout: post
slug: starting-apache-on-mac-os-x
title: Starting Apache on Mac OS X
wordpress_id: 64
categories:
- Computing
- HowTo
tags:
- apache
- launchd
- mac
---

For the last year I've been using an ugly hack to have my custom compiled Apache started at bootup on my Mac OS X systems. Basically, and this is really bad, I would replace the /usr/sbin/httpd binary with a symbolic link to the version I compiled. Why is that bad, you might ask? Because if Apple ever updates their installed Apache, it will probably overwrite my version of httpd. Thanks to Google, I recently found a much better way, and wanted to document it as a blog entry all its own, rather than as a comment or sidenote to something else.  
  
Say you've compiled your version of Apache and it's now installed in the default location of /usr/local/apache2, and let's say you want it to be started at bootup, effectively replacing the version of Apache that comes with Mac OS X. Here are the simplest steps I know of to do this, and without using a single hack.  


  1. Before doing anything else, turn off the Web Sharing item in the Sharing panel of System Preferences, since in a few minutes, you'll be running your own Apache instance.
  2. Create the file /Library/LaunchDaemons/org.apache.httpd.plist with the contents shown below, using your favorite editor (e.g. emacs, nano, vi).
  3. Use the launchd service to manage the Apache instance by invoking the command: sudo launchctl load -w /Library/LaunchDaemons/org.apache.httpd.plist
And that will do it. You now have a managed Apache instance of your own creation, and it should be running on the configured port (e.g. 80). Now just make a request and check the logs to make sure it's really working as you expected.  
  
Here's the plist file mentioned in the steps above:  


<blockquote><?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">  
<plist version="1.0">  
<dict>  
  <key>Label</key>  
  <string>org.apache.httpd</string>  
  <key>ProgramArguments</key>  
  <array>  
    <string>/usr/local/apache2/bin/httpd</string>  
    <string>-k</string>  
    <string>start</string>  
  </array>  
  <key>RunAtLoad</key>  
  <true/>  
</dict>  
</plist></blockquote>

If you ever want to turn off your version of Apache, you can invoke the launchctl command shown above, replacing "load" with "unload". In the mean time, you can manage the Apache instance using the apachectl command as usual (e.g. /usr/local/apache2/bin/apachectl graceful).  

