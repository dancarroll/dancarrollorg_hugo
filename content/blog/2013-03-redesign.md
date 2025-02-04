+++
date = '2013-03-16T22:38:05-05:00'
title = 'Redesign'
slug = 'redesign'
description = "Finishing personal projects is hard. The redesign of this site took way longer than expected."
tags = [
    "general",
    "coding",
    "python",
]
+++

This site has been quiet for a long time.  My last post was over a year ago in February 2012.  That post before that was yet another year prior.  With a full-time job and other personal commitments, blog posting and other side projects easily fall off the radar.

Take my site's new design, rolling out today.  I initially started working on it in February 2011, going by the file creation timestamps on my machine.  I worked on it for a week or so, got some feedback, then dropped it.  A year later, I took the big step to actually create a source repository on Bitbucket to make sure the code was backed up somewhere safe (I don't recall making any other progress at the time).

It took me over a year from that point to actually pick up the project and push it through to completion.

The design
----------
Like the previous design of my site, I have taken a lot of inspiration from people in the [Django](http://www.djangoproject.com/) community.  A lot of the design inspiration comes from [Steve Losh](http://stevelosh.com/), who has consistently done an excellent job at clean, readable web design.

I'm not sure what I was thinking with the previous iteration, which featured orange and beige on a darker, almost navy blue background.  That's been fixed now!

The code
--------
Like Steve, I also made a transition in how the site is implemented.  Previously, my site was developed using Django.  Django is a Python-powered web framework, and it gave me tremendous flexibility to add features like a [lifestream](http://en.wikipedia.org/wiki/Lifestreaming).  However, it came with a lot of overhead for a simple blog, and could be unreliable on inexpensive, shared web servers.

This new iteration of the site is built using [Hyde](https://github.com/hyde/hyde), a static web site generator built in Python.  Hyde allows me to work with Python code and templating languages, much like the one used with Django, but the end result is a static HTML and CSS website (rather than a dynamic site backed by executing Python code and databases).  The end product can now be hosted anywhere with relatively good performance.

And like the previous iteration, the new version is still open source.  For ease of browsing both implementations, the new design lives in a [separate code repository on GitHub](https://github.com/dancarroll/dancarrollorg_hyde).

The problems
------------
Another big improvement made during this transition is in ease of use and maintainability for me.  Previously, I tried a few different ways of creating blog posts.  First, I tried writing HTML each time I needed to create a new post, and uploaded it using Django's admin UI.  This was a pain and prone to rendering issues.

Next, I spent a lot of time implementing support for the [MetaWeblog API](http://en.wikipedia.org/wiki/MetaWeblog), which allowed me to create blog posts using applications like [Windows Live Writer](http://en.wikipedia.org/wiki/Windows_Live_Writer). That made post creation much easier for me, but those blogging applications would create ugly HTML with their own special markup, and often required tweaking the HTML by hand to get things to show up correctly.

Finally, regardless of how I created posts, they ended up being saved to a database on my web host. That's not a very safe location, so I needed to make sure to create regular backups.

The solutions
-------------
With Hyde, it was easy to convert all of my blog posts to be written in [Markdown](http://en.wikipedia.org/wiki/Markdown).  When generating the site, the posts get passed through a Markdown filter to convert them to HTML.  As long as I have good CSS defined on how to render what HTML gets produced, I can create a blog post using a simple text editor and know that it will come out looking good on the other side.

**Aside:** Django has built-in support for Markdown, so I could have used it with the site's previous implementation.  However, things would have been more complicated.  There would be performance issues if posts were passed through the Markdown filter for each web request, deployment complexity if I needed to leverage server-side caching, or implementation complexity if both versions were stored (Markdown version for editing, HTML version for serving up web requests).

For the issue of storing and backing up blog posts, content is now part of the codebase itself.  It gets pushed up to Bitbucket, and lives on a few different machines where I have the code checked out.

The future
----------
I feel very accomplished for finally completing this major redesign project, so I hope to actually use this thing more often.  I'll never be prolific, but my goal is to publish at least several posts a year.
