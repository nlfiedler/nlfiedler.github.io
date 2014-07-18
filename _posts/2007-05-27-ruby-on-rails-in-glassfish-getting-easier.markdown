---
author: nlfiedler
comments: true
date: 2007-05-27 05:50:00+00:00
layout: post
slug: ruby-on-rails-in-glassfish-getting-easier
title: Ruby on Rails in Glassfish, getting easier
wordpress_id: 39
tags:
- glassfish
- rails
- ruby
---

I recently read Arun’s [blog entry](http://blogs.sun.com/arungupta/entry/mephisto_on_glassfish_v3) on getting the [Mephisto](http://www.mephistoblog.org/) blog engine running inside [Glassfish](http://glassfish.dev.java.net/). It has gotten easier since about a month ago, when it was necessary to use GoldSpike to package the Rails app into a .war file before deploying it to Glassfish. The reason I bring this up is that during the question and answer portion of the [Ruby on Rails: To Scale or Not To Scale](http://www.geeksessions.com/2007/04/27/ruby-on-rails-scalability/) talk, Glassfish came up. Jason Hoffman, CTO of Joyment, said that running a (test?) Rails app in Glassfish showed that it could handle five times the number of requests per second as [Mongrel](http://mongrel.rubyforge.org/). One of the thought provoking comments that Jason made was, what if Glassfish became the defacto standard for running Rails apps, and subsequently [JRuby](http://jruby.codehaus.org/) became the preferred Ruby implementation? Continuing that thought, he asked, who then drives the definition of the language? Likewise, many newcomers to Ruby are learning it because of Rails. What if Rails drives the direction of Ruby, as well.

   

Admittedly, I had not considered such questions. I’ve been too excited about learning Ruby and Rails to stop and think about its future. It looks to me like it’s in good hands, so I’m not the least bit worried. I’ll just focus on writing apps, until the next language and framework come along.
