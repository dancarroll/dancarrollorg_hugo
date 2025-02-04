+++
date = '2010-12-20T01:38:04-05:00'
title = 'Setting up a Linux virtual machine for easy web development on Windows'
slug = 'linux-vm-for-web-development'
description = "Being a Windows user can be a real drag when trying to use technologies originally designed for the Unix world. There must be an easier way!  Virtualization to the rescue!"
tags = [
    "windows",
    "linux",
    "django",
]
+++

Being a Windows user can be a real drag when trying to use technologies originally designed for the Unix world.  This hardship is especially prominent in web development.  While I do admire [ASP.NET MVC](http://www.asp.net/mvc), and enjoy being able to develop in C# as much as possible, my real passion right now is using [Python](http://www.python.org/) with the [Django](http://www.djangoproject.com/) framework.

For simple applications, I often start out using [Cygwin](http://www.cygwin.com/), the Django development server, and a sqlite database (side note: have you ever tried to get the [MySQLdb library](http://sourceforge.net/projects/mysql-python/) installed in Cygwin?  What a mess!).  But that setup can only take me so far.  Not only is development a little tricky on a Windows box, but deployment and testing can be a mess.  Most of the time, my web apps will be running on a Linux server.  Ideally, I would be able to test everything locally in an environment as close to production as possible (and as a personal choice, without switching my development machine to run Linux).

For a while, I tried to set up my Windows machine to run Apache and MySQL.  That's fairly easy to do with something like [XAMPP](http://www.apachefriends.org/en/xampp.html).  The hard part is finding all of the necessary extensions and bindings to run (if you want to go that route, [this StackOverflow question](http://stackoverflow.com/questions/1000444/run-django-with-xampp-on-windows) is a good starting point).  While I did get everything working in the end, it was a real pain.  My machine was filled with extra applications and services that take up resources simply to provide me with a testing environment, and setting up a new development machine became a very non-trivial task.

Instead, I turned to virtualization.  Rather than try to set up everything on Windows, I can just setup a virtual machine running Linux and configure it the exact same way as my production server.  When I'm not developing, I shut the VM down.  If I get a new computer, I can simply migrate the existing virtual hard disk over to the new box.

There are several virtualization technologies available, but I ultimately settled on [VirtualBox](http://www.virtualbox.org/).  It is extremely easy to use, and seems to be very lightweight.  Right now, I've got an Ubuntu 10.10 VM running on a Windows 7 laptop with a very acceptable impact to performance.

Rather than repeat the steps to get the Linux VM up and running, I'll point you to this great blog post by Louis Simoneau: [Build Your Own Dev Server with VirtualBox](http://blogs.sitepoint.com/2009/10/27/build-your-own-dev-server-with-virtualbox/).  Also, this guide runs through the VirtualBox setup process for Windows: [Installing Ubuntu inside Windows using VirtualBox](http://www.psychocats.net/ubuntu/virtualbox) (that guide references Ubuntu 10.04, but the 10.10 setup is exactly the same).

Once the virtual machine is up and running, you can configure it the same way as any other Linux machine.  I'll have another blog post up shortly about how to communicate with the Linux VM over the network, as there is some extra configuration necessary if you want to avoid using the IP address all the time.
