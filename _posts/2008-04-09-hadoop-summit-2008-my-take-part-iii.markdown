---
author: nlfiedler
comments: true
date: 2008-04-09 15:39:00+00:00
layout: post
slug: hadoop-summit-2008-my-take-part-iii
title: 'Hadoop Summit 2008: My Take (Part III)'
wordpress_id: 61
categories:
- Computing
tags:
- conference
- hadoop
- summit
---

After an exciting morning, we all had the chance to partake in a free lunch. I had the (mis)fortune of sitting next to a gentleman from CMU whose job was to travel the world and meet researchers to learn the latest in cool inventions. Probably there was more to it than that, but that was the part I understood. He and two other gentlemen my age that he had met earlier in the day pontificated on the future directions of technology and its effects on our lives, while I struggled to keep up and not look dumb. It's hard being a heads-down sort of techie and finding yourself thrust into a conversation with people whose job is to think big.  
  
After that minor ordeal, the technical presentations started up again with Michael Stack explaining the virtues of [HBase](http://hadoop.apache.org/hbase/). One of his early remarks was that HBase doesn't have any of that sissy RDBMS stuff. Well that's good, we wouldn't want to rile the DB folks, who already have a hard time [grasping the obvious](http://www.databasecolumn.com/2008/01/mapreduce-a-major-step-back.html) (to be fair, they have a follow-up article that is at least a little bit better than their original post). One interesting revelation was that HBase ran significantly slower than the numbers published in the BigTable paper, as little as 20%. Speculation that HDFS was to blame was made. That Hadoop is still a little immature is not a surprise, and clearly performance is not yet a major concern.  
  
Next up was Bryan Duxbury of Rapleaf, who spoke of their use of HBase and Hadoop. Bryan is the one responsible for the Ruby support in HBase, by the way. He primarily discussed the performance of HBase. They are using a 64 node cluster, with a total of 2TB disk space and 64GB memory. After some experimentation, Bryan found that the compression built into HBase was a little slow, and that compressing the data in the client helped (less data going over the network, less processing for HBase when data is stored as-is).  
  
Speaking of databases, the next talk was by two developers at Facebook who are working on Hive. It sounded to me like another database implemented on top of HDFS, with yet another query language. The good news was it resembled SQL and supports streaming (to programs written in languages other than Java). As far as I can tell, the project is not open source, so this is of little interest to me. Sure, I like hearing about these projects, but as with the Microsoft presentation, it's all just "research" until you share your work with others to enable active discussion and collaboration.  
  
Jinesh Varia from Amazon then presented on their use of Hadoop in relation to Amazon Web Services. He primarily spoke of the architecture of EC2 and SQS. A couple of interesting points were that SQS uses message queues to deal with machine failure, and S3 is a bottleneck when used from EC2. No word on how they are planning to address that. I got the impression that Amazon uses their own infrastructure extensively, with basically everything running on EC2. Definitely an interesting talk by an energetic speaker.  
  
I'll finish up with this mini-series on the Hadoop Summit in my next post.
