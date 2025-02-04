+++
date = '2010-12-19T01:52:44-05:00'
title = 'Cygwin: Fatal error - unable to remap'
slug = 'cygwin-unable-to-remap'
description = "I love using Cygwin to get access to a Unix shell on Windows, but this error happens pretty frequently on Vista and Windows 7. Here's some information and how to fix it."
tags = [
    "windows",
]
+++

I love using Cygwin to get access to a Unix shell on Windows, but often you'll run into this fatal error on Vista and Windows 7:

```text
*** fatal error - unable to remap \\?\C:\cygwin\lib\python2.6\lib-dynload\time.dll to same address as parent
```    

This failure can happen with just about any DLL, and from running just about any command (SSH, source control, etc).  However, the failure message will be the same.  Here's some information and how to fix it.

Doing a Google or Bing search will turn up many results for this error, but I wanted to also put this information here for my own benefit (plus, many of the top results point to mailing list discussions telling people to search for the fix!).

This problem is the result of an unfortunate interaction with a DLL security feature added in Windows Vista.  A good discussion can be found in a [discussion on Superuser](http://superuser.com/questions/194529/cygwin-fatal-error-unable-to-remap-what-does-it-mean).

Finally, the fix:

1. Close all open Cygwin windows
2. Open a Windows command prompt
3. Navigate to the Cygwin bin directory (i.e. C:\cygwin\bin)
4. Run the following commands:

```text
$ c:\cygwin\bin>ash
$ ./rebaseall
```

5. Wait for the rebase to finish, then restart your Cygwin session
