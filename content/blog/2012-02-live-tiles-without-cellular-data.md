+++
date = '2012-02-14T14:23:53-05:00'
title = 'Caveat about WP7 Live Tiles without cellular data'
slug = 'live-tiles-without-cellular-data'
description = "A short note about developing for WP7, and the implications of how the Windows Phone OS manages power usage and Wi-Fi connections."
tags = [
    "coding",
    "windowsphone",
]
+++

I have owned and used a Windows Phone since they were introduced in late 2010.  While I am currently a Microsoft employee, I do love the device as a consumer.  I previously used an iPhone 3G and have not had an desire to go back to that world.  The biggest downside is missing out on some of the big iOS apps, especially games, but the overall experience WP7 provides with improved email, people, and information management has made up for the loss.

While I've owned one for a while, I hadn't had much of an opportunity to develop apps.  As a former iPhone user, I wanted to jump into iOS app development, but was not able to since the development tools were only available on a Mac.  I finally made the commitment and jumped into the mobile development world.  For the past month and a half, I have been using my spare coding time working on Windows Phone development.  Hopefully I will get the chance to blog about my experience, along with tips and tricks.

Meet Wi-Fi, the battery hog
---------------------------
One interesting "feature" of WP7 aims to save gobs of battery life. Whenever the screen is locked, the OS will turn off any active Wi-Fi connection (unless it is being used to stream music/video or download something in the background).  I'm sure this feature is great for battery savings, but it does have one interesting implication that recently hit me.

I have a second phone I just acquired to use for testing my apps.  I only have a single cell phone plan for my main phone, and the second phone connects using Wi-Fi only.  One of my apps uses a background agent to download information and update a live tile.  The live tile will not get updated, since the background agent has no connection to use when the screen is locked (as Wi-Fi is disabled).

The workaround?  Plug your phone in.  Wi-Fi connectivity stays active when the phone is plugged in, regardless of whether the screen is locked.

It would be nice if there was an option to disable this feature, since Windows Phone loses functionality if you don't have a cellular data plan.  There is a [suggestion on the WP7 UserVoice forum to add that option](http://windowsphone.uservoice.com/forums/101801-feature-suggestions/suggestions/2282561-add-option-to-keep-wifi-alive-while-the-phone-is-l), in case you want to go vote for it.

Update (2013-02-27): Windows Phone 8 includes an option to keep Wi-Fi alive when the screen is off.
