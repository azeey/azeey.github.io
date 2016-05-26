---
title: Mapping in Linux Network Interfaces
layout: post
kind: article
tags:
- debian
- interface
- linux
- mapping
- ubuntu
excerpt: One really cool feature of this configuration system is the ability to map network interface names to specific configurations
---


If you use one of the many Debian derived distributions such as Ubuntu, you may be familiar with the network interfaces
file located at /etc/network/interfaces.  This file is part of the ifupdown package and gives users a high level access
to configure how their system is connected to the network.  One really cool feature of this configuration system is the
ability to map network interface names to specific configurations.  You can use this to setup several different
configurations and use one of them based on a certian condition.  You can see different examples in the documentation
that is probably installed on your system.  This should be located at `` /usr/share/doc/ifupdown/examples ``. One of the
examples shows how use different configuration based on your ip class while another determines which configuration to
use based on the mac address of network interface.  In this post, I will show how to map the network interface based on
the runlevel of the system.. This was useful to me on my embedded linux platform which had a wireless
interface. I wanted the wifi to be in managed or ad-hoc mode based on the runlevel of the system.

Here is a quote from the manual:

>Stanzas beginning with the word "mapping" are used to determine how a logical interface name is chosen for a physical interface that is to be brought up. The first line of a mapping stanza consists of the word "mapping" followed by a pattern in shell glob syntax.  Each mapping stanza must contain a script definition.  The named script is run with the physical interface name as its argument and with the contents of all following "map" lines (without the leading "map") in the stanza provided to it on its standard input. The script must print a string on its standard output before exiting. 

## Syntax

The syntax for mapping is fairly simple and can be found in the manual (`` man interfaces ``):

    mapping interfacetomap
      script /path/to/script
      map arg1 arg2
      map arg3 arg4

The only confusing thing for me was the lines that start with "map".  It seems that they don't serve any purpose other than being provided to the script on its standard input.  If the script doesn't need any arguments, you won't need the lines starting with "map".

## Determining current runlevel

To do what I wanted, I needed to know what the current runlevel was.
There are many ways to determine this. Here are some of them:

##### Using ``who``

    set $(who -r)

which would give a response like

    run-level 2  2009-03-20 19:21                   last=

you can then use "$2" in your shell script to get the second string.

##### Using ``runlevel``

    set $(runlevel)

and the output would be something like:

    N 2

You would use "$2" as the first method

The runlevel is usually set in /etc/inittab.. We can parse this file to figure out what the default runlevel is.

    RL="$(sed -n -e "/^id:[0-9]*:initdefault:/{s/^id://;s/:.*//;p}" /etc/inittab)"

Since the network is configured while in the "S" run level in my case, the firs two method will output "S", which is not what I wanted.  So I decided to use the third method.

## Putting it all together

The name of my physical interface is ``wlan0`` and I want it to be "brought up" at boot time.  I will have two settings that will set the wifi interface into managed mode or ad-hoc.  Here is my interfaces file:

    auto wlan0
    mapping wlan0
      script /usr/local/sbin/runlevelconfig.sh
      map 2 wlan0-managed
      map 3 wlan0-adhoc

    iface wlan0-managed inet dhcp
    wireless-essid "MyWifi"
    wireless-key  "MyWifiKey"
    # If you want to use pre-up,up, or post-up, you can use the $IFACE variable
    # pre-up iwconfig $IFACE essid MyWifi key MyWifiKey

    iface wlan0-adhoc inet static
    wireless-mode Ad-hoc
    wireless-essid "MyAdhoc"
    wireless-key "MyAdhocKey"
    address 192.168.1.1
    netmask 255.255.255.0

The argument we are passing tells our script to associate runlevel 2 with ``wlan0-managed`` and runlevel 3 to ``wlan0-adhoc``.  **Remember** that it is the output of the script that will be used to map ``wlan0``.  Here is runlevelconfig.sh:

    #!/bin/sh
    set -e
    RL=""
    which=""
    INITTAB="/etc/inittab"

    if [ -r $INITTAB ]; then
        RL="$(sed -n -e "/^id:[0-9]*:initdefault:/{s/^id://;s/:.*//;p}" $INITTAB || true)"
    fi
    if [ ! -n "$RL" ]; then
        exit 1;
    fi

    while read runl scheme; do
        if [ "$which" ]; then continue; fi
        if [ "$runl" = "$RL" ]; then which="$scheme"; fi
    done
    if [ "$which" ]; then echo $which; exit 0; fi
    exit 1

## Testing

In order to test this without rebooting your system, you can save the following text in a file:

    # test.txt
    2 wlan0-managed
    3 wlan0-adhoc

and run the script with this file redirected to the scripts standard input:

    ./runlevel.sh < test.txt

If it is working properly and you are running either runlevel 2 or 3, it should output ``wlan0-managed`` or ``wlan0-adhoc``.  You can change the numbers if your system is running at a different runlevel.
