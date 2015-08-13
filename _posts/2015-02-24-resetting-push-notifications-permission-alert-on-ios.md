---
layout: post
title:  "Resetting Push Notifications Permission Alert on iOS"
date:   2015-02-24 16:00:00
permalink: /3
---
Well, it's just ridiculous. Here's how to do it:

>If you want to simulate a first-time run of your app, you can leave the app uninstalled for a day. You can achieve the latter without actually waiting a day by following these steps:
>
>Delete your app from the device.
>Turn the device off completely and turn it back on.
>Go to Settings > General > Date & Time and set the date ahead a day or more.
>Turn the device off completely again and turn it back on.
*[Troubleshooting Push Notifications](https://developer.apple.com/library/ios/technotes/tn2265/_index.html)*

And this *convenient* piece of trickery isn't even mentioned in  [Local and Remote Notification Programming Guide](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Introduction.html)! I wish there were a simple, app-specific option for this in the Developer menu of Settings.app. Doing it programmatically within the app would be *too easy* (and  prone to misuse). Though it could be a private API call meant to be only used during development.
