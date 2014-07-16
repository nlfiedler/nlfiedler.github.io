---
author: nlfiedler
comments: true
date: 2008-03-13 16:11:00+00:00
layout: post
slug: converting-subversion-to-mercurial-update
title: Converting Subversion to Mercurial update
wordpress_id: 57
categories:
- Computing
tags:
- mercurial
- SCM
- subversion
---

Our development team at work is moving from a single Subversion repository to multiple Mercurial repositories. Being the SCM "expert" in the group, I was nominated to carry out the migration. My first instinct was to use what I know, which up until now had been [hgsvn](http://cheeseshop.python.org/pypi/hgsvn), which works fairly well, but lacked a few key features we needed. Fortunately, our team lead, [Nick Sieger](http://blog.nicksieger.com/), pointed out the [hg convert](http://www.selenic.com/mercurial/wiki/index.cgi/ConvertExtension) extension, which appeared to be perfect.  
  
Armed with this enticing new option, which didn't exist the last time I had done a Subversion to Mercurial migration, I set about updating my Ultra 20 to the latest Solaris Express DE (build 79b of OpenSolaris). I was pleasantly surprised by the much-improved installer, and the fact that all of the tools and languages our team uses are pre-installed, and most are the latest versions. But I digress...  
  
To perform the migration, I had to implement a couple of work-arounds for bugs in that build of OpenSolaris. First was the fact that the Subversion language bindings are not in the default load path, and secondly, the neon library isn't [loaded properly](http://bugs.opensolaris.org/bugdatabase/view_bug.do?bug_id=6612347) for Python. I ended up with a command like this, to convert a single application within our source repository:  


<blockquote>LD_LIBRARY_PATH=/lib:/usr/lib:/usr/apache2/2.2/lib LD_PRELOAD=libneon.so hg convert  -A  ~/author_map.txt --filemap scm_map.txt file:///export/home/nfiedler/svn_mirror/trunk/scm  
</blockquote>

With that, I was able to rename authors to match what we're using in our new infrastructure, and rename some of the poorly named directories in our source tree. The only missing piece is the svn:externals we were using to manage shared dependencies, but we solved that by using symbolic links. By the way, we won't shed a tear for losing the svn:externals feature, the most poorly implemented feature in all of Subversion.  
  
The best part of the migration process is that it's iterative, meaning I can run it again and again as each of the developers is ready to check in their pending work and switch over to Mercurial. I made the switch first to test the waters, and it's working very well. So far as I can tell, Mercurial is a lot faster than Subversion ever was for us, even when we were using an internal server. Granted, we were always using http over Apache, so perhaps using the svn protocol would have helped. We'll be implementing that in our system soon enough, but that's for another post.
