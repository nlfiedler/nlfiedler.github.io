---
author: nlfiedler
comments: true
date: 2009-07-19 14:02:38+00:00
layout: post
slug: improvements-to-burstsort-for-java
title: Improvements to Burstsort for Java
wordpress_id: 119
categories:
- Algorithms
- Computing
tags:
- burstsort
- java
- parallel
- sorting
---

Recently I had been side tracked by the need to do something about the network attached storage situation at home, so I had taken a break from my software projects. But a gentleman by the name of [Kimo Crossman](http://www.linkedin.com/pub/kimo-crossman/0/196/a32) wouldn't let me forget about [burstsort](http://en.wikipedia.org/wiki/Burstsort). He has been sending me links to research papers on various parallel algorithms and other cache-related subjects, and making suggestions for how to improve my open source Java [implementation](http://code.google.com/p/burstsort4j/) of the original burstsort. In particular, Kimo felt that applying principles from parallel algorithms would be the most important means for improving the performance of burstsort. I have very much appreciated his help and I thoroughly enjoy reading academic papers. In return, I've made an effort recently to work on a few improvements for burstsort4j.


## Parallelized


The first major change was to introduce a parallelized version of burstsort. In this initial attempt, it only parallelizes the bucket sorting. The building of the trie and buckets still happens in a single thread. However, once the structure is built, the multi-threaded version creates a thread pool of size equal to the number of available processors, and creates sort jobs that are then run in parallel. These jobs run independently of one another, copying the sorted output to the original array without any unnecessary synchronization. As a result, the overall runtime is shortened considerably on a dual-core CPU, which in my case is the Intel Core 2 Duo in my MacBook Pro.


## Engineered Burstsort


With the publication of the WEA 2008 [paper](http://www.springerlink.com/content/35022477853m05v7/) by Ranjan Sinha and Anthony Wirth came a newly engineered variation of the original burstsort. In particular, this algorithm made much better use of memory, while still being nearly as fast as the original algorithm. It primarily makes a change in the structure of the buckets, where instead of a single dynamic array they now have an array that points to other arrays of pointers to strings. These sub-buckets, as they are called, are grown at a slower pace and stop growing at a much lower threshold than the original algorithm. Once a sub-bucket is filled, a new sub-bucket is created, and so on until the overall bucket size reaches a threshold equal to that of the original algorithm. As a result, the memory usage is dramatically improved.

Needless to say, my excitement was very high at this point. I desperately wanted to implement this redesigned burstsort in Java as soon as I could. But, certain other [obligations](http://code.google.com/p/jswat/) got in the way for a time, and after a few months I finally wrote the Java version of the engineered burstsort. After fixing one small mistake it was working and it was better than I could have imagined. Not only did the memory efficiency go from about 25 percent to 95 percent, it was often a little bit faster than my original implementation.


## What's Next


There are yet more improvements to make. First of all, the WEA 2008 paper offers a second improvement, which is to copy the string tails from a bucket to a string buffer and sort them there. That is, the string buffer would only be used during the bucket sorting phase and would be re-used after each bucket is sorted. I have an idea to use a large character array and an implementation of CharSequence to create lightweight strings.

Secondly, I want to experiment with the parallel version of burstsort. In particular, try out the suggestions made by Kimo to parallelize the building of the trie/bucket structure. I think it can definitely be done, the only question will be how much contention there will be on the trie nodes. As a means to test these parallel algorithms I've bought a quad-core AMD Phenom CPU and mainboard to use as my development machine. I'm really looking forward to seeing the results.
