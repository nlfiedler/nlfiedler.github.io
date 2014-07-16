---
author: nlfiedler
comments: true
date: 2009-02-17 01:44:00+00:00
layout: post
slug: time-machine-and-invalid-sibling-link-now-what
title: Time Machine and invalid sibling link, now what?
wordpress_id: 71
categories:
- Computing
- Scripting
- Storage
tags:
- hfs
- mac
- python
- time machine
---

Last month I was playing around with the [timedog](http://code.google.com/p/timedog/) script to determine where all the disk space was going on my Time Machine backups. I had mounted the remote disk image using hdiutil attach, when a few minutes later Time Machine kicked off a backup. In a moment of poor judgment, I tried to stop the backup and unmount the second mount of the backup volume that TM had established. At first nothing appeared to have gone wrong as a result of that action, but the next time TM tried to make a backup, it said the volume was corrupted and it couldn't make another backup. No matter, I was sure Disk Utility or fsck could correct the problem, surely the error wasn't too serious. Ah, but this error was no run of the mill error. It was in the fact the dreaded invalid sibling link error. This is the error that makes most disk repair tools turn pale and shirk away into the corner. In most cases, nothing will fix the broken link, and there's no telling just what might be lost as a result.

But, I wasn't going to give up too easily. After all, I had over a year's worth of backups that I wanted to recover. I Googled around for days, reading forum posts and blog entries, and anything else that might bring some hope to my dire situation. In many cases, others who had this problem tried fsck or Disk Warrior, and some of them were successful. By this time, I had tried fsck -r several dozen times, to no avail. Being the inventive type, I tried using hdiutil convert to create a new disk image from the corrupt one. But, as you can probably guess, all that accomplished was creating a new disk image with the same invalid sibling link. That meant that a simple disk block copy was not going to work, I had to try something that would copy the files one by one from the corrupt volume to a new one. Knowing that Time Machine makes gratuitous use of hard links, I needed a copy program that knew how to manage the hard links. Otherwise, a simple copy would result in a TM volume that was many times the size of the original.

Using rsync -H was the first thing I tried, but it ran out of memory before it managed to copy anything. Next, I tried SuperDuper!, which was recommended by quite a few people. The free version would only perform a whole disk copy, erasing the destination and starting from scratch. That was fine since that was exactly what I wanted. Sadly, it too failed after about 36 hours, reporting a "type 8 error". It had managed to copy quite a bit of the TM backups, but I wasn't satisfied, I wanted everything.

At this point I began to realize that there was only one way I was going to make a faithful copy of the Time Machine volume in a reasonable amount of time. Yep, that's right, I would have to write a script that would accomplish my goal. Being that Python is the language in which I am strongest, after Java, I chose to put its built-in file manipulation routines to good use. I figured I could use the same approach that timedog was using, comparing the inode values of the directory entries from one snapshot to the next. In this way, I could know which entries were hard links and which were new files. After just four weeks of spending my spare time hacking away in Python, I finally succeeded.

Introducing [timecopy.py](http://code.google.com/p/timedog/wiki/UsingTimecopy), the fruit of my labor. It's a Python script that traverses a Time Machine volume and reproduces its contents to the destination of your choice. Aside from knowing about the hard links that Time Machine creates, it also copies over the extended attributes that convince TM to accept the copied backups. As a result of writing this tool, I have recovered my Time Machine backups and everything seems to be working fine once again. Granted, I'll never know what files may have been lost due to the invalid sibling link error, but at least I managed to save the vast majority of the data.
