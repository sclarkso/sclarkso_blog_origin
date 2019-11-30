---
layout: post
title: Razer Mouse Consistently Disrupts Windows Sleep
categories: [Tech]
tags:
 - tech
 - tips
---

I recently got a new Razer Basilisk mouse as I thought a clutch would be fun to use in FPS games.  The mouse is great other than the fact that it CONSTANTLY woke up my windows 10 pc.  I tried disabling 'Allow this device to wake computer' in device manager, but it still woke it up almost as soon as I put the computer to sleep.  It turns out the device registers as a mouse AND keyboard (for the extra buttons).

The easiest way to track down what devices to disable is to unplug everything you don't want to use to wake up the computer, and then uncheck "allow this device to wake computer" on all other devices.

For the Basilisk, these are the 3 devices you need to uncheck:

![device-manager](/assets/images/dev-man.PNG)

![allow-wake](/assets/images/dev-man1.PNG)
