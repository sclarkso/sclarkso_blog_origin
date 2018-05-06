---
author: sclarkso260218
comments: true
date: 2018-04-05 00:28:56+00:00
layout: post
link: https://sclarkso860540808.wordpress.com/2018/04/05/variable-expansion-in-sudo-over-ssh-with-passwords/
slug: variable-expansion-in-sudo-over-ssh-with-passwords
title: Variable Expansion in Sudo over SSH with Passwords
wordpress_id: 44
tags:
- ssh
- tips
---

The trick is to set the ssh command as a variable so that the expansion happens at the correct time.

    
    #!/bin/bash
     cmd="echo XXXXXXXX | sudo -S mongo 127.0.0.1/admin -u admin -p $pass --eval 'printjson(rs.conf())'"
     ssh -q -o BatchMode=yes -o ConnectTimeout=10 -o StrictHostKeyChecking=no user@host $cmd
