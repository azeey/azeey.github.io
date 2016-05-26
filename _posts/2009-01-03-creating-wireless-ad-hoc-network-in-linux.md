---
title: Creating wireless ad-hoc network in Linux
layout: post
kind: article
tags:
- ad-hoc
- linux
- wifi
excerpt: Creating a wireless ad-hoc network on linux is a fairly simple task
---

Creating a [wireless ad-hoc](http://en.wikipedia.org/wiki/Ad-hoc_network") network on linux is a fairly simple task. The
only problem might be with the wifi device drivers as some may not support this feature. Make sure the normal wifi
connection ( connection to an access point) works. If you have two Machines A and B this is what you would do.

### Step 1: Change wifi interface configuration to ad-hoc

On both machines:

    iwconfig wlan0 mode Ad-hoc
    iwconfig wlan0 essid MyWifi

You can also set other options such as encrypting with the iwconfig command.  Check `man iwconfig`

In my case, the mode change command gives a resource busy error if wlan0 interface is up. That is, if you can see wlan0 when you do ifconfig, then your wifi interface is up.  Here is what I get:

` Error for wireless request "Set Mode" (8B06) : SET failed on device wlan0 ; Device or resource busy.`

The only way I found around this problem is to do the iwconfig commands before I configure the wifi interface with ifconfig.

### Step 2: Set the IP addresses 

On Machine A:

    ifconfig wlan0 192.168.1.1 netmask 255.255.255.0

On Machine B:

    ifconfig wlan0 192.168.1.2 netmask 255.255.255.0

You can choose different IP addresses but make sure you have the right subnet mask.

You should be set. You can now ping.
