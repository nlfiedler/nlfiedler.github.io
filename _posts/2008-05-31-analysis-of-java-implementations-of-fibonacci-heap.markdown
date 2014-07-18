---
author: nlfiedler
comments: true
date: 2008-05-31 07:30:00+00:00
layout: post
slug: analysis-of-java-implementations-of-fibonacci-heap
title: Analysis of Java implementations of Fibonacci Heap
wordpress_id: 67
tags:
- fibonacci
- java
---

Some years ago, around 1997 or so, I wrote a Java implementation of the Fibonacci Heap data structure, as described in [Introduction to Algorithms](http://www.introductiontoalgorithms.com/), by Cormen, Leisersen, Rivest, and Stein. A data structure such as the Fibonacci Heap is useful in graphing applications, such as the one I spent some time working on, [GraphMaker](https://github.com/nlfiedler/graphmaker).

Unfortunately, there were mistakes not only in my implementation, but in the pseudo-code published in the book. Due to the fact that my version was one of the first ever written in Java, and it was open source, it eventually spread to other open source software. A few months back, Jason Lenderman and John Sichi brought up an issue in the implementation via an email to me. In particular, John felt that the size of the degree array in the consolidate() method was too large. In fact, I had it set to the size of the heap, which meant the consolidate method had a running time of O(n). Oops, so much for the amortized O(log n) we were hoping for. After spending some time looking at other implementations, and studying the CLRS book, I realized that calculating the degree array size at all was a waste of time (n can never be greater than `Integer.MAX_VALUE`, and log base phi of that is 45). Terrific! The method was much faster now that it had an appropriately sized degree array.

Not being satisfied that everything was working perfectly, I proceeded to write a unit test that would stretch the heap to its limits. Inserting a large numbers of random numbers, and then extracting the minimum value would cause a massive heap consolidation. This yielded a problem, and it was bewildering. I couldn't for the life of me see what was going wrong. Then my wife, Antonia, came to the rescue. At the time she was between jobs and had some time on her hands, so she took a look at it and found that the original pseudo-code in CLRS was missing two important steps. Elated, I submitted a bug report to Dr. Cormen and subsequently the fix has made its way into the example Java source code in an upcoming edition of the book. However, Antonia was not the first to realize there was a problem in the pseudo-code. It seems that Doug Cutting wrote a version of Fibonacci Heap in Java for the [Apache Nutch](http://nutch.apache.org/) project, and it didn't have the problems that my wife had uncovered.

Curious what other Java implementations looked like, I found several and have collected some notes on their implementations. In particular, I was looking at the consolidate operation, which is the only complex bit of code in a Fibonacci Heap, as everything else is fairly trivial. The "array" referred to below is the degree array used to keep track of the root nodes by their degree.

  * [Scott Cotton](http://www-verimag.imag.fr/%7Ecotton/)
    * Calculates array size using binary search of lookup table
    * Pre-fills array with nulls
    * Allocates an additional buffer for iterating root list -- can be very expensive
    * Rebuilds root list
  * [Ashish Gupta](http://www.cs.northwestern.edu/%7Eagupta/)
    * Calculates array size
    * Pre-fills array with nulls
    * Breaks when "w" or "nextW" is made a child
    * Rebuilds root list
  * [Apache Nutch](http://lucene.apache.org/nutch/) (removed in Nov 2007)
    * Calculates array size
    * Involves a hash table which kills performance
  * [CLRS](http://www.introductiontoalgorithms.com/)
    * Calculates array size
    * Pre-fills array with nulls
    * Breaks when "nextW" is made a child
    * Rebuilds root list
  * [John Sichi](http://www.jgrapht.org/)
    * Degree array is of size N
    * Pre-fills array with nulls
    * Counts number of root list elements (R)
    * Iterates over root list R times, possibly wasting additional time
    * Does not handle the "w" and "nextW" issue
    * Rebuilds root list

It should be noted that all of the problems in Sichi's implementation are entirely my fault, as his code is a fork of my original implementation. Since our email discussion, Jason and John are aware of all of the bugs and their appropriate fixes.

In the process of analyzing the various implementations, I learned a few things.

  * Allocate a fixed-size array for the degrees of the root nodes. At most it will be 45 entries to hold log base phi of `Integer.MAX_VALUE` elements, and it's cheaper to create that than to perform a series of floating point operations to arrive at a number that is slightly smaller than 45 (e.g. it's 23 to hold 50,000 elements).
  * Do not fill the degrees array with nulls -- all arrays in Java are automatically initialized to zero/false/null.
  * Do not waste time rebuilding the root list at the end of consolidate; the order of the root elements is of no consequence to the algorithm.
  * Use the Cormen "splice in" technique in removeMin() to save significant time (see the Java implementation in the recent editions of the book, or the version in GraphMaker).

My new implementation, as found in [GraphMaker](https://github.com/nlfiedler/graphmaker), has all of these improvements, so take a look if you're at all interested. In all, this was a fascinating exercise for me. I hope you had a chance to learn something, too.
