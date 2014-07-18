---
author: nlfiedler
comments: true
date: 2009-07-13 14:58:23+00:00
layout: post
slug: building-a-nas-box
title: Building a Network Attached Storage box
wordpress_id: 103
tags:
- drobo
- nas
- opensolaris
- zfs
---

## Introduction


In an earlier post I compared a Drobo to a custom built storage system based on OpenSolaris, with the conclusion that while a Drobo is convenient, building your own server offers many advantages. In this post I want to show how I went about building a new storage system based on an [article](http://blogs.sun.com/mebius/entry/diy_home_nas_box_with2) that came out at the end of last year. Among the improvements over my old server are lower power consumption and a smaller form factor. In fact, the case that I've chosen has the same footprint as the Drobo, and only about five inches taller. In addition, this particular case has hot swappable drive bays, so disks can be replaced while the system is powered on.

Before I get into the particulars, I want to share some useful links that may help if you're new to OpenSolaris and/or building a system from parts. First of all, there's the [First-Timer's Guide to Building a Computer from Scratch](http://lifehacker.com/5151369/the-first+timers-guide-to-building-a-computer-from-scratch) at LifeHacker. I find that the hardest part of building your own system is getting the parts list right. To that end, check out [Ars Technica's excellent system buyer's guide](http://arstechnica.com/hardware/guides/2008/09/guide-200809.ars), which offers recipes for various types of systems and provides advice on choosing suitable parts. Once the system is put together you might be asking yourself which operating system to choose. For me, [OpenSolaris](http://opensolaris.com/) with [ZFS](http://opensolaris.org/os/community/zfs/) is a no-brainer -- it really is an excellent software stack that gives me piece of mind knowing my that data is as safe as it can be (for the amount of money I'm willing to spend). But you don't have to take my word for it, see what [Simon](http://breden.org.uk/2008/03/02/a-home-fileserver-using-zfs/) and [Scott](http://scottstuff.net/blog/articles/2007/10/19/why-not-linux-new-server-part-2) have to say about OpenSolaris and ZFS. If setting up and administering OpenSolaris is too daunting for you, then check out [FreeNAS](http://freenas.org/) -- it's based on [FreeBSD](http://www.freebsd.org/) with a port of ZFS and is designed to be easier to set up and maintain.


## Power Consumption


One of the primary goals with building a new storage server was to minimize power consumption. According to a recently purchased Kill-A-Watt device, my original web server box consumes about 100W while idle, and 120W while the disks are active. During power-on, the consumption peaks at around 260W. This is actually not too bad for a server-class machine. As for the new server, it starts off at around 36W, then hits 117 when everything spins up at once, then levels out at 62. During active reading and writing, the consumption peaks at 71W and typically hovers around 67W. Not too bad, around half of the consumption of my original machine.


## Bill of Materials


These prices are based on what was available in March of 2009, so these may have changed by now. Nonetheless, it shows just how little there is to buy, with half of the parts available from a single retailer.
<table width="100%" border="0" summary="bom" >
<tbody >
<tr >

<td >Chenbro ES34069
</td>

<td >eWiz.com
</td>

<td >$153
</td>
</tr>
<tr >

<td >Intel D945GCLF2
</td>

<td >newegg.com
</td>

<td >$79
</td>
</tr>
<tr >

<td >Kingston 2GB RAM
</td>

<td >newegg.com
</td>

<td >$22
</td>
</tr>
<tr >

<td >Panasonic CD/DVD-ROM
</td>

<td >logicsupply.com
</td>

<td >$51
</td>
</tr>
<tr >

<td >Seagate 80GB 2.5" HDD
</td>

<td >newegg.com
</td>

<td >$50
</td>
</tr>
<tr >

<td >SYBA SATA II NCQ
</td>

<td >eforcity.com
</td>

<td >$43
</td>
</tr>
<tr >

<td >Flexible PCI riser
</td>

<td >logicsupply.com
</td>

<td >$22
</td>
</tr>
</tbody>
</table>
That adds up to $420 for everything you need to equal a Drobo and DroboShare. That's $150 less than the price of the Drobo plus DroboShare on Amazon, and this system is faster and the components can be sourced from multiple vendors. That, by the way, was one of my goals in building a new system; I didn't want to be locked in to a particular hardware vendor.

Regarding the selection of the Chenbro case, it was a luxury. It's rather pricey for an ITX case, but the [LogicSupply review](http://www.logicsupply.com/blog/2008/11/05/the-chenbro-es34069-case-review-part-2-the-perfect-mainboard/) convinced me it was worth it. Another option would be to use the Enlight PR-42A1 which can be had for around $60. The major difference is the Enlight is an ATX case, so you would have to get a different mainboard, and chances are it would not have a low power chipset.

You may have noticed that the mainboard does not support ECC (error-checking and correcting) memory. Yes, that was a bit of a let down, but to get ECC you typically have to go with server-grade parts, which cost more and often consume more power.


## Operating System


Unlike with pre-built systems, you get to choose your operating system when building your own system. There are many people who go with Windows Home Server, but obviously that costs money. Free options include FreeBSD, FreeNAS, Linux and OpenSolaris. Being a long time user of Solaris, I went with OpenSolaris. It has the advantage of including the reference implementation of ZFS. While I had been a Linux user for 10 years, and ran a software project/web server on Linux for half that time, I know a better solution when I see one.


## Disk Performance


Given that this system was built to replace a Drobo, I wanted to compare their performance. However, there is no reasonable way to compare the performance of a Drobo with a storage server such as the one I've built here. Firstly, the Drobo does not have native Ethernet, so it has to rely on an external device connected over USB. That alone is going to add delay and turn the comparison into an apples and oranges argument. What I can say though is that the new server feels faster than the Drobo, and is certainly fast enough for my needs. It is on par with the server-grade equipment that this box is replacing, and meets our file sharing and Time Machine backup requirements perfectly.


## Conclusion


If you're new to building a storage server, I hope that I've given you some inspiration to learn more. If you have the time and a handful of tools, building your own system is pretty easy. And if you haven't spent a lot of time learning how to configure an operating system, then let [FreeNAS](http://www.youtube.com/watch?v=16v4jNYH0GI) come to your aid. In short, you can choose your own parts from any of a number of suppliers, install whatever operating system you like, and set up a pretty reliable vault for your data.

**Update**: Check out the [media storage system](http://blogs.sun.com/drapeau/entry/draft_2_my_home_media) George Drapeau built based on OpenSolaris and ZFS.
