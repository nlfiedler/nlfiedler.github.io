---
author: nlfiedler
comments: true
date: 2009-02-08 22:13:00+00:00
layout: post
slug: building-netatalk-on-opensolaris-200811
title: Building netatalk on OpenSolaris 2009.06
wordpress_id: 70
tags:
- mac
- netatalk
- opensolaris
---

For a few months now, basically since the [Drobo](http://drobo.com/) started supporting third party applications, I have been using my Drobo, via a DroboShare, as a Time Machine backup for my MacBook Pro. I used the [BackMyFruitUp](http://code.google.com/p/backmyfruitup/) toolkit to set up the DroboShare as an [AFP](http://en.wikipedia.org/wiki/Apple_Filing_Protocol) server, so the Mac saw it as an Apple-compatible network file share. One particularly fun step in that process was migrating my existing Time Machine volume over to the Drobo, but that's another story. This story is about how I replaced the Drobo and DroboShare with a server running OpenSolaris.

## Installing OpenSolaris

To start with, I took the old web/file server I had sitting around since last year and installed [OpenSolaris](http://www.opensolaris.com/) 2009.06. Why OpenSolaris you may ask, considering I had been a Linux user for over a decade? Well, I have one word for you: ZFS. If you don't think [ZFS](http://opensolaris.org/os/community/zfs/) is the most impressive file system on the planet, then you haven't watched the three hour [presention](http://blogs.sun.com/storage/entry/video_the_utlimate_zfs_tutorial). But I can't give ZFS all of the credit for prompting the switch to OpenSolaris. There are plenty of very [good reasons](http://www.opensolaris.com/learn/features/) to use OpenSolaris and ZFS is just one of them.

Okay, so once OpenSolaris was on the system disk, what next? Well, you should update the installed packages and reboot into the new boot environment. Next we'll need the C compiler and related packages: pfexec pkg install gcc-dev

The system is now capable of compiling software from source, in particular [netatalk](http://netatalk.sourceforge.net/). Be sure to get the latest stable release (at least 2.0.4) in order to get around weird permissions issues introduced in Leopard. Before installing netatalk we must first install a compatible version of Berkeley DB.

## Installing Berkeley DB

The one prerequisite of netatalk is [Berkeley DB](http://www.oracle.com/technology/products/berkeley-db/). I found that netatalk 2.1.1 would not detect Berkeley DB 5.0 or higher so I had to use the previous stable release, 4.8.30 (on the Berkeley DB web site look for the previous releases link). Compiling it is pretty straightforward. Start by adding /usr/local/lib to the library load path (pfexec crle -u -l /usr/local/lib). Then compile and install Berkeley DB like so (consult their build instructions for details, but it basically goes like this):

```
$ cd build_unix
$ ../dist/configure --prefix=/usr/local
$ make
$ pfexec make install
```

## Installing netatalk

Once Berkeley DB is installed we're ready to build netatalk. I'm skipping the PAM option here because it's more work to set it up and I'm generally looking for the fastest setup time I can find.

```
$ ./configure --without-pam
$ make
$ pfexec make install
```

Now comes the configuration stage. The setup described below suits my own needs, so if you want additional services then check out the netatalk [documentation](http://netatalk.sourceforge.net/2.0/htmldocs/configuration.html) for more information. In general though, you will probably want to make similar changes to the default configuration, so I'll detail what I've done for my environment.

  * Edit /usr/local/etc/netatalk/afpd.conf, adding the following line at the end of the file (this sets up the encrypted password authentication method and tells clients not to save the password, although that seems to be ignored on OS X):
    - -transall -uamlist uams_dhx.so -nosavepassword
  * Edit /usr/local/etc/netatalk/AppleVolumes.default, adding the following (changing the default ~ line as well):
    ~ cnidscheme:dbd options:usedots,invisibledots,upriv perm:0770
    /tank/shared "Shared" allow:@staff cnidscheme:dbd options:usedots,invisibledots,upriv perm:0770
    /tank/nathan_backup "Nathan Backup" allow:nfiedler cnidscheme:dbd options:usedots,invisibledots,upriv perm:0770
    /tank/antonia_backup "Antonia Backup" allow:akwok cnidscheme:dbd options:usedots,invisibledots,upriv perm:0770

Without the `cnidscheme` parameter, warnings will be issued by `afpd`, so just set it to something reasonable (with version 2.1.1 the cdb module could not be found so I used dbd instead). The usedots option tells netatalk to use dots instead of ":2e" for encoding dot files, while invisibledots says to make the dot files invisible by default. Now about the permissions issue alluded to above (see this [discussion](http://groups.google.com/group/linux.debian.bugs.dist/browse_thread/thread/649e575e7f4094ce/7380c402ceca9eaf) for details). With Tiger, newly created files would be writable by others, but in Leopard the permissions are wacky, so the latest netatalk has a work around for that. Add the upriv option and perm:0770 to force the permissions for new files to allow others to read and write to them. After all, this is a shared volume, it's silly if no one else can access the files.

With the configuration complete, you can start the netatalk services. I'm assuming that it's not running already, in which case you can just run this command: pfexec /etc/init.d/netatalk start

## Connecting and Permissions

Now at this point you should be able to connect to the server from your Mac, using the Connect to Server feature in Finder (you can use the Cmd+K shortcut). Type in something like "afp://myserver" in the dialog, replacing myserver with the name of your server, and you will be prompted for a name and password. Use whatever you have for your user accounts on the OpenSolaris server. You could configure netatalk to use PAM, allowing authentication against LDAP or some other service, but for simplicity I just use the system accounts. Once you've authenticated, you will be prompted to select an available shared volume. It doesn't seem to matter which one you pick since the server will be added to the Finder sidebar, and from there you can browse to any of the shared volumes. As for accessing the files on the server, make sure the ownership and permissions are set up such that the user you connect as can read and write to those areas. For instance, the nfiedler user has read/write permission to /tank/nathan_backup, and that same user is a member of the staff group, and the /tank/shared area is owned by the staff group and is group writable.

I can't take the credit for uncovering this information. In fact, this entry is just pulling together the different bits of information into a single, concise set of instructions. The [original blog](http://darkdust.net/writings/opensolaris/compilingnetatalkonopensolaris) that I encountered was written by Marc Haisenko, and for step-by-step instructions on configuring netatalk on Linux, I found the [kremalicious](http://www.kremalicious.com/2008/06/ubuntu-as-mac-file-server-and-time-machine-volume/) blog by Matthias Kretschmann.

[Updated June 16, 2010 with new software versions and instructions.]
