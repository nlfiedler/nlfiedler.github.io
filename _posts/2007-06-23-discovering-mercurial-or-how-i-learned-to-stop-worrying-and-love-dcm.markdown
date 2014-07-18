---
author: nlfiedler
comments: true
date: 2007-06-23 11:12:00+00:00
layout: post
slug: discovering-mercurial-or-how-i-learned-to-stop-worrying-and-love-dcm
title: Discovering Mercurial, or how I learned to stop worrying and love DCM
wordpress_id: 44
tags:
- dvcs
- mercurial
---

Because of a project at work, I’m having to come up to speed quickly with [Mercurial](http://selenic.com/mercurial), a distributed source code management system. Much like learning Ruby on Rails, I had some apprehension toward learning DCM. My reservations centered around fear of the unknown and an assumption that having the full repository on your development system would mean using a lot of disk space. Well, that turns out to not be so true. I had read a blog entry about a migration from Subversion to Mercurial (or was it from CVS), in which the author said that his Mercurial repository/working directory was smaller than the Subversion working directory. This seemed absurd… how could that be so? To prove myself right, I used [Tailor](http://progetti.arstecnica.it/tailor) to migrate a few of my own Subversion repositories to Mercurial. In the end I proved myself wrong.

   

### Very small repository

   

  * 22 revisions
  * 2.0 MB svn repository
  * 4.4 MB svn working copy
  * 3.7 MB Hg repository
   

Yikes! That’s a lot of overhead for a svn working copy for a (very) small repository. This might be interesting, but most projects will grow to considerable size, so one would assume the overhead is less significant in those cases. However, it is already the case that the Mercurial disk usage is considerably less than that of Subversion.

   

### Small repository

   

  * 214 revisions
  * 3.1 MB svn repository
  * 3.5 MB svn working copy
  * 2.9 MB Hg repository
   

All of these measurements are being made on a Linux system using the `ext3` file system, and the numbers were generated using `du -sh`. With the `--apparent-size` option, the size of both the Svn working copy and the Hg repository turned out to be 2.4 MB. But, apparent size is a useless metric since what really matters is the actual disk usage.

   

### Not so small repository

   

  * 2967 reivisions
  * 64 MB svn repository
  * 109 MB svn working copy
  * 83 MB Hg repository
   

So, as expected, the overhead for the Subversion working copy has dropped off a bit, from 220% to 170%. As before, the actual file sizes are much less, amounting to 47 MB for Mercurial and 73 MB for the Subversion working copy. As with the smaller data sets, the size of the Mercurial repository is half the size of the combined Subversion usage.

   

In case you were wondering, the Subversion repositories are all in the “fuzz-fuzz” format (FSFS), rather than the old Berkeley DB format. The BDB format was considerably worse, with the 2967 revisions occupying 90 MB of disk space. Yes, I cleared the logs, daily, so that was not the issue.

   

### In conclusion…

   

Okay, I realize I’m talking about fairly small repositories here. Unfortunately I don’t have a really large code base to play with. That’s why I spent half an hour trying to find that blog entry I had seen earlier. I remember it very clearly because it seemed impossible to me that Hg could have a repository smaller than an svn working copy. As I’ve learned from reading the [Mercurial book](http://hgbook.red-bean.com/), Hg is very clever about compressing the files stored in the repository.

   

With my newfound appreciation for Mercurial, I’ve asked our project team at work what they think about using Hg instead of SVN. There are a few other significant advantages, of course. I’m not thinking solely of disk space, since disk space is cheap these days. Consider, for instance, that a DCM like Hg makes collaborating with others significantly easier than with a centralized SCM. Chapter 1 of the hgbook gives a few examples, the most important of which I feel is the intelligent merge support that Hg employs. That, and the clever compression algorithms are described in detail in chapter 4, which goes behind the scenes on how Hg works. It’s fascinating, and a good marketing tool at the same time. In fact, Bryan’s book is largely responsible for swaying my opinion on Mercurial.

   

One thing is for sure, I’ll be using Mercurial for my personal projects. Even though they are small, the advantages are too significant to ignore.
