+++
date = '2025-01-11T01:15:00-05:00'
title = 'Migrating from Hyde to Hugo'
slug = 'hugo'
description = "Switching to a new, blazing fast static site generator."
tags = [
    "general",
    "cloudflare",
    "coding",
]
+++

Almost [twelve years]({{% ref "2013-03-redesign" %}}) after the last big site
infrastructure change, it's time for another round. This time is purely
technical, with no intentional visual changes.

Motivation
----------
I haven't posted in a long time, and this blog had largely faded into the
background for me. I was doing some management of my domains, trying to clean
things up after my domains registered with Google [were transferred to
Squarespace](https://9to5google.com/2024/04/05/google-domains-squarespace-controls-migration/).
As part of this, I decided to use Cloudfare as my nameserver, which I've used
for other projects.

I started reading about [Cloudfare Pages](https://pages.cloudflare.com/), which
seemed like a great match for easily deploying a statically-generated site. You
can point Cloudflare to a GitHub repo, and it will automatically kick off a
build after every commit, and deploy the static artifacts. Unfortunately, the
[Hyde framework](https://github.com/hyde/hyde) I was using is no longer
maintained, and still does not support Python 3. Cloudflare Pages does not
have support for Python 2.7.

So I decided it would be a fun project to migrate to a new static site
generator.

Finding a new tool
------------------
Searching around, I quickly settled on [Hugo](https://gohugo.io/) due to its
large community, and reputation for blazing fast speed.

I wasn't quite sure how the easy it would be to migrate my old site template,
so I started off following the [quick start](https://gohugo.io/getting-started/quick-start/)
guide, chose an existing public theme that looked ok, and started copying the
content from my site's old repository. This was pretty easy since both tools
are based on Markdown -- I just needed to slightly adjust each post's metadata.
With less than 20 total posts, I did that manually.

Fundamental differences
-----------------------
Hyde is a very simple generation tool, compared to Hugo which has a much larger
feature set, but also very strong opinions on how things should be structured.
In Hyde, I had a flat folder of templates, and could directly point individual
pages to an arbitraty template.

Hugo has a [very specific structure](https://gohugo.io/getting-started/directory-structure/)
to follow, which it leverages to classify into a [set of page types](https://gohugo.io/templates/types/).
This prevents the organizational structure that I wished to have, but I quickly
adjusted. For example, my previous file hierachy looked like this:

```txt
content/
content/blog
content/blog/2025
content/blog/2025/01
content/blog/2025/01/post.md
```

Having multiple nested directories is [not easily supported](https://discourse.gohugo.io/t/hugo-way-to-create-new-content-in-year-month-subfolders/36557),
so I needed to switch to the following hierarchy:

```txt
content/
content/blog
content/blog/2025-01-post.md
```

Given the limited number of posts, I don't mind this setup too much. However,
if I were a more prolific blogger, I would probably dig in to find a better
way to organize individual post files.

Porting a Hyde theme
--------------------
[Hugo themes](https://gohugo.io/templates/) are also much more structured than
the ad-hoc Hyde layouts. There is a [rigid template lookup order](https://gohugo.io/templates/lookup-order/),
which required some trial-and-error to determine where all of the template
types needed to live in order to get picked up correctly.

Hugo uses a Go templating library, so I needed to tweak all of my previous
Jinja2 templates. This was a bit of manual work, but overall not too bad,
largely because of the [rich documentation available](https://gohugo.io/functions/).

On the plus side, Hugo does have [built-in templates](https://gohugo.io/templates/embedded/)
for Google Analytics and Disqus, both of which are used on this site. Rather
than needing to include the code for those in my templates, I could
just define some simple config parameters and reference the built-in templates:

```go-html-template
<head>
  {{ template "_internal/google_analytics.html" . }}
</head>
```

For posts, I ended up defining one ["single" template](https://gohugo.io/templates/single/),
which is used for both posts and standalone pages like [About]({{% ref "about" %}}).
I need conditional logic to prevent showing Disqus on non-posts. I settled on
excluding pages that appear in the site navigation menu:

```go-html-template
{{ if not .Params.menu }}
{{ template "_internal/disqus.html" . }}
{{ end }}
```

Final result
------------
As before, the code for this site remains open source. I left the old code
in its own repository, and created a new repository for the Hugo-base site
implementation, which you can [find on GitHub](https://github.com/dancarroll/dancarrollorg_hugo).
