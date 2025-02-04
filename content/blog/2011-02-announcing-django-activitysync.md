+++
date = '2011-02-15T23:25:46-05:00'
title = 'Announcing django-activitysync'
slug = 'announcing-django-activitysync'
description = "Django-activitysync provides easy-to-use extensible social activity aggregation for Django projects."
tags = [
    "coding",
    "django",
]
+++

Today I'm happy to announce the release of my first reusable Django app, [django-activitysync](http://pypi.python.org/pypi/django-activitysync/). The app allows easy integration of social network activities and status updates into Django projects. I've been using it to power the activity section of this website for the past year and have been eager to decouple it into a separate application.

What it provides
----------------
Django-activitysync can provide functionality similar to other [lifestream](http://en.wikipedia.org/wiki/Lifestreaming) solutions, but its real power is as an extensible framework for aggregating, storing, and displaying any type of simple item. It currently provides built-in support for Twitter status items, Twitter searches, Google Reader shared items, and Reddit liked items.

Adding support for a new network or activity type is as simple as implementing a new activity provider. Providers consist of 5 functions, one of which returns a list of simple objects containing the activity detail. Django-activitysync takes care of storage and duplicate detection.

How to use it
-------------
Django-activitysync is available as an installable Python package [on PyPI](http://pypi.python.org/pypi/django-activitysync/). You will also find detailed documentation there on installation, configuration, and use. The source code itself is also available in a [GitHub repository](https://github.com/dancarroll/django-activitysync).

Future plans
------------
This announcement marks the initial release of django-activitysync. I plan to continue supporting it with additional features, new social networks, and bug fixes. Feel free to open issues on the GitHub repository if you encounter bugs or have feature suggestions.
