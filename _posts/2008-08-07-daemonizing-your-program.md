---
title: Daemonizing your program
layout: post
kind: article
tags:
- daemon
- init.d
- linux
- rc script
excerpt: So you have written the coolest server in the world and you want it to start every time you boot your machine. How do you go about doing that?
---

So you have written the coolest server in the world and you want it to start every time you boot your machine. How do you go about doing that?

## 1. Edit your program.
The first step is to edit your program to make it "daemonizable".
The easiest way I have found it to make your program fork a child process and exit.  Have the child process write its pid file to a .pid file somewhere, usually in `` /var/run ``

## 2. Use a template script.
There are probably actual templates for this but i just used one of the scripts in `` /etc/init.d/ ``, namely lighttpd. Just copy the script and rename it with your program's name. 
Then change everything that says lighttpd to the name of your program. Try doing `` /etc/init.d/$YOURPROGRAM `` start and stop and see if that works.

## 3. Make it start at boot up
This is pretty simple. If you followed the above two steps, all you have to do next is:

    update-rc.d $YOURPROGRAM defaults


