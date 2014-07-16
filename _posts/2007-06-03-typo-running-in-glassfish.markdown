---
author: nlfiedler
comments: true
date: 2007-06-03 14:43:00+00:00
layout: post
slug: typo-running-in-glassfish
title: Typo running in Glassfish
wordpress_id: 43
categories:
- Computing
tags:
- glassfish
- ruby
---

Using the instructions provided by Arun in his [blog entry](http://blogs.sun.com/arungupta/entry/mephisto_on_glassfish_v3) I was able to get the [Typo](http://typosphere.org/) blogging engine running in Glassfish v3 Preview. Initially I had tried to use [JRuby](http://jruby.codehaus.org/) 1.0.0RC2, but every time I tried to install the Rails gems, it would run out of memory. I reported the issue and Charles suggested trying the just-released RC3, and if that didn’t work, add `-Xmx512m` when installing the gems. The good news is that just upgrading to RC3 fixed the problem and I was able to install all of the Rails gems, plus the `activerecord-jdbc` gem.

   

Once that issue was out of the way, starting up Glassfish and deploying an instance of Typo was no problem. Granted, it took around two minutes to deploy and the JVM process ate up about 225MB of memory. Normally this isn’t a problem, but with just 1GB of RAM and nine Mongrel instances running at the same time, it forced the kernel to swap, which slowed things down a bit. It could be that JRuby and Glassfish are not yet optimized for hosting Rails applications, which is probably a safe bet considering they are both pre-release versions.

   

While I won’t be switching from [Mongrel](http://mongrel.rubyforge.org/) any time soon, I will definitely be keeping my eye on JRuby and Glassfish, and trying them out from time to time. I’m very much looking forward to simplifying my deployment and monitoring setup by using Glassfish, as setting up and monitoring several mongrel clusters is tedious. Glad to see the direction that Glassfish is going with version 3; not only does it run Rails applications, but it supports PHP as well. I haven’t tried it, but I will when v3 gets a little closer to beta.

   

Thanks Arun and Charles, and of course, the Glassfish and JRuby teams. Can’t wait to see what happens when GF and JRuby get the attention of the Rails community. It’s going to be exciting.
