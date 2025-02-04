+++
date = '2010-12-20T03:07:20-05:00'
title = 'Using Windows to access a Linux virtual machine by its host name'
slug = 'using-windows-to-access-linux-host-name'
description = "Initially, I needed to use an IP address to access a Linux virtual machine.  This post details how to setup Samba on the VM to join in the NetBIOS action."
tags = [
    "windows",
    "linux",
]
+++

In my [previous post]({{% ref "2010-12-linux-vm-for-web-development" %}}), I talked about using a Linux virtual machine to ease the pain of doing web development in Windows.  There is one additional step I took to greatly ease the pain of interacting with the Linux machine.

Initially, I had to interact with the server using its IP address.  While this process was straightforward for initiating a simple SSH session, it became increasingly frustrating when I needed to access it using HTTP and FTP.  Plus, since my home network uses DHCP, I often needed to log in to the virtual machine console directly in order to determine its new IP.

I'd much rather just reference the Linux machine using its hostname.  This does not work initially, since Windows uses NetBIOS for its name service rather than TCP/IP host names.  An easy way to get in on the NetBIOS action is to install [Samba](http://www.samba.org/).  While that may be overkill as a solution to this problem, it is helpful to have running when working with Linux machines in a Windows environment.

These instructions were written against Ubuntu 10.10 and may require some tweaking depending on which flavor of Linux you are using.  First, we need to get Samba installed.  Run the following from the terminal:

```shell
$ sudo apt-get install samba
```

And you're done!  Assuming the install finished successfully, the Samba services were started automatically.  And, of course, assuming your Windows workgroup uses the default name `WORKGROUP`.  If that's true, Samba will use your machine's host name as its NetBIOS name, and you should be able to ping the Linux machine now.

If you need configure the name of your workgroup or want to use a different NetBIOS name, edit the file `/etc/samba/smb.conf` and make sure this information is present:

```text
workgroup = {WORKGROUP_NAME}
netbios name = {NAME_YOU_WANT}
```

If you are trying to configure Samba in an Active Directory domain instead of a Windows Workgroup, then your life just got a lot more complicated.  I'll refer you to [this post](http://www.terminally-incoherent.com/blog/2007/10/03/access-linux-workstation-by-hostname-on-a-windows-network/) by Luke Maciak that got me up and running, as he also has instructions for setting up Samba with Active Directory.

But what if I don't want to install Samba?
------------------------------------------

Certainly a fair request.  If you do not want to install Samba on the virtual machine, then there is a workaround that requires a little manual work:

1. Configure the virtual machine to use a static IP address
2. Create an entry in your /etc/hosts file to create an alias to the static IP address

    ```text
    192.168.1.1 hostname
    ```

**Important note:** In Cygwin, the `/etc/hosts` file is just a link to the Windows hosts file at `%WINDIR%\System32\drivers\etc\hosts`, so you will want to edit that file directly.
