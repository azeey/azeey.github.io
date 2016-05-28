---
title: ! 'Termnetd: Network serial port daemon'
layout: post
kind: article
tags:
- linux
excerpt: Termnetd (aka termpkg apparently) is a cool little application that lets you stream serial port data over the network
---

Termnetd (aka termpkg apparently) is a cool little application that lets you stream serial port data
over the network. It's a terminal server daemon that exposes the serial port and tty devices directly to a network port.

The setup is pretty simple. If using Ubuntu, you can just use:

    sudo apt-get install termnetd

Otherwise, you can build from source by downloading it from [http://www.linuxlots.com/~termpkg/](http://www.linuxlots.com/~termpkg/)

## Configuration

Once installed, the configuration file, **/etc/termnetd.conf**{:.file}, can be edited to configure which serial port maps to which network port. It can be used to set serial port connection settings as well.
The format is as follows:

    <IP port>:<state>:<device>:<termios options>;

Example:

    3000:on:/dev/ttyS0:B115200 CLOCAL IGNBRK CRTSCTS CS8 CREAD;

In Ubuntu, **/etc/default/termnetd**{:.file}, there is a `NO_START` option that can be set to let termnetd start automatically at
boot up.

You can also refer to [termnetd(1)](http://manpages.ubuntu.com/manpages/natty/man1/termnetd.1.html)

## Usage

Start termnetd by running

`termnetd`
or
`/etc/init.d/termnetd start`
from the command line. From another terminal use telnet or netcat to receive the data

`nc localhost 3000`

I work with embedded systems and termnetd has been great for me. I am now able to run this on the target and use python
or any other flexible interpreted language on the host side.
