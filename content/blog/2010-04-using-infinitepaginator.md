+++
date = '2010-04-26T23:34:44-05:00'
title = 'Using InfinitePaginator to improve Django paging performance'
slug = 'using-infinitepaginator'
description = "Two classes from the django-pagination project make paging objects easy."
tags = [
    "coding",
    "django",
]
+++

One of my favorite Django projects is [django-pagination](http://code.google.com/p/django-pagination/).  It provides some tools to make it incredibly easy to add paging support for long lists of objects.  There are, however, a few considerations to make before using it in an application.  Using it requires adding additional middleware, which imparts additional overhead to all of the requests and responses processed by a Django app.  Also, in order to calculate the number of pages, django-pagination counts the number of rows in the model's table every time.

While my site does not necessarily require screaming performance, I do like to squeeze as much out of my server as possible.  For my paging needs, I do not need an exact count on the number of pages available.  I provide a monthly archive on the site's sidebar, so for paging I only really care whether there is a previous or next page available.

Using InfinitePaginator
-----------------------
Luckily, django-pagination provides another option.  I took the `InfinitePaginator` and `InfinitePage` classes from [paginator.py](http://code.google.com/p/django-pagination/source/browse/trunk/pagination/paginator.py).  These allow for easy paging in the cases where it does not matter how many pages there are.  To determine if there are any additional pages, it simply performs another `SELECT` query for one additional object past the current page.  If it succeeds, there must be at least one more page.  This query can be much quicker than a full `COUNT` on the database table.

Since I only wanted a small part of the project, I copied those two classes into my own project.  Using it then requires no additional dependencies or configuration when deploying to a new machine.  Here's a quick example of how to use it:

```python
from django.shortcuts import render_to_response
from django.template import RequestContext
from django.http import Http404
from mysite.models import Entry
from mysite.paginator import InfinitePaginator

def entry_list(request):
    queryset = Entry.objects.all()

    # Make sure page request is an int.  If not, deliver first page.
    try:
        page = int(request.GET.get('page', '1'))
    except ValueError:
        page = 1

    paginator = InfinitePaginator(queryset, 10)
    try:
        entries = paginator.page(page)
    except:
        raise Http404

    return render_to_response('entry_list.html',
                entries.create_template_context(),
                context_instance=RequestContext(request))
```

Pretty straightforward, right?  I try and grab the current page from the request, or set it to 1 if no page is specified in the URL.  I then create the `InfinitePaginator` object and set the page.  If the page is invalid (such as being out of range), I raise a `404` error.

Next Steps
----------
On the next to last line, I call `create_template_context()`, which is not a part of the `InfinitePage` class.  I'll cover what that does in a future post covering improvements and extensions to `InfinitePage`.
