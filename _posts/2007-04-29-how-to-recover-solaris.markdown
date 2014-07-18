---
author: nlfiedler
comments: true
date: 2007-04-29 01:22:00+00:00
layout: post
slug: how-to-recover-solaris
title: How to Recover Solaris
wordpress_id: 36
tags:
- maintenance
- recovery
- solaris
- windows
---

Previously I mentioned the (in)servicability of Windows XP. Now what about other operating systems, such as Solaris? I have an OpenSolaris installation at the office, and the other day I installed some packages from Blastwave. Everything was fine until I logged out of GNOME and tried to log in again the next day. The logging in progress window would appear, then a few seconds later the screen would blank and return to the login screen.

   

Thankfully Solaris, like many Unix-based operating systems, has more than one way to log in. There is an option in the login screen to go to a command prompt login, where you don’t have X Windows running, just a naked command shell. It ain’t much, but it makes it possible to fix all sorts of problems. From there I could see a core file in my home directory. At first I tried to use gdb, but it didn’t understand the file format. Then I remembered adb, which has been replaced by mdb, the module debugger. This quickly revealed that GNOME was having an issue with a font cache, and by perusing the man pages for fc-cache, I saw that restarting the service should fix the problem. Tada! Problem solved. Just took 30 minutes of messing around to discover that. Compare that with hours of reinstalling the operating system and setting it up again. Yay for servicability!

   

Likewise, Linux has the same degree of servicability. Even if you can’t log in at all, you can boot from the install disk and go to “rescue” mode, which is as good as logging in to the system. Solaris has this, too, ever since one of the version 10 updates.

   

Windows XP, on the other hand, has a “Recovery Console”. I’ve attempted to use it a few times, but have never been successful. It is far too limited to be of much use. If you get a memory dump from a crashed program, you can’t examine it from the command line, so what good is it? If you can’t get Windows to boot or let you log in, you are basically out of luck. Re-install.

   

Am I glad I evicted Windows from my life? Oh hell yeah.
