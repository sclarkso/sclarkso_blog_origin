---
author: sclarkso@redhat.com
comments: true
date: 2018-04-24 00:10:48+00:00
layout: post
slug: worlds-tiniest-openshift-cluster
title: 'World''s Tiniest OpenShift cluster, with a Mobile twist: Part 1'
wordpress_id: 46
categories:
- Tech
---

I decided I needed an OpenShift cluster at home for testing, and instead of doing it the easy way with [Minishift](https://docs.openshift.org/latest/minishift/index.html), I decided I wanted to try to do it the hard way and run it on some sort of development board.   Given that my eventual goal was to run Red Hat's Mobile Application Platform ([RHMAP](https://www.redhat.com/en/technologies/mobile/application-platform)) on the OpenShift on this cluster,  I felt that an ARM-based board was probably outside of my capabilities and I started researching Intel x86 based boards.

There are many x86 development boards on the market today.  The ones I considered, in no particular order were:




  * [MinnowBoard](https://minnowboard.org/)


  * [UDOO x86](https://www.udoo.org/udoo-x86/)


  * [UP board](http://www.up-board.org/)


  * [Jaguarboard](http://www.jaguarboard.org/)


Of the 4, only 2 had models with more than 4GB's of RAM.  OpenShift recommended specifications are 16Gb's of RAM per node, so I thought anything less than 4 would be a stretch.  I chose the UP board based on the fact that it was the same size as a raspberry pi, which would give me many more options for cases and accessories, that it was slightly cheaper, and that it was available for shipping to Australia.

<figure>
  <a href="{{site.url}}/assets/images/20180424_083037.jpg"><img src="{{site.url}}/assets/images/20180424_083037.jpg" alt="my alt text"/></a>
  <figcaption>The 3 nodes and my assistant</figcaption>
</figure>

When the boards arrived I [updated their firmware ](https://wiki.up-community.org/Bios_Update)and installed Red Hat Enterprise Linux (RHEL) 7.4.  I considered using [RHEL Atomic Host ](https://www.redhat.com/en/resources/enterprise-linux-atomic-host-datasheet)but decided on standard RHEL as I have more experience with it.  Alternatively, you can use Centos 7.4 with identical results.

<figure>
  <a href="{{site.url}}/assets/images/screen-shot-2018-04-24-at-10-04-27-am.png"><img src="{{site.url}}/assets/images/screen-shot-2018-04-24-at-10-04-27-am.png" alt="my alt text"/></a>
  <figcaption>Firmware Installation</figcaption>
</figure>

The [install](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/index) was fairly straightforward, the only customisation was trying to determine the filesystem layout.  I needed storage for the OS and a separate volume group for the container images.  The boards have 32Gb of eMMC storage, so I opted for 5 Gb for the OS, 20 Gb for images, and everything else (/run, /boot, swap) in what was left over.

I then started and enabled sshd, set static IP's and hostnames, configured /etc/hosts, and ran subscription-manager (RHEL Licensing) on all the nodes to prepare them for OpenShift installation.  I also created 20 NFS shares on a USB 3 Flash Drive on the 'Master' node for OpenShift.

<figure>
  <a href="{{site.url}}/assets/images/20180420_095502.jpg"><img src="{{site.url}}/assets/images/20180420_095502.jpg" alt="my alt text"/></a>
  <figcaption>Mocked up in case</figcaption>
</figure>

My tiny OpenShift cluster will cost me approximately $2.77 (AUD) to run 24 hours a day for a month.  Each node draws a maximum of 4W, and the switch adds a maximum of 1W.  For comparison, a 3 node cluster built on desktop class workstations with 300W power supplies costs my colleague $192 (AUD) a month.  As a learning platform, that is a significant amount of savings.

I will cover the OpenShift installation in my next post.
