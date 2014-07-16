---
author: nlfiedler
comments: true
date: 2007-09-07 15:08:00+00:00
layout: post
slug: follow-up-to-perl-not-for-dummies
title: Follow-up to Perl not for dummies
wordpress_id: 53
categories:
- Computing
tags:
- dummy
- perl
- ruby
---

So if I were to write that bit of Perl code I posted about 3 days ago in Ruby (using the additional functionality provided by Rails), it would look like this:  


  
    
    <code>hash = Hash.from_xml(res.content)<br></br>service_id = hash['service']['id']<br></br>hash['service']['activities'].each do |item|<br></br> if item['name'] == activity<br></br>   activity_id = item['id']<br></br>   break<br></br> end<br></br>end</code><br></br>

  

I’m fairly certain that code is an accurate translation of the Perl snippet, but without reproducing the same situation, I can’t guarantee it. The point is, it is representative of Ruby code, and its significantly more readable syntax.
