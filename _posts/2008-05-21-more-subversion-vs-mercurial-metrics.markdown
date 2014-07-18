---
author: nlfiedler
comments: true
date: 2008-05-21 07:06:00+00:00
layout: post
slug: more-subversion-vs-mercurial-metrics
title: More Subversion vs. Mercurial metrics
wordpress_id: 66
tags:
- comparison
- mercurial
- subversion
---

I wrote a while back about the disk usage of Subversion versus Mercurial, but all of my examples were rather small. Today I managed to create a migration of a Subversion repository, as complete and intact as possible (with trunk, tags, and branches directories just like in Subversion). The repository contains 3526 revisions and 28977 files, stored in Subversion 1.4.6 and migrated to Mercurial 1.0, using a hacked hg convert to keep the TTB structure intact.

Keeping the entire revision history, the Subversion repository used 238 MB of disk, while the Mercurial repository (sans working copy) occupied 403 MB. Compare that with a "snapshot" repository (i.e. a repository with one revision) that consists of the latest version of all the files: Subversion 160 MB, Mercurial 229 MB. You can probably guess from these numbers that our repository has a lot of third-party code that we do not change frequently. Interestingly, in both cases, Subversion comes out ahead in terms of disk usage, by a significant margin.
