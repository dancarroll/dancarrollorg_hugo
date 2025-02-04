+++
date = '2010-03-15T22:24:30-05:00'
title = 'Using two-digit months in the URL configuration for a Django blog application'
slug = 'using-two-digit-months'
description = "A how-to for making clean, nice looking URLs in a Django blog.  No three letter month abbreviations here!"
tags = [
    "coding",
    "django",
]
+++

This blog is developed in Python using the [Django](http://www.djangoproject.com/) web framework.  The great thing about Django is that it has a pretty vibrant community using it.  When starting a new application, there probably are numerous other projects out there that you could reference for help and inspiration.

Some things, however, you just have to figure out on your own.  One problem I had when developing this blog was getting the URLs to look the way I wanted.  A lot of Django blogs, including the [official project blog](http://www.djangoproject.com/weblog/), use a format similar to `/2010/Jan/31/title`.  I wanted to use `/2010/01/title` for two reasons:

1. I don't plan on posting enough new entries every month to warrant grouping each entry into a day of the month.
2. I prefer two-digit numeric months over single digit months, and much more over three-letter abbreviations for the month.

Implementation
--------------
First things first, we need to set up the correct URL matching in `urls.py`:

```python
urlpatterns = patterns('',
    url(r'^(?P<year>\d{4})/(?P<month>\d{2})/(?P<slug>[-\w]+)/$',
        view=mysite.blog.views.blog_entry_detail,
        name='blog_entry_detail'),
)
```

To actually get this working, though, there are a few other things that need to be done.  The URL configuration above points to the `blog_entry_detail` view, which is in the `views.py` for my blog application.

Django helps a new project get up-to-speed quickly by providing generic views to handle common scenarios.  Initially, I used the `django.views.generic.date_based.object_detail` generic view for my blog entries.  This view, however, is designed to work by specifying year, month, day, and title.  From our URL configuration, we only know the year, month, and title.  So, I needed to write my own view to handle displaying blog entries.

Here is the code for my `blog_entry_detail` view:

```python
from django.shortcuts import render_to_response
from django.template import RequestContext
from mysite.blog.models import Entry

def blog_entry_detail(request, slug, year, month,
    template_name="blog/entry_detail.html"):

    entry = Entry.objects.filter(
        slug = slug,
        pub_date__year = int(year),
        pub_date__month = int(month),
    )
    if not entry:
        raise Http404
    
    return render_to_response(template_name,
        {"object": entry[0],},
        context_instance=RequestContext(request))
```

The final piece of the puzzle lies in the blog's `models.py` file.  We want to implement a `get_absolute_url()` function, with the `@permalink` decorator, to allow easy linking to this entry from other parts of the site.

```python
from django.db import models

class Entry(models.Model):
    ...
            
    @models.permalink
    def get_absolute_url(self):
        return ('blog_entry_detail', None, {
                'year': self.pub_date.year,
                'month': self.pub_date.strftime("%m"),
                'slug': self.slug
            })
```

Note: I have to use the `strftime` function to format the date as a two-digit month.  If I had tried using `self.pub_date.month` instead, there would have been failures trying to match to a URL because that property returns an integer (which will not have a leading zero for a single-digit month).

Final thoughts
--------------
That's it!  Now the URLs for the blog entries are nice and clean.  Plus, when we need to link to the entry in a template, we can call `get_absolute_url()` to generate the correct link instead of hardcoding it.  Perfect!
