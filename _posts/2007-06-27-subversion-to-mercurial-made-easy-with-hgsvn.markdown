---
author: nlfiedler
comments: true
date: 2007-06-27 16:08:00+00:00
layout: post
slug: subversion-to-mercurial-made-easy-with-hgsvn
title: Subversion to Mercurial made easy with hgsvn
wordpress_id: 45
tags:
- hgsvn
- mercurial
- subversion
---

After experimenting with [Tailor](http://progetti.arstecnica.it/tailor) to convert my five years of Subversion history into a new Mercurial repository, I started looking for alternatives. Tailor has a problem at the moment, where it blows up if any entry in the Svn repository changed names (i.e. a file or directory was moved/renamed). While I was able to deal with this during the conversion process, by manually correcting the mistakes and resuming the conversion, it was tedious and slow. Sure, I sent mail to the Tailor mailing list, but I didn’t get a response, so I took that to mean I was on my own.

Then I remembered seeing a [blog entry](http://ww2.samhart.com/node/49) about converting an Svn repo to Hg, in which the author used a different set of Python scripts, called [hgsvn](http://cheeseshop.python.org/pypi/hgsvn). One distinct advantage of these scripts is that they can pull out a subtree from an Svn repo, whereas Tailor only seems to deal with entire repositories (if it does, it’s too hard to tell from the documentation). Sometimes a general-purpose solution is too general. Tailor is meant to be the Swiss Army knife of SCM conversion, going to and from anything imaginable. All I need is Svn to Hg, and I need it to work correctly, which hgsvn does.

Now that I am able to correct my original mistake of having a single Svn repo for all of my projects, I can play with configuring web access to the Mercurial repositories. Following [Bill’s](http://www.dehora.net/journal/2007/04/mercurial_part_ii.html) example, I created a simple Apache configuration that redirected to the [hgwebdir.cgi](http://www.selenic.com/repo/hg/raw-file/tip/hgwebdir.cgi) script.

~~~
ScriptAlias /hg /var/www/cgi-bin/hgwebdir.cgi
Order allow,deny
Allow from all
Options ExecCGI
AddHandler cgi-script .cgi
AuthType Basic
AuthName "Mercurial Repository"
AuthUserFile /etc/hgpasswd
AuthGroupFile /dev/null
Require valid-user
~~~

My one innovation on Bill’s setup, which appears to be copied from the Mercurial wiki, is to use `LimitExcept` instead of `Limit`. The reason for that is the list of HTTP methods that are acceptable for anonymous access is smaller than the set that is not acceptable (e.g. PUT, POST, DELETE). And that set will never change, whereas the set of HTTP methods may (someday) change. That, after all, is the advice from the Subversion developers, and it’s worked for me for a long time. One other thing, I actually don’t use the `AuthUserFile` method, but I have an LDAP directory to act as a Poor Man’s single-sign-on.

**Update:** Thanks to Aristotle, fixed the ScriptAlias line in the configuration above. Indeed, the `ScriptAlias` directive is much simpler than the `ScriptAliasMatch`, probably faster, too.
