---
author: nlfiedler
comments: true
date: 2011-12-10 17:48:35+00:00
layout: post
slug: running-zfs-filesystem-backup
title: Running ZFS filesystem backup
wordpress_id: 168
tags:
- backup
- openindiana
- opensolaris
- python
- zfs
---

If you've read some of my earlier posts regarding ZFS, you'll likely know that it is the heart of my home file server. In some of my other posts, you'll notice that I take backups pretty seriously. So combining both of those interests I developed a small Python script that replicates a ZFS filesystem. It works both for a one-time replication as well as ongoing incremental replications. All that's needed is to set up the cron job to run the script. Here is an example entry from the root user's crontab:

```
10 1 * * 0 /usr/local/bin/replica.py yubaba/shared safe/shared
```

The `yubaba` pool is my main RAID-Z file system and the `safe` pool is on a USB hard drive located in a fire/water-proof safe with a USB pass-through. You can use any two filesystems that you like, as long as there is sufficient space on the destination.

The script will maintain at most two snapshots on both the source and destination filesystems in order to support sending incremental snapshots. It is a zero-maintenance script, there is nothing that you need to do beyond the one-time setup of the cron job. Be sure not to make any changes in the destination filesystem, as they will be automatically rolled back during the next incremental replication.

The script is located on GitHub: [replica.py](https://github.com/nlfiedler/devscripts/blob/master/zfs/replica.py) -- while I am running OpenIndiana build 151, I specifically install Python 3.x, so the script requires that version as well. With the [SFE](http://wiki.openindiana.org/oi/Spec+Files+Extra+Repository) repository added, you can install Python 3.2 easily:

```
$ pfexec pkg set-publisher -p http://pkg.openindiana.org/sfe
$ pfexec pkg install runtime/python-32
```

If you find this useful, feel free to file any feature requests you might have on the GitHub project page.
