---
layout: post
title: Scheduling kdeconnect
categories: [Linux]
---

I have a smartwatch and use kdeconnect to forward desktop notifications to my phone.  I do not want to receive these notifications before or after hours (computer stays on and we're a 24/7 operation).  You can manipulate kdeconnect from the command line with kdeconnect-cli, but there is no mechanism for scheduling alerts on and off.  My first test used the '--pair' and '--unpair' commands found in kdeconnect-cli, but this deletes all of your in-app settings (i.e. what features are enabled).  This is less than ideal.  This is the solution I come up with.  It involves using crontab to kill the kdeconnect daemon (/usr/libexec/kdeconnectd), then restart the daemon, then run the 'kdeconnect-cli --refresh' command.  There is a bit of a delay needed between starting the daemon and the refresh command.

    30 7 * * 1,2,3,4,5 /usr/libexec/kdeconnectd
    35 7 * * 1,2,3,4,5 /usr/bin/kdeconnect-cli --refresh
    30 17 * * 1,2,3,4,5 /usr/bin/pkill kdeconnectd


