---
author: nlfiedler
comments: true
date: 2008-12-27 19:49:00+00:00
layout: post
slug: burstsort-for-java
title: Burstsort for Java
wordpress_id: 69
tags:
- java
- sorting
---

Shortly before working at [Quantcast](http://www.quantcast.com/), I became interested in a sorting algorithm that I had not heard of before, called [Burstsort](http://en.wikipedia.org/wiki/Burstsort). I found it while browsing [Wikipedia](http://wikipedia.org/), reading about various methods of sorting. Burstsort, in case you haven't heard of it already, is very fast for large sets of strings, much faster than [quicksort](http://en.wikipedia.org/wiki/Quicksort) and its friends, including multikey quicksort and [radix sort](http://en.wikipedia.org/wiki/Radix_sort). It works by inserting the strings to be sorted into a shallow [trie](http://en.wikipedia.org/wiki/Trie) structure, where buckets are used to store the string references, to reduce memory usage. The buckets are "burst" when they exceed a certain size, and these buckets are sorted using a multikey quicksort. The structure is then traversed in order to retrieve the sorted strings. As a result, Burstsort is cache friendly and thus runs considerably faster than algorithms that are not cache-aware.

Along with the original paper is a C implementation, but as far as I could tell, there was no Java implementation, at least not in open source. So, after reading all of the Burstsort papers several times, I finally started writing a Java implementation of the original algorithm. You can find the project on Google Code, at the [burstsort4j](https://github.com/nlfiedler/burstsort4j) project page. The initial implementation is basically a rewrite of the original C code. After fixing a few bugs that I introduced during the rewrite, it appears to be working well and is indeed much faster than the other algorithms (quicksort and its multikey variant). Of course, I also rewrote those based on their C implementations, so it could be due to mistakes made on my part. Hopefully, since this is all open source now, others can evaluate the code and point out any mistakes I may have made.

In the mean time, I'll be working on the newer algorithms, in particular the CP-burstsort and the "bucket redesign" Burstsort. The goal there is to reduce the memory usage, without trading off substantially from the run time.
