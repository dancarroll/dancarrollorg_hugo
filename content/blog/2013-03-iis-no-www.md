+++
date = '2013-03-22T00:45:00-05:00'
title = 'Configuring No-WWW for IIS on Azure'
slug = 'iis-no-www'
description = "Some tips for configuring setting up IIS' Web.Config file to ensure a good canonical name."
tags = [
    "windows",
    "coding",
    "azure",
]
+++

As part of the rollout of my site redesign, I've also switched hosts from [WebFaction](http://www.webfaction.com/) to [Windows Azure](http://www.windowsazure.com/).  WebFaction was a great value for a shared host, and I'll probably continue to use it in the future for small Python/Django projects, but I've been experimenting with Azure on some other projects and took the opportunity to make the switch.

Where'd my .htaccess go?
------------------------
[Azure Web Sites](http://www.windowsazure.com/en-us/home/features/web-sites/) use IIS as a web server, not the more common Apache (or Nginx) server widely used across the Unix world.  While Azure handles all of the server setup and maintenance for many cases, you'll still need to get your hands dirty if you need custom handling.

One of the big behaviors I needed to maintain in the migration was to continue adherence to the [No-WWW philosophy](http://no-www.org/).  This means making dancarroll.org the canonical name of my site, with www.dancarroll.org redirecting to the no-www version.

I had this set up on the previous iteration of the site using an Apache .htaccess file.  It was very easy to find examples of how to do this across the web.

```text
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} ^www\.(.+)$ [NC]
RewriteRule ^(.*)$ http://%1/$1 [R=permanent,L]
```

IIS, however, does not use .htaccess files.  Instead, I needed to create a [Web.config](http://en.wikipedia.org/wiki/Web.config) file with the appropriate settings.


IIS URL Rewrite
---------------
Luckily, IIS has its own [URL rewriting module](http://www.iis.net/downloads/microsoft/url-rewrite).  And, even luckier, Azure Web Sites get this module configured automatically.

To get this set up, you need to create a file named `Web.config` in the wwwroot directory of your Azure deployment.  I was only able to find a few examples of doing no-www redirects for IIS, but all of them hardcoded the domain name in the file.  This wasn't acceptable, as I like to reduce maintenance and use a similar configuration across many different web sites.

Here's what I came up with:

```xml
<?xml version="1.0"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Canonical Hostname" stopProcessing="false">
          <match url="(.*)" />
          <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
            <add input="{HTTP_HOST}" pattern="^(www\.)(.*)$" />
          </conditions>
      <action type="Redirect" url="http://{C:2}{REQUEST_URI}" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

And that's it!  I found that Azure would recognize the file modification and start using it, but you may need to manually restart the site using the Azure management portal.

Breaking it down
----------------
The [documentation for IIS URL Rewrite](http://www.iis.net/learn/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) is decent, but I wanted to provide a few quick notes about what is going on here.

```xml
  <match url="(.*)" />
  <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
    <add input="{HTTP_HOST}" pattern="^(www\.)(.*)$" />
  </conditions>
```

The `match` element defines the pattern that will be used to find URLs to be processed.  In this case, the regular expression used captures all URLs.  A condition is then created to provide additional refinement to which URLs will have a rewrite action taken.  Here, the condition only looks at the server variable `HTTP_HOST`, which requires matching the *host domain* (not the full URL).  The regular expression used only matches hosts starting with `www.`.

```xml
  <action type="Redirect" url="http://{C:2}{REQUEST_URI}" redirectType="Permanent" />
```

If there was a successful match, then an action is taken for that particular URL.  In this case, the action is a redirect, with the "Permanent" `redirectType` mapping to an HTTP 301 status code.  The `url` attribute defines the redirect URL, and I'm using two features here to generate it.

`{C:2}` is a back-reference, which as its name implies, refers back to data captured previously in the rule.  The `C` states that we are referring to something captured by a condition (as opposed to the rule match), and the `2` is the second group match (0 is the full pattern, 1 is the `www.` part, and 2 is the remainder of the host).

Finally, `{REQUEST_URI}` is a another server variable that IIS lets us reference, and it contains the full path to the requested document.  If the URL is `http://www.dancarroll.org/blog/`, then `REQUEST_URI` is `/blog/`.

Bonus Rules
-----------
A couple of extra URL rewrite rules I am using that are worth sharing.

### Forcing lowercase URLs

```xml
  <rule name="Convert to lower case" stopProcessing="true">  
    <match url=".*[A-Z].*" ignoreCase="false" />  
    <action type="Redirect" url="{ToLower:{R:0}}" redirectType="Permanent" />
  </rule>
```

### Redirecting default documents
For default documents (i.e. index.html), it is nice to redirect a request to `domain.com\dir\index.html` to its parent directory, `domain.com\dir\`.  This also help to ensure there is only a single canonical name for any document.

```xml
	<rule name="Default document - html" stopProcessing="true">
	  <match url="(.*)index.html" />
	  <action type="Redirect" url="{R:1}" redirectType="Permanent" />
	</rule>
```
