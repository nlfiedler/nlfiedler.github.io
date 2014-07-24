---
author: nlfiedler
comments: true
date: 2009-03-01 15:21:46+00:00
layout: post
slug: installing-logwatch-on-opensolaris
title: Installing Logwatch on OpenSolaris
wordpress_id: 90
tags:
- logwatch
- opensolaris
---

Typically, installing [Logwatch](http://www.logwatch.org/) is fairly trivial. On Linux, you'd just use the package installer command and you're done. On OpenSolaris, there doesn't seem to be a packaged version of Logwatch (yet), so installing from the source tarball is necessary. Fortunately, there's a shell script that performs the installation. The bad news is this script finds `/usr/sbin/install` which is the Solaris version of `install`. This version behaves very differently from those found in other Unix variants. The Logwatch installer is expecting the behavior of the `install` script found on Linux, so it fails miserably on OpenSolaris.

The good news is, there's a simple solution. Just install the `compatibility/ucb` package. This is the "source compatibility package", which installs numerous commands that help OpenSolaris behave more like other Unix systems. The Logwatch installer script prepends the `/usr/ucb` directory to the `PATH` when it runs, so it finds the `install` script that it is expecting, and thus it installs Logwatch perfectly. The only thing left is to add the `crontab` entry, as shown at the end of the install output.

One last note about Logwatch, and it concerns that crontab entry. It seems that the default configuration for Logwatch is to print the report rather than sending an email to the default recipient, root. However, the example crontab entry is redirecting all output to `/dev/null`. So how exactly is one supposed to get a daily report? The answer is to edit the `/etc/logwatch/conf/logwatch.conf` file, adding `Print = no` at the end of the file. That tells Logwatch to email the report rather than printing. It's a mystery to me why that's the default given the example crontab entry they display during the installation process. But at least it's easy to fix, and nicely demonstrates how easy it is to customize Logwatch without touching the default configuration files.
