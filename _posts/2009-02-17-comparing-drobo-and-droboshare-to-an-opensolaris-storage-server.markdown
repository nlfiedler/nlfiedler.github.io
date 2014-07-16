---
author: nlfiedler
comments: true
date: 2009-02-17 07:32:00+00:00
layout: post
slug: comparing-drobo-and-droboshare-to-an-opensolaris-storage-server
title: Comparing Drobo and DroboShare to an OpenSolaris storage server
wordpress_id: 72
categories:
- Computing
- Storage
tags:
- drobo
- opensolaris
- time machine
- zfs
---

In my previous entry I recalled the trial of recovering from a corrupted HFS+J volume that contained my Time Machine backups. During that process, I became aware of some of the drawbacks of the storage appliance on which my backups were stored, the [Drobo](http://www.drobo.com/). During all of that intensive reading and writing of large chunks of data, it became obvious that the Drobo was a bit on the slow side. Meanwhile, I had come across a [video tutorial](http://blogs.sun.com/storage/entry/video_the_utlimate_zfs_tutorial) of [ZFS](http://en.wikipedia.org/wiki/Zfs), the file system that [Sun](http://www.sun.com/) created a few years back. It was so impressive, I decided then and there that the solution was to replace the Drobo with a server running [OpenSolaris](http://www.opensolaris.com/) and ZFS. Granted, there are some advantages to an appliance like the Drobo, and in this entry I'll describe the pluses and minuses as I see them. [Update: if you want a proper review of the Drobo, rather than my take on home storage solutions, check out [George Ou](http://blogs.zdnet.com/Ou/?p=508)'s balanced analysis, as well as [Marcel Binder](http://www.tomshardware.com/reviews/meet-drobo,1633.html)'s review on [Tom's Hardware](http://www.tomshardware.com/). They are reviewing the first generation Drobo, which is what I am discussing here.]

Drobo Advantages
It's an appliance, you plug it in and stick in the drives and that's basically all you need to do. In fact, the disks do not even have to be the same size, although if they are then it won't waste any of the capacity. With four 200GB drives, the Drobo provided 554GB of usable storage space. And it does this while consuming a modest 36W of power.

Drobo Disadvantages
While the list of advantages was rather brief, this list is going to be significantly longer. First, if you are using a Drobo, you almost certainly want to share it on a LAN, in which case you need to buy a DroboShare (another $200 on top of the $500 for the empty Drobo, plus whatever you had to pay for the disks). To make this work properly, you need to run the Drobo Dashboard, which has a couple of idiosyncrasies. First, you have to completely disable the firewall in Mac OS X in order for the Dashboard to detect the DroboShare. Second, the Dashboard application files must be owned by a particular user, the one that installed the software. That would not be a problem except that the application won't launch for any other user. While the DroboShare can be mounted without using the Dashboard, it seems there is a file ownership issue with at least some of the files on the Drobo, making them appear to be corrupted. The Drobo support tech was stumped and unable to resolve that particular issue, and could not bring themselves to admit that the Dashboard software was flawed.

During all of that Time Machine volume copying I was doing, the used space on the Drobo climbed upward. But as I deleted the botched copies, the Drobo was reluctant to return the space to the free side of the usage dial. Weeks went by and mysteriously one day the space all came back. I'll never know why, just one of the mysteries of the black box that is the Drobo. In fact, that is exactly what the Drobo is: a closed, black box. There is no access into it, no remedy when something goes wrong, no tool for diagnosis. If the box fails for any reason, you have exactly one place to turn to for help (if you're [Bill Streeter](http://billstreeter.net/2008/03/18/do-not-buy-a-drobo/) or [Jan Rychter](http://jan.rychter.com/enblog/2010/6/18/drobo-and-droboshare-a-review.html), you'll know exactly how true that is). You better hope your support contract is still good ($50/year, $150 if you let it lapse). In the worst case, you have to buy a new Drobo just so you can get the data off of your disks. Yes, that is indeed the truth. Like any proprietary RAID-like device, the Drobo's on-disk format is a closely guarded secret. If the device fails and you can't find a replacement, your data is gone forever.

While its power consumption is modest, it has insufficient airflow around the disks and as a result they run a bit too hot for my taste. Now that they are in the new storage server, they are much cooler. Speaking of power, the power connector on the Drobo is notoriously loose. It's fallen out numerous times over the year that I've used my Drobo. The tech support person suggested taping the power cord to the side of the Drobo. Brilliant.

Last, but not least, the Drobo has a weird capacity "limit" of 2TB. Apparently it has something to do with the fact that it's only interface to the world is through a USB port. I would assume the second generation Drobo is better at this, but I'm not willing to spend $500 to find out. Regardless, your capacity is limited to whatever you can find in four disks, as that is the maximum number of disks any single Drobo can take. But, if you've got the money, you can plug two Drobos into a single Drobo Share. I doubt too many people have done that, as it would cost $1200 plus the cost of the disks. Meanwhile, I could build a system to hold 10+ disks for a fraction of that cost.

[Update: one more disadvantage, in case there weren't enough. With the Drobo and DroboShare, you have to have a paid support plan to get the firmware updates. So you better hope they find all the bugs before your support contract expires. With OpenSolaris, updates are free and new releases are made about twice a year.]

OpenSolaris and ZFS
The only disadvantage to running a server with OpenSolaris is that you have to install, configure, and maintain it. But hey, I've been doing that for years so it's no trouble for me. In fact, the recent releases of OpenSolaris are remarkably easy to set up and administer. Most of the standard configuration is done using the graphical interface, and for everything else there are well-written manual pages. As for the advantages of OpenSolaris and ZFS, there are many. First of all, ZFS is the most amazing file system on the planet. It's incredibly easy to set up a storage pool and create file systems. It handles stripes, mirrors, and data/parity formats (RAID 0, 1, 5, and 6) depending on your replication needs. You can add as many disks as your hardware can handle, and you can configure them any way you like.

ZFS has invincible data integrity. Unlike most, if not all, RAID 5 implementations, it does not suffer from the infamous [write hole](http://en.wikipedia.org/wiki/Standard_RAID_levels#RAID_5_performance). Instead, it never overwrites live data, so all writes go to free blocks, with data being written first, then the meta data blocks, and finally the über blocks. If power is lost at any point, when the system comes back, it will only see valid data and meta data. What's more, all blocks are check-summed, and that checksum is stored in the parent block. This checksum carries upward to the über block, which effectively has a fingerprint of the entire file system. This guards against the worst kind of data loss, the silent kind, as it detects the occasional bit rot that some disks can suffer. With built-in data replication, these problems can be automatically corrected on the fly.

But what about data portability, which was a major issue for me with the Drobo? Well, get this: ZFS is open source. What's more, it's been ported to BSD and Mac OS X. So even if my storage server were to suddenly die, I not only have a choice of vendors to repair/replace the hardware, but I also have a choice of operating systems to access the data in the storage pool. But, in my opinion OpenSolaris is the best choice as it has the reference implementation of ZFS. What's more, OpenSolaris supports SMB, NFS, iSCSI, and AFP (via netatalk; see earlier blog entry), so I have several choices for how to access the storage over the network.

ZFS has built-in support for snapshots. In fact, it has a feature similar to Time Machine, called Time Slider, that makes automatic snapshots and manages their expiration, much like Time Machine. With snapshots in place, if I ever run into a corrupted HFS disk image again, I can roll back the file system to an earlier snapshot. Granted, I may lose some data, but it's better than losing the entire image to an uncorrectable error.

Final Notes
I lied earlier when I said there was only one disadvantage to running a storage server instead of a Drobo. The one other issue is the power consumption of most server-class systems is over 100W. In fact, my current server consumes at least 102W and often hits 120W while actively doing work. But, I have [a plan](http://www.logicsupply.com/blog/2008/11/05/the-chenbro-es34069-case-review-part-2-the-perfect-mainboard/) to replace the hardware with low power parts, ones primarily aimed at the mobile and embedded market. I plan to talk about that more in a future entry.

While I could certainly format the Drobo using ZFS, I would only gain snapshots and on-disk consistency. [Update: the Drobo does not support ZFS, only NTFS, HFS+, and FAT32; see Drobo knowledge base article #0043. Nonetheless...] Performance would still be rather poor, and disk management and repair would rely entirely on the Drobo itself. As far as ZFS would know, the Drobo would appear as one big disk. That is not the recommended scenario for ZFS according to its creators.

Earlier I mentioned that Drobo provided 554GB of usable space with the four 200GB disks I had installed. In comparison, ZFS provided just 548GB. Not too bad considering I'm getting rock solid data integrity and automated snapshots.

One final point, in regards to reclaiming disk space after deleting files. With ZFS, the freed space was returned within seconds, unlike the many weeks that it took the Drobo to realize I had deleted 100GB of data.

All in all, I'm very happy with the decision I've made. I now have a fast, reliable, serviceable, and manageable storage box that I can update with newer software versions indefinitely, as well as easily grow the capacity as my needs change.
