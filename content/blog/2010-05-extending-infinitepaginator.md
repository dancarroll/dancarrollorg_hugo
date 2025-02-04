+++
date = '2010-05-04T23:48:52-05:00'
title = 'Extending and improving InfinitePaginator'
slug = 'extending-infinitepaginator'
description = "There are a few tweaks we can make to the InfinitePage class to make it easier to use, and further improve its performance."
tags = [
    "coding",
    "django",
]
+++

In the [previous entry]({{% ref "2010-04-using-infinitepaginator" %}}), I explained how to use the `InfinitePaginator` class from the [django-pagination](http://code.google.com/p/django-pagination/) project.  I wanted that article to focus strictly on integrating the functionality into another application, leaving out the additional tweaks I had made.

Setting up template context
---------------------------
When it comes time to writing a template for a paged object list, there are numerous pieces of data needed: the objects on the page, whether there is a previous page, and whether there is a next page.  We can get all of that information from the `InfinitePage` object in each view and manually add all of it to the template context, or we could directly access that data from the page object itself from the template.  Personally, I wanted a consistent experience, so I added a helper method to the `InfinitePage` class.

```python
  def create_template_context(self):
      return {
          'object_list': self.object_list,
          'has_next': self.has_next(),
          'has_previous': self.has_previous(),
          'next': self.next_link(),
          'previous': self.previous_link(),
      }
```

This function returns a dictionary with the contents of the page, boolean values specifying whether there are previous or next pages, and strings specifying the URLs to the previous and next pages.  Then it is easy to call this function whenever you need to set up the template context:

```python
    return render_to_response('entry_list.html',
                page.create_template_context(),
                context_instance=RequestContext(request))
```


Improving the performance of has_next
-------------------------------------                
Technically, we only need to call `has_next()` once per page object in order to set the context.  At one point during the development of this site, however, I was accessing `page.has_next()` directly twice in the template.  I noticed that each of these calls generated its own SQL query, which seemed unnecessary.  Let's take a look at the original code for the function:

```python
  def has_next(self):
      """
      Checks for one more item than last on this page.
      """
      try:
          next_item = self.paginator.object_list[
              self.number * self.paginator.per_page]
      except IndexError:
          return False
      return True
```

As we can see, this function attempts to access the next index beyond what is on the current page (since indices for Python arrays are 0-based).  Unfortunately, each time this happens causes another SQL query.  To avoid this hit, we would need to cache the value of that query.  So let's go ahead and do that!

In order to store the cached value, I needed to add a new class variable.  `InfinitePage` extends Django's `Page` class, but does not override its `__init__` function.  So let's add that in, along with a `cached_has_next` variable:

```python
class InfinitePage(Page):
    def __init__(self, object_list, number, paginator):
        self.object_list = object_list
        self.number = number
        self.paginator = paginator
        self.cached_has_next = None
```

Now let's take a look at how `has_next()` changes:

```python
  def has_next(self):
      if self.cached_has_next != None:
          return self.cached_has_next

      try:
          next_item = self.paginator.object_list[
              self.number * self.paginator.per_page]
      except IndexError:
          self.cached_has_next = False
          return False

      self.cached_has_next = True
      return True
```

When `cached_has_next` is `None`, then we can assume `has_next()` has never been called.  If `has_next()` has been called previously, then `cached_has_next` would be true or false and we can avoid an additional query.

The code
--------

All of the code for this site is hosted on Bitbucket.  Specifically, you may want to get the [updated version of InfinitePage](https://github.com/dancarroll/dancarrollorg_django/blob/master/mysite/blog/paginator.py).
