---
author: nlfiedler
comments: true
date: 2009-07-18 15:35:24+00:00
layout: post
slug: mirroring-zfs-root-pool-with-messy-disks
title: Mirroring ZFS root pool with messy disks
wordpress_id: 121
tags:
- opensolaris
- zfs
---

Recently I needed to re-purpose my old [OpenSolaris](http://opensolaris.org/)-based file server as a development box (upgraded to [AMD Phenom X4](http://www.amd.com/us/products/desktop/processors/phenom/Pages/AMD-phenom-processor-X4-features.aspx), woohoo!). Since I wasn't planning on making backups on reliable schedule, I wanted to mirror the boot disk, which in [ZFS](http://opensolaris.org/os/community/zfs) is a cinch. Surprisingly this took much longer than I had assumed when reading the ZFS documentation. Mostly this was due to the messy disks I was using, that is, they had been configured as data disks so they all had an EFI label. Since most PC BIOS's don't support EFI labels, ZFS requires that all rpool devices have an SMI label. What's more, the partition table has to be just right, and I couldn't figure out how to use the format command to achieve that. Turns out there's an easier way.



	
  1. Install OpenSolaris on the first disk as usual.

	
  2. Make sure the second disk has an SMI label instead of an EFI label so it can be attached to the rpool. Invoke the `format -e` command (the -e is important, it enables expert options like setting the label type), choose the second disk, type  "label", and choose the SMI option. If it does not allow this change because you have to delete the partitions, then type "fdisk" and delete the partitions and create a new one (Solaris2 type), save the changes to disk, and now you can change the label type. Type "quit" to save your changes to disk. [[opensolaris.org](http://www.opensolaris.org/jive/thread.jspa?messageID=379421)]

	
  3. The partition table of the second disk needs to be made identical to the first one. As the root user, type `prtvtoc /dev/rdsk/disk1 | fmthard -s - /dev/rdsk/disk2` where disk1 and disk2 might look like `c0d0s2` and `c0d1s2` (note the use of slice 2 here, the "whole disk" slice). [[opensolaris.org](http://mail.opensolaris.org/pipermail/zfs-code/2009-May/000843.html)]

	
  4. Attach the second disk to the root pool: `zpool attach rpool disk1 disk2` (where `disk1` and `disk2` are the device names, typically including the slice, such as `c7d0s0` and `c7d1s0`). You may find it necessary to force the attach as ZFS may complain about slices overlapping. If `s0` overlaps `s2` that's actually normal, so just add the `-f` flag.

	
  5. Make sure the boot loader is copied to the second disk so it is bootable in the event the first disk becomes unbootable: `installgrub /boot/grub/stage1 /boot/grub/stage2 /dev/rdsk/disk2` [[opensolaris.org](http://opensolaris.org/jive/thread.jspa?messageID=357125)]


See, it was a cinch after all. That is, once you knew what to do.

[Update: Removed the remark about using s0 or s2 interchangably. So far the posts I've seen mostly lean toward s2, but the prtvtoc output for s0 and s2 on my disks were identical, so it would not have made a difference in my case. Nonetheless, go with the flow. Also added references to original sources in case you were to think I was some kind of genius or something.]
