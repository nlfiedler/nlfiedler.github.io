---
author: nlfiedler
comments: true
date: 2010-08-12 14:10:34+00:00
layout: post
slug: finding-unversioned-files-in-perforce
title: Finding unversioned files in Perforce
wordpress_id: 161
tags:
- perforce
- python
---

For many years I've used [CVS](http://www.nongnu.org/cvs), and my experience with [Subversion](http://subversion.apache.org/) started during the pre-alpha days, so I am accustomed to the features they provide. In particular, I make heavy use of the Subversion `status` command to list not only the files I've modified locally, but also the files I've created but not yet added to the list of pending changes. It helps tremendously in remembering to add files to the change list before committing.

Recently I was working for Gracenote, which had been using Perforce for the last 10 years. Finding myself using Perforce again after several years, I searched high and low to find a way to get the Perforce command line client to provide me with the ability to discover unversioned files. Alas, I found none. Being that I know how to write halfway passable Python I wrote a script that does exactly what I want: [p4unknown.py](https://github.com/nlfiedler/devscripts/blob/master/perforce/p4unknown.py)

Admittedly it's not the prettiest name for a script, but it works and I used it for several months while I was at Gracenote. Hopefully you may find it useful as well.

**Update**: Recently the p4 command line has introduced a new subcommand called `reconcile` which makes my script irrelevant. Hurray for progress!
