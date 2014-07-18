---
author: nlfiedler
comments: true
date: 2008-04-11 13:21:00+00:00
layout: post
slug: hadoop-summit-2008-my-take-part-iv
title: 'Hadoop Summit 2008: My Take (Part IV)'
wordpress_id: 62
tags:
- conference
- hadoop
- summit
---

Continuing on with the user testimonials, Steve Schlosser (of Intel?) spoke on using Hadoop to create graphs of ground motion models, using interpolated data generated via two different systems. A technique he had learned was to massage the key values returned from the Mapper to trick Hadoop into naturally gathering tuples for the reduce stage. He reminded everyone that while MapReduce sounds simple, it's actually a number of stages: InputFormat, Map, ManipulateKey, Bucket, Combine, Shuffle, Sort, Reduce, and finally OutputFormat. Their cluster, by the way, consisted of 50 8-core blades with 8GB memory and 300GB disk. That's a pretty hefty set of machines, but I think they had a lot of calculations to run through to create their pretty pictures.  
  
Mike Haley from Autodesk got a turn to tell how he uses Hadoop in an effort to simplify the cataloging and searching of building materials. At least I think that's what it was about. He spent a lot of time talking about building materials and just a couple of minutes on Hadoop, so I kinda missed the point of it. Still, it was interesting; it's not often you get a chance to take a peek into a completely different industry.  
  
To provide a perspective on Yahoo's use of Hadoop, Christian Kunz spoke on the WebMap change-over. He apparently was not slated to speak for this presentation, as the [schedule](http://developer.yahoo.com/hadoop/summit/) shows Arnab Bhattacharjee as the speaker. Subsequently, the talk was brief and it was obvious Christian was not at all comfortable in front of a large audience. In short, Yahoo went from a set of in-house scripts and programs, developed over a number of years, to Hadoop and saw a lot of advantages and improvements. The talk was literally 10 minutes, so there wasn't much else to it.  
  
Next up was a guy from Google, who spoke briefly on the problem of hiring people who know anything about parallel computing, much less MapReduce. Well, yeah, duh. Most colleges aren't teaching distributed/parallel computing techniques, and except for Yahoo and Google, who uses MapReduce? He then introduced Jimmy Lin from the University of Maryland, who spoke on a new course being offered at the university in which students use Hadoop to solve interesting problems. At this point in the day, I was a bit tired and wasn't really getting much from these last presentations. Interesting, yes, but not remarkable in my opinion. But I liked the irony that Google has a hard time finding people who are ready to dive into M-R, and their solution is to use their competitors open-source clone of their very own infrastructure. Come to think of it, the students aren't just learning M-R, they are learning Hadoop, so it's just as likely they would get snatched up by Yahoo! upon graduation.  
  
To wrap up the exciting day, a panel of folks from Yahoo, Powerset, and Mahout spoke on the future direction of Hadoop. For instance, the core developers want to eventually have Hadoop auto-configure itself, but still have options for power-users to tweak the settings (my thought was of the Java VM, which has dozens of settings for fine tuning its behavior, but out of the box, it automatically adjusts itself to suit the environment). Another wish was to support Kerberos for protecting the HDFS data; right now anyone can read anything in the system, which is not good for privacy and isolation. Another comment was that building the community is a challenge. Hopefully as committers become more senior, contributors can be mentored into committers and help foster new involvement from others.  
  
Well, that was it, the whole Hadoop Summit in a nutshell. There was a happy hour but I had to get home to my family, so I gave it a miss. For additional reading material, check out these: [Hadoop Summit - Best in Show](http://blog.blist.com/index.php/2008/03/26/hadoop-summit-best-in-show/), [At the Hadoop Summit](http://www.csdhead.cs.cmu.edu/blog/2008/03/26/at-the-hadoop-summit/), [Visited Hadoop Conference](http://mikaelronstrom.blogspot.com/2008/03/visited-hadoop-conference.html), [Hadoop Summit Notes](http://parand.com/say/index.php/2008/03/25/hadoop-summit-notes/).
