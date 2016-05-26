---
title: Minicom inside Minicom
layout: post
kind: article
tags: [linux, minicom]
excerpt: In order to run another Minicom session within the one you are running currently, you need to change the command key (Default Ctrl-A).
---

I use Minicom as a serial terminal in most of my embedded systems projects.  Recently I discovered how to use a Minicom
session inside another Minicom session.  It is not a trivial matter if you don't know how to change the settings in
Minicom.   This was helpful when other means of accessing the terminal in the target system such as ssh were
unavailable.

When started without special command line arguments, Minicom defaults to Ctrl-A as the command key combination.  Once
you have issued Ctrl-A, you can press any of several keys which do different things.

![minicom_main_menu]

In order to run another Minicom session within the one you are running currently, you need to change the command key
(Default Ctrl-A). You would do this by doing the following steps:

1. Ctrl-A o

2. Screen and Keyboard

3. A

4. Press the new key combination (I changed mine to Ctrl-B)

![minicom_configuration]


![minicom_screen_keyboard]


![minicom_change_command]

Now that you have changed the the command key combination for your first Minicom session, you can start another
session. Obviously this would work only if the second Minicom session is setup to use a command key other than Ctrl-B.
Since the default is Ctrl-A, you don't have to do anything to the second session. You can probably run even more
Minicom sessions by just changing the command key but I have never tried it. Besides, if you need more sessions, you
probably ought to find a better way of accessing the target system.

![two_minicom_sessions]

[minicom_main_menu]: /assets/images/screenshot_027.png "Minicom Main Menu"
[minicom_configuration]: /assets/images/screenshot_025.png "Minicom configuration menu"
[minicom_screen_keyboard]: /assets/images/screenshot_024.png "Minicom's screen and keyboard configuration menu"
[minicom_change_command]: /assets/images/screenshot_026.png "Minicom's menu for changing command key"
[two_minicom_sessions]: /assets/images/screenshot_029.png "Two minicom sessions"

