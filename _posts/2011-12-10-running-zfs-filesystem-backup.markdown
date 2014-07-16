---
author: nlfiedler
comments: true
date: 2011-12-10 17:48:35+00:00
layout: post
slug: running-zfs-filesystem-backup
title: Running ZFS filesystem backup
wordpress_id: 168
categories:
- Computing
- Storage
tags:
- backup
- openindiana
- opensolaris
- python
- zfs
---

If you've read some of my earlier posts regarding ZFS, you'll likely know that it is at the heart of my home file server. In some of my other posts, you'll notice that I take backups pretty seriously. So combining both of those interests I developed a small Python script that replicates a ZFS filesystem. It works both for a one-time replication as well as ongoing incremental replications. All that's needed is to set up the cron job to run the script. Here is an example entry from the root user's crontab:

    10 1 * * 0 /usr/local/bin/replica.py yubaba/shared safe/shared

The `yubaba` pool is my main RAID-Z file system and the `safe` pool is on a USB hard drive located in a fire/water-proof safe with a USB pass-through. You can use any two filesystems that you like, as long as there is sufficient space on the destination.

The script will maintain at most two snapshots on both the source and destination filesystems in order to support sending incremental snapshots. It is a zero-maintenance script, there is nothing that you need to do beyond the one-time setup of the cron job. Be sure not to make any changes in the destination filesystem, as they will be automatically rolled back during the next incremental replication.

The script is located on Google Code: [replica.py](https://github.com/nlfiedler/devscripts/blob/master/zfs/replica.py) -- I'm running OpenIndiana build 148 and the version of Python there 2.6, so the script most likely requires that version.
