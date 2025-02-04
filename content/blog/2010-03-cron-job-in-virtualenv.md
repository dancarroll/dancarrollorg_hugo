+++
date = '2010-03-25T21:06:20-05:00'
title = 'Setting up a cron job to run a Django management command in a Python virtualenv'
slug = 'cron-job-in-virtualenv'
description = "It's pretty straightforward, but it took some trial on my part to get it working the way I want."
tags = [
    "coding",
    "django",
    "linux",
]
+++

For the ~~Activity~~ (*update:* activity removed from redesigned site) section of this site, I wrote a Django management command.  The command connects to the various social network sites being tracked, fetches my activity, and then creates database items.  By caching the items in my own database, I avoid the terrible performance that would come from fetching them anytime someone visits my site.

The only downside is that this command has to be run whenever I want the data updated.  To handle this, I set up a `cron` job to run this command every hour.  Worst case, the activity shown on the site is nearly an hour stale, but the performance and reliability are worth it.

Since I use [virtualenv](http://pypi.python.org/pypi/virtualenv) to set up an isolated Python environment to host this site, the command I needed to use is a little more complicated than normal.  I figured I'd put that here for reference:

```shell
cd <path_to_project>
~/.virtualenvs/<venv_name>/bin/python <path_to_project>/manage.py updateactivities
```

Note that, since my project does not happen to be on the `PYTHONPATH`, I need to switch into the directory first before running the management command.
