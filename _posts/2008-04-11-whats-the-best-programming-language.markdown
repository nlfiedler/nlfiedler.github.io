---
author: nlfiedler
comments: true
date: 2008-04-11 14:34:00+00:00
layout: post
slug: whats-the-best-programming-language
title: What's the best programming language?
wordpress_id: 63
categories:
- Computing
tags:
- programming languages
---

The other day I came across an esoteric programming language called [brainfuck](http://en.wikipedia.org/wiki/Brainfuck). It gives new meaning to the term "esoteric". If you can write something practical in that, you're pretty damn hard-core. You also have entirely too much time on your hands. This made me wonder, "What is the worst programming language?", and I began collecting my thoughts based on my personal experience. But rather than focus on the negative, what about turning the question around and asking what is the best programming language? To start with, let me explain my recent experiences with a handful of programming languages.  
  
In the past year at work, I've been involved in a project that involves mostly [Ruby](http://www.ruby-lang.org/) on [Rails](http://www.rubyonrails.org/) development. Coming up to speed with Ruby, after many years of [Java](http://java.sun.com/) programming, was an interesting experience. Ruby is certainly easy to pick up. It's syntax is quite elegant, more expressive than Java, and closures are an appealing alternative to inner classes. However, after reading through [glyphobet's analysis](http://glyphobet.net/blog/essay/228) of Ruby, I've come to appreciate how little I really learned. From that essay, and Gilad Bracha's rant on [monkey patching](http://gbracha.blogspot.com/2008/03/monkey-patching.html), and this very [insightful analysis](http://groups.google.com/group/comp.lang.python/msg/28422d707512283) by Alex Martelli, I'm strongly in favor of using Python over Ruby.  
  
Getting back to my recent experiences, just when I felt comfortable with Ruby, it was time to shift gears and write an Apache module. Given that my C skills are pretty rusty, I opted to use [mod_perl](http://perl.apache.org/) instead, as I had at least used [Perl](http://www.perl.org/) a few times in the last decade. This, as it turned out, was not nearly as easy as I had hoped. It wasn't long before I decided that Perl was a time-wasting abomination, having evolved in a rather poor manner over a period of many years. I'm by no means a language expert, but if I get confused by a language, and waste entirely too much time on the lousy syntax, then there is a problem.  
  
As a reprieve, I had the opportunity to pick up [Python](http://www.python.org/). This came about because we wanted to integrate with a third party tool written in Python. Since Ruby can't call Python code, I initially tried invoking the tool's shell script wrapper, capturing the output and parsing it. Not surprisingly, performance was dreadful, and it was unstable as well. To solve the problem, I broke down and started learning Python, as well as [Django](http://www.djangoproject.com/), in an effort to write a new web service to handle integration with this third party tool. In a matter of weeks, I had a functional web service, complete with unit tests, and its performance was fantastic compared to the Rails service.  
  
In addition to these scripting languages, I had the chance to write some Java code (again, another integration piece). Naturally that went very quickly since I have a decade of basically nothing but Java experience. But it isn't just about experience. The tool support for Java is simply phenomenal -- code completion, error-checking, and flawless refactoring are amazing time savers. A little experience and great tools make Java a very productive language.  
  
So what is the best programming language? To make the selection a little easier, let's consider the popular languages, those in the top 20 on the [TIOBE](http://www.tiobe.com/index.php/content/paperinfo/tpci/) list. Having written code in 11 of them myself, I can honestly say that Java, in my opinion, is the major contender. It's syntax is much easier to learn than most others, it has incredibly good tool support, and the fact that you can run it just about anywhere (enterprise, workstation, mobile phone, sensors, etc) makes it very versatile. Granted, Ruby and Python are fun to learn and have some advantages, they come at a significant cost that makes me appreciate the durability of Java.
