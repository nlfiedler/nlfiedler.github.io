---
author: nlfiedler
comments: true
date: 2007-05-28 10:14:00+00:00
layout: post
slug: why-ruby-is-so-appealing-to-me
title: Why Ruby is so appealing (to me)
wordpress_id: 41
tags:
- ruby
---

Having worked almost entirely with the [NetBeans](http://www.netbeans.org/) Platform for the last three years, I have grown accustom to certain limitations in the framework. And being written in Java, the constraints of the language have become second nature to me. Classes in Java are closed, such that a class cannot be modified during runtime. Worse, if a class is marked `final`, it cannot be extended even at compile-time. The architect of NetBeans utilizes these features to his advantage. He wants to make sure no one can break NetBeans by writing an improperly designed module, I guess. I don’t know, I haven’t actually asked him, but that is the most likely answer.

   

This was a real issue for us last year. I was tasked with writing the undo/redo support for the XML Schema editor in NetBeans Enterprise Pack. In particular, we wanted to allow the user to make changes in both the text view, and the graphical view, and undo those changes from any view. Changes made in one view may result in more than one change in the other, and we wanted the undo to behave sensibly no matter how it was used. As you can probably imagine, the undo/redo support in NetBeans was not designed for this use case. It knows only how to work with text, nothing else. To do what we wanted, we would have to work around the constraints put in place both by the Java language, and the NetBeans editor framework. Since nearly everything in NetBeans is either package-private or `final`, it is mighty hard to do what we were doing.

   

Ultimately, we got it to work, but not without a lot of effort. The result is a beautiful hack, one for which I am the lead inventor on a recent patent application (yay, I’m evil).

   

So how does this relate to Ruby? Classes defined in Ruby are “open”, new methods can be added to them at runtime. So if I need a new `blank?` method on the `String` class, I can just define one in my application, and now all instances of `String` will have my method. Neat, huh? “No, no, no, this is terrible, scary, crazy even!” Au contraire! It is lovely, and exactly the sort of thing that would give Yarda a heart attack. He’d never be able to create the “code jail” that he’s created in NetBeans — a vast repository of code that cannot be re-used, only copied and pasted.

   

Can you tell I have some resentment for Java and NetBeans? Don’t get me wrong, they absolutely have their place, and I will always be a NetBeans user. But Ruby makes me a happy hacker, and it may be quite a while before I look at Java development again.
