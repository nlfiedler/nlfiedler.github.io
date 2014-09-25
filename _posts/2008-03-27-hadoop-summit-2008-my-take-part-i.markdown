---
author: nlfiedler
comments: true
date: 2008-03-27 15:28:00+00:00
layout: post
slug: hadoop-summit-2008-my-take-part-i
title: 'Hadoop Summit 2008: My Take (Part I)'
wordpress_id: 59
tags:
- conference
- hadoop
- summit
---

On Tuesday I attended the first Hadoop Summit held in the TechMart building in Sunnyvale. It was a terrific experience, much better than I had expected. It doesn't help that my expectations have been tempered by such events as the first NetBeans Day, wherein many of the people that showed up where only there to see James Gosling speak for the first few minutes, after which no more than ~50 people came through (and half of them were Sun employees). It probably didn't help that they were competing with JavaOne as well, which they have corrected since then.

But getting back to the Hadoop Summit. Everything worked very well. Parking was easy, registration was a breeze, and the staff was friendly and accommodating. I asked politely about whether water would be supplied and they quickly chased down the TechMart folks to locate the bottles -- they were already in the auditorium waiting for us. Strike one for dumb attendee. The breakfast spread was a good as any conference I'd been to.

Upon entering the auditorium, it was immediately evident this was an event for computer nerds. There were multiple WiFi routers sitting on a table on one side of the room, and power strips were on the floor beneath every row of chairs. Wow. Too bad the WiFi connections were flaky -- I only got an IP address for only a few minutes, then lost it. Someone came up and asked if the Internet connections were working for me, which gave me the sense I was not the only one suffering from bad connectivity. No matter, I was only going to take notes anyway.

By the time Anjay was ready to open the presentations at 8:55, the room was nearly full. On the wall was a sign stating that the maximum occupancy was 299. I'd say it was a safe bet that about 300 people were crammed into the room, as there were folks standing along the walls.

The first to speak was Doug Cutting, the guy who created Hadoop as a subproject of Lucene. His opening line was "Are you sure you are all in the right place, there's an awful lot of people here." He gave the history of nutch and how Hadoop got started. Getting the history of it all was fascinating, I always like hearing how projects get started. Another fascinating aspect of Hadoop is that it's been barely two years from an almost nothing subproject to a large project with a conference consisting of hundreds of attendees. I guess that means distributed computing is more appealing than Java IDEs. That or Yahoo does a better job organizing these things than Sun. In all fairness, subsequent NetBeans Days have grown exponentially, and are much, much, much better than the first one.

Okay, back to the Hadoop Summit. Next up was Eric14 (some bizarre nickname because his high school friends couldn't pronounce Baldeschwieler), who explained how Hadoop is being used and developed within Yahoo. It's growing very fast and they are struggling to hire people who have any experience with distributed computing, let alone Hadoop in particular. He says they have tens of thousands of nodes (i.e. machines running in clusters in case you are not familiar with this overloaded term) and each machine typically has about 8 cores. Yeah, that's a lot of horse power, but Yahoo must feel they can make good use of it with Hadoop, which supports many MapReduce jobs running in parallel. I believe Google stated they use systems with 4 cores, each with two IDE disk drives.

Eric was curious what the attendees were doing in terms of their Hadoop usage, and asked for an informal poll. There were many people, a little less than half, that had at least 20-node clusters, whereas only a few attendees were running clusters with more than 100 nodes. I was surprised, I didn't expect that many folks to be running such sizable clusters.

There's lots more to say, so breaking this up into several parts makes sense to me. Look for at least three more entries, roughly along the lines of presentations given, with my impression on each of them.
