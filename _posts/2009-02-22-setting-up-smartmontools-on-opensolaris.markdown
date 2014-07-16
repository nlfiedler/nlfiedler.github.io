---
author: nlfiedler
comments: true
date: 2009-02-22 08:13:45+00:00
layout: post
slug: setting-up-smartmontools-on-opensolaris
title: Setting up smartmontools on OpenSolaris
wordpress_id: 77
categories:
- Computing
- HowTo
- Storage
tags:
- opensolaris
- smartmontools
- zfs
---

As a follow-up to the previous entries concerning my new storage server, I thought I'd talk about installing and configuring the [smartmontools](http://smartmontools.sourceforge.net/) monitoring software in OpenSolaris. Like most open source software, it's fairly easy to compile and install on OpenSolaris, it's the automating part that's a little different from Linux, for which smartmontools was developed.

To get started, download the latest release of the smartmontools source and extract it to a temporary directory. Next, make sure you have the `gcc-dev` packages installed, otherwise compiling the source is going to be a challenge (if `which gcc` returns nothing, run `pfexec pkg install gcc-dev`). Now you can build and install the tools quite easily with the following commands.



	
  1. `./configure`

	
  2. `make`

	
  3. `pfexec make install`


At this point the `smartd` and `smartctl` binaries are installed under `/usr/local`, along with the manual pages and a sample configuration file, `/usr/local/etc/smartd.conf`. There are just a couple of changes to be made in the configuration file, and a few notes before proceeding. First off, as of today, ATA disk support in smartmontools on Solaris is not there, so SCSI emulation is used instead. While this gives us basic health status, it seems to prevent any detailed SMART data from being collected. It may also be the reason why I can't run self-tests on my disks. This all worked in Linux with these same disks, so I'm guessing it's due to the lack of ATA support in Solaris. Secondly, before you can monitor your disks, you'll need to know the labels for those disks. I found `zpool status` worked quite well.

    
    $ zpool status
      pool: rpool
     state: ONLINE
     scrub: scrub completed after 0h6m with 0 errors on Sun Feb 15 02:21:37 2009
    config:
    
            NAME        STATE     READ WRITE CKSUM
            rpool       ONLINE       0     0     0
              c0d0s0    ONLINE       0     0     0
    
    errors: No known data errors
    
      pool: yubaba
     state: ONLINE
     scrub: scrub completed after 0h59m with 0 errors on Sun Feb 15 03:15:02 2009
    config:
    
            NAME        STATE     READ WRITE CKSUM
            yubaba      ONLINE       0     0     0
              raidz1    ONLINE       0     0     0
                c4t0d0  ONLINE       0     0     0
                c4t1d0  ONLINE       0     0     0
                c5t0d0  ONLINE       0     0     0
                c5t1d0  ONLINE       0     0     0
    
    errors: No known data errors


Not only does it show which disks are in which pools, but it gives you the names of the disks that smartmontools expects, namely `c4t0d0` and so on. Now we are ready to make changes to the `smartd.conf` file.

The first change to make in `smartd.conf` is to comment out the `DEVICESCAN` line, which is fine if you want to scan all disks in your system, but I found that smartmontools didn't like my rpool disk, and it wanted me to declare the disk types as "scsi" for it to do anything at all. Next we have to tell `smartd` which disks to monitor, so I added the following lines to the end of the `smartd.conf` file:

    
    /dev/rdsk/c4t0d0 -d scsi -H -m root
    /dev/rdsk/c4t1d0 -d scsi -H -m root
    /dev/rdsk/c5t0d0 -d scsi -H -m root
    /dev/rdsk/c5t1d0 -d scsi -H -m root


This seems to work, as invoking `pfexec smartd -q onecheck` resulted in output like this:

    
    $ pfexec smartd -q onecheck
    smartd version 5.38 [i386-pc-solaris2.11] Copyright (C) 2002-8 Bruce Allen
    Home page is http://smartmontools.sourceforge.net/
    
    Opened configuration file /usr/local/etc/smartd.conf
    Configuration file /usr/local/etc/smartd.conf parsed.
    Device: /dev/rdsk/c4t0d0, opened
    Device: /dev/rdsk/c4t0d0, is SMART capable. Adding to "monitor" list.
    Device: /dev/rdsk/c4t1d0, opened
    Device: /dev/rdsk/c4t1d0, is SMART capable. Adding to "monitor" list.
    Device: /dev/rdsk/c5t0d0, opened
    Device: /dev/rdsk/c5t0d0, is SMART capable. Adding to "monitor" list.
    Device: /dev/rdsk/c5t1d0, opened
    Device: /dev/rdsk/c5t1d0, is SMART capable. Adding to "monitor" list.
    Monitoring 0 ATA and 4 SCSI devices
    Device: /dev/rdsk/c4t0d0, opened SCSI device
    Device: /dev/rdsk/c4t0d0, SMART health: passed
    Device: /dev/rdsk/c4t1d0, opened SCSI device
    Device: /dev/rdsk/c4t1d0, SMART health: passed
    Device: /dev/rdsk/c5t0d0, opened SCSI device
    Device: /dev/rdsk/c5t0d0, SMART health: passed
    Device: /dev/rdsk/c5t1d0, opened SCSI device
    Device: /dev/rdsk/c5t1d0, SMART health: passed
    Started with '-q onecheck' option. All devices sucessfully checked once.
    smartd is exiting (exit status 0)


So far so good, but what about having smartd run at bootup, and continuously monitoring the disk status? In Linux, you'd use `initd`, but since this is OpenSolaris, we'll use the Service Management Framework (SMF) instead. To do that, paste the following text into `/var/svc/manifest/site/smartd.xml`, change the file ownership to `root:sys`, and invoke `pfexec svccfg -v import /var/svc/manifest/site/smartd.xml`. Then check that the service is running (`svcs smartd`), and if not, enable it using `pfexec svcadm enable smartd`.

    
    <?xml version="1.0"?>
    <!DOCTYPE service_bundle SYSTEM "/usr/share/lib/xml/dtd/service_bundle.dtd.1">
    <service_bundle type="manifest" name="smartd">
      <service
         name="site/smartd"
         type="service"
         version="1">
        <single_instance/>
        <dependency
           name="filesystem-local"
           grouping="require_all"
           restart_on="none"
           type="service">
          <service_fmri value="svc:/system/filesystem/local:default"/>
        </dependency>
        <exec_method
           type="method"
           name="start"
           exec="/usr/local/etc/rc.d/init.d/smartd start"
           timeout_seconds="60">
          <method_context>
            <method_credential user="root" group="root"/>
          </method_context>
        </exec_method>
        <exec_method
           type="method"
           name="stop"
           exec="/usr/local/etc/rc.d/init.d/smartd stop"
           timeout_seconds="60">
        </exec_method>
        <instance name="default" enabled="true"/>
        <stability value="Unstable"/>
        <template>
          <common_name>
            <loctext xml:lang="C">
              SMART monitoring service (smartd)
            </loctext>
          </common_name>
          <documentation>
            <manpage title="smartd" section="1M" manpath="/usr/local/share/man"/>
          </documentation>
        </template>
      </service>
    </service_bundle>


At this point we have a managed service that is checking the health of our disks, and if anything comes up, it will send an email to the root user. While I would have liked to also set up short and long self-tests, I can live without it for now. In the mean time, I've got a weekly cron job that scrubs the data on the disks using `zpool scrub`, which will identify any data read errors on the disks and attempt to correct them automatically.
