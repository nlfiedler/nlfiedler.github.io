---
author: nlfiedler
comments: true
date: 2008-09-07 14:22:00+00:00
layout: post
slug: finally-a-new-product-that-ships
title: Finally, a new product that ships
wordpress_id: 68
tags:
- kenai
- whining
---

During my last 18 months at Sun, I have been working on a project whose name changed several times, and whose purpose changed almost as often. For the time being, it is just another source forge, but at least it "shipped". For all of the projects that I have worked on that were breaking new ground, I believe this is the first one to see the light of day. Another remarkable quality of my part in this project is that I worked on just one area, source code management. The project is called [Project Kenai](http://kenai.com/), and at the moment you need an invitation to create a new project. Given it's relative newness, it's the sanest way to grow the site and scale the infrastructure to meet demand.

Let me give you a brief history of the project, from an insider's point of view. To start with, I was introduced to the newly assembled project as the fifth developer, to work with other developers poached from NetBeans-related products (Creator and Enterprise Pack). We learned we were building a web site, a developer collaboration site, it was eventually called. The plan was to build a prototype for the upcoming JavaOne conference, two months away. We were to learn a new language, Ruby, and a new framework, Rails. None of us had done anything quite like this before, and we going to do it in a very un-Sun-like manner. We were packed into a small room, told in rough terms what to build, and encouraged to use the "agile" development methodology. I use lower-case because we never really knew what Agile was, nor did we ever actually practice it.

The prototype came and went; the demo at JavaOne was cancelled. We got Craig McClanahan and started the entire system from scratch. Instead of a single Rails app that basically did nothing more than scrape HTML from other services (e.g. Hudson, Mailman, WebSVN, MediaWiki), we were to build a set of RESTful web services, all in Rails, each implementing one particular aspect of the system. Because of my familiarity with Subversion, I took on the source code management portion of the project.

Initially almost everything was written in Rails, except that we chose Sympa for the mailing list support, and being written in Perl, the mailing list web service was also written in Perl. My part was still Rails at that time, because Ruby support in Subversion was quite satisfactory. That changed, however, when we took on Mercurial support in addition to Subversion. My first attempt was to invoke the hg script from Ruby and capture the output. This led to dreadful performance and was very buggy. Of my own volition, and on my own time, I learned Python and Django and rewrote the SCM web service so I could properly support both Mercurial and Subversion.

And that was basically the last bit of coding I did for this project, several months ago. Since then I've been fighting all of the integration issues and exploring options for improving the broken deployment process (at this time, it consists of a set of poorly written shell scripts, invoked by hand on each of the production systems, in a long and complicated process). Not developing new code or solving interesting problems, while debating with management about priorities (yes, releasing is good, but having an infrastructure to get you there is at least as important), is very disheartening. Ultimately, this is the reason I have left Sun. The project shipped on the very same day I left the company; it shipped and I shipped out.
