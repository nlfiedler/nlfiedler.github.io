---
author: nlfiedler
comments: true
date: 2010-08-09 14:42:01+00:00
layout: post
slug: using-opensolaris-and-zfs-with-time-machine
title: Using OpenSolaris and ZFS with Time Machine
wordpress_id: 96
tags:
- mac
- opensolaris
- time machine
- zfs
---

In earlier posts I described how to set up a [file server](http://cafenate.wordpress.com/2009/07/13/building-a-nas-box/) using OpenSolaris and ZFS. This proved to be a very good choice, at least for me, and it made sense to make this file server be the destination for my Mac's Time Machine backups. In this post I'll try to cover everything I did to make that possible.

Assuming you've got the hardware set up and the operating system is in place, it's time to set up the ZFS pool and file system for the Time Machine backups. The values shown below will almost certainly differ for your system, so use `pfexec format` to get a list of disk device names; look for those that are not part of a zpool already (use `zpool status` to see which devices are in which pools).

    
    $ pfexec bash
    # zpool create yubaba raidz c4t0d0 c4t1d0 c5t0d0 c5t1d0
    # zfs create yubaba/mac_backup
    # zfs set compression=on yubaba/mac_backup
    # zfs set com.sun:auto-snapshot=true yubaba/mac_backup


I chose to use RAID-Z (striping with parity) so as to maximize my disk space while still getting some redundancy. The two `zfs set` commands are used to enable ZFS supported features for the new file system. The first turns on compression, which if your system is fast enough, is probably a good idea. The second enables the automatic snapshots that OpenSolaris makes possible via a feature called Time Slider. This is a good way to roll back unwanted changes if the need arises.

Now that the file system is ready, set up the [netatalk](http://cafenate.wordpress.com/2009/02/08/building-netatalk-on-opensolaris-200811/) daemon so your Mac can talk to the file server. There are other options (e.g. NFS) but this seems the most straightforward approach to me. With netatalk in place, add the following line to the `/usr/local/etc/netatalk/AppleVolumes.default` file so that the backup volume is mountable on the Mac, then restart the netatalk daemon.

    
    /yubaba/mac_backup "Mac Backup" allow:@staff cnidscheme:dbd options:usedots,invisibledots,upriv perm:0770


By default, earlier versions of Mac OS X do not allow unsupported servers to be the destination for Time Machine backups. To convince it otherwise, simply run the following command in Terminal. This is only necessary the first time, as the change is saved permanently.

    
    $ defaults write com.apple.systempreferences TMShowUnsupportedNetworkVolumes 1


The next step is to create the sparse bundle disk image on the server. If simply pointing Time Machine to the backup server doesn't work (i.e. it reports that it could not create the backup disk image), then you will have to create the disk image manually and copy it over to the server. There is a procedure that makes good use of the graphical utilities in OS X, outlined on [kremalicious](http://www.kremalicious.com/2008/06/ubuntu-as-mac-file-server-and-time-machine-volume/), but since we already have the **Terminal** open, let's do it from the command line. After all, if you're a Solaris fan you probably won't mind seeing how this is really done.

Time Machine puts some cryptic pieces of information into the disk image name that it creates on any remote device, be it Time Capsule or our storage server. These are nothing more than the MAC address and the host name, and can be gathered with the following commands.

    
    $ hostname -s


This command shows the host name of your Mac in shortened form, which is the first piece of information.

    
    $ ifconfig en0 | awk '/ether/{gsub(/:/, "", $2); print $2}'


This command sequence does nothing more than return the MAC address in the format used by Time Machine. You could get this from Network Utility, but then you'd have to type the numbers by hand.

Now we can create the sparse bundle disk image in which Time Machine will store the backups. This is done with a single, albeit long, command. Where you see the "300g" below, feel free to replace that with whatever size specification you like. The 'g' stands for gigabytes and the 300 indicates the number of gigabytes. Obviously this is a sparse bundle so it won't immediately consume 300GB from the start, that's just the maximum size to which the image will grow.

    
    $ sudo hdiutil create -nospotlight -type SPARSEBUNDLE -imagekey sparse-band-size=131072 -size 300g -fs "HFS+J" -volname "MacBackup" hostname_MACADDR.sparsebundle


That's a rather long command, so let's take a look at each part. First, the `hdiutil` command is used to manage disk images of all sorts, and it has a subcommand called `create` that will create just about any kind of image you could want. The `-nospotlight` option simply sets an attribute that tells Spotlight to ignore this volume. The `-type` option is obvious, but the `-imagekey` is a bit mysterious. That sets up the disk image to use larger files for the "bands" that make up the sparse bundle. According to this [discussion](http://nslog.com/2008/11/11/formatting_the_drobo_for_time_machine_backups), Jon Stevens (latchkey) says the larger the band size the better the performance over the network. The `-size` was alluded to above; use whatever value you like but it should at least be as large as your OS X system disk. The `-fs` option indicates the type of file system to use within the disk image, in this case it's HFS+ with journaling.

The last parts of the command are machine specific and you will need to change their values accordingly. First off, for the `-volname` option, use whatever name you like, it's there for ease of identification, nothing more. The `hostname` should be replaced with the value returned by `hostname -s` from the commands shown above. Likewise, the `MACADDR` must be replaced with the MAC address revealed with the `ifconfig` command shown above. Note that the underscore (_) and the extension (.sparsebundle) are important, so keep this in the disk image name.

A lot of what I've learned about Time Machine came from using [BackMyFruitUp](http://code.google.com/p/backmyfruitup/), created by Jon Stevens. It includes an AppleScript scriptlet that creates the sparse bundle disk image for you, if you like.

Now that the disk image has been created, you can copy it to the volume you created on your storage box. You can use `cp` in Terminal, or simply drag and drop it using Finder.

At this point you can open the Time Machine preferences and choose the volume on the storage server as the backup "disk". At first nothing will happen, so you may want to start a backup immediately. If that fails mysteriously, try unmounting the volume in Finder, then start the backup again. From this point onward, Time Machine will automatically mount the volume whenever it performs a backup, and any time you "enter" Time Machine to browse its contents.
