---
author: nlfiedler
comments: true
date: 2007-09-04 15:03:00+00:00
layout: post
slug: perl-is-not-for-dummies-im-living-proof
title: Perl is not for dummies, I'm living proof
wordpress_id: 52
tags:
- dummy
- perl
- ruby
---

Let’s start with the following (ugly) Perl code snippet.  


  
    
    <code>my $xs = new XML::Simple(ForceArray => 1, KeyAttr => [ ]);<br></br>my $xml = $xs->XMLin($res->content);<br></br>$service_id = $xml->{service}->[0]->{id}->[0]->{content};<br></br>foreach my $item (@{$xml->{service}->[0]->{activities}->[0]->{activity}}) {<br></br>if ($item->{name}->[0] eq $activity) {<br></br>  $activity_id = $item->{id}->[0]->{content};<br></br>  last;<br></br>}<br></br>}</code><br></br>

  

Where to begin? Actually, I want to start with the fact that trying to understand what’s happened to Perl in the years between the time I first learned and enjoyed using it, and the present time, is like trying to understand a crazy person. At first they sort of make sense, and even seem familiar, but then they say something that doesn’t make sense. And you wonder, “What the hell was that?”, and because you have to know what was intended, you struggle, turning your brain inside-out to understand.

   

This all started last week when I volunteered myself to write a Perl module for a set of access control, authentication, and authorization handlers for Apache (collectively known as AAA). At first this seemed to be easy enough, I had used Perl for several years off-and-on, so I felt comfortable with it. That, as I have come to realize, was long enough ago that the memory has been overwritten by Java and Ruby.

   

Learning about `mod_perl` and how to write Perl handlers for Apache was the easy part, surprisingly enough. The painful bits were trying to understand why Perl is so stupid about `use` and methods that can’t be found just because I didn’t `use` some damn class. Ruby doesn’t have this problem—if the object has a method with a given name, you can invoke it. There’s no need to include, import, require, or “use” anything. The method is there whether you know it or not. Perl, it seems, has to be told that it’s okay to invoke the method. What’s up with that?

   

The other major pain point was the `XML::Simple` library. It seems to return these objects that kinda look like hashes and lists, but they’re really something else. And since this is Perl, and not Ruby, I have to access them differently than I would if they had been hashes and arrays. Just iterating over an array of elements took me over an hour to figure out (the fourth line in the snippet above). Naturally, the answer was buried somewhere in the FAQ, but of course I had to bang my head on the problem for a while before I turned to the FAQ. Why doesn’t the documentation have any decent examples? Why can’t it just work like it’s supposed to? If this were Ruby, I’d have been done last Friday.

   

I realize all of this is my fault. Perl is wonderful, it’s been around for ages, billions of people knock themselves out using it every day, so this is clearly a problem with me. I’m a dummy, I’ll be the first to admit that. My conclusion is, Perl is not for dummies.

   

Of course, that implies that Ruby _is_ for dummies. Uh, yeah… that’s not what I meant. Ruby fits me and my way of thinking much better than Perl. Now that I think about it, Ruby actually suits me better than Java, too. Now if only `mod_ruby` were as up-to-date as `mod_perl`, then I could rewrite everything I spent the last three days writing in the span of 15 minutes.
