+++
date = '2011-01-17T00:24:11-05:00'
title = 'Accessing and debugging the Django development server from another machine'
slug = 'debugging-django-dev-server'
description = "In continuing my series about Django development on a Linux VM, I make a few tweaks to some Django settings to make debugging easier from the host machine."
tags = [
    "coding",
    "django",
]
+++

In continuing my series about setting up a Linux virtual machine for Django development (see parts [one]({{% ref "2010-12-linux-vm-for-web-development" %}}) and [two]({{% ref "2010-12-using-windows-to-access-linux-host-name" %}})), I wanted to share another tip for accessing the VM from the host machine.

Set up development server to listen to external requests
--------------------------------------------------------
By default, when using the Django management `[runserver](http://docs.djangoproject.com/en/1.2/ref/django-admin/#runserver-port-or-ipaddr-port)` command, the development server will only listen to requests originating from the local machine (using the loopback address `127.0.0.1`). Luckily, the runserver command accepts an IP address and port. Specifying `0.0.0.0` will allow the server to accept requests from any machine:

```shell
$ python manage.py runserver 0.0.0.0:8000
```

I do not have to worry about security issues with the development server listening to all requests, since the VM is protected from external access by a firewall.

Set up server to send debug information to local network
--------------------------------------------------------
While the first step will allow us to access the development server from the host machine, we will not be able to see debugging information (for example, the [django-debug-toolbar](https://github.com/robhudson/django-debug-toolbar) will not be displayed on the host machine, even if `[DEBUG](http://docs.djangoproject.com/en/1.2/ref/settings/#debug)` is set to True). Django uses another setting, `[INTERNAL_IPS](http://docs.djangoproject.com/en/1.2/ref/settings/#internal-ips)`, to determine which machines are allowed to view debugging information.

For a typical installation, I set `INTERNAL_IPS` to only specify `127.0.0.1`, allowing me to easily debug Django apps running on the local machine.

```python
  INTERNAL_IPS = ('127.0.0.1',)
```

Now, since the setting is a tuple, we could easily add the IP address of the host machine and call it a day. We would run into the same problem as the last post, which is the reason I [installed Samba in order to access the VM using its host name instead of IP address](/blog/2010/12/using-windows-to-access-linux-host-name/). Whenever the machine gets a new IP address from the DHCP server, I would have to update the setting. I found a great solution to this problem in this [snippet](http://djangosnippets.org/snippets/1380/), which adds wildcard support to `INTERNAL_IPS`. I personally put this code inside an if block, so that it only executes in a development setting. Here is the code from the end of my settings.py file:

```python
  if DEBUG:

      from fnmatch import fnmatch
      class glob_list(list):
          def __contains__(self, key):
              for elt in self:
                  if fnmatch(key, elt): return True
              return False

      INTERNAL_IPS = glob_list(['127.0.0.1', '192.168.*.*'])
```

Now, I can access and debug Django projects using the development server from any machine on my local network.
