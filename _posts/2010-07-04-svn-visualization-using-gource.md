---
title: SVN Visualization using gource
layout: post
kind: article
tags:
- git
- gource
- svn
- version control
- visualization
excerpt: Gource is a software version control visualization tool mainly designed to work with Git, Mercurial and Bazaar.
---

I stumbled on [Gource][gource] the other day while browsing the
interwebs. It is a software version control visualization tool mainly designed to work with Git, Mercurial
and Bazaar. Since we have been using SVN for almost 2 years now here at Ciholas Technologies, I thought it would be
cool to see our SVN activity visualized.

[![gource-logo]][gource]


After a few hours of playing around and a wonderful suggestion for the music from my wife, here is the result:

<object width="100%" height="338">
  <param name="allowfullscreen" value="true" />
  <param name="allowscriptaccess" value="always" />
  <param name="movie" value="http://vimeo.com/moogaloop.swf?clip_id=12604296&amp;server=vimeo.com&amp;show_title=1&amp;show_byline=0&amp;show_portrait=0&amp;color=00adef&amp;fullscreen=1" />
  <embed type="application/x-shockwave-flash" width="100%" height="338" src="http://vimeo.com/moogaloop.swf?clip_id=12604296&amp;server=vimeo.com&amp;show_title=1&amp;show_byline=0&amp;show_portrait=0&amp;color=00adef&amp;fullscreen=1" allowscriptaccess="always" allowfullscreen="true" />
</object>

[ciholas-svn-vis-1080p](http://vimeo.com/12604296) from [azeey](http://vimeo.com/azeey) on [Vimeo](http://vimeo.com).

Using Gource
-----------

### Setup

Getting gource installed was a breeze since it's already included in the Ubuntu repositories, although later on I had to
build it from source to get the latest available version. The next step was to generate the SVN log and convert it to a
format gource would understand. The easiest and quickest way that worked for me was issuing ``svn log`` on the svn
repository and piping it to a file followed by running the conversion script on this file.

I found two scripts in the gource wiki to do the conversion. The one that worked the best for me, albeit slower, was the
ruby script [svn2gource].

{:.bash}
    $ svn log --verbose svn://repo | tee changelog-plain.txt
    $ ./svn2gource changelog.txt | tee changelog-gource.txt

### Interactive Gource

At this point, you are ready to run gource. You can run gource interactively. In this mode you can manually change the
camera mode, speed, and even the date. The command for that is:

{:.bash}
    $ gource --log-format custom changelog-gource.txt

Interactive keyboard commands (Copied directly out of the manpage):

>|(V) Toggle camera mode
>|(C) Displays Gource logo
>|(M) Toggle mouse visibility.
>|(N) Jump forward in time to next log entry.
>|(+-) Adjust simulation speed.
>|(&lt;&gt;) Adjust time scale.
>|(TAB) Cycle through visible users
>|(ESC) Quit

### Gource to Video

Gource has the ability to output uncompressed sequence of screenshots in PPM format using the ``--output-ppm-stream``
option, which can then be processed by another program (such as *ffmpeg*) to produce a video. There are several
options that gource provides to help you tweak your video such as font size, screen size, and logo positions. After
several experimentation, I came up with this script:

    #!/bin/bash
    gource --title "Ciholas Technologies SVN Visualization" --font-size 14 \
    --logo logo2.png --logo-offset 1620x30 -1920x1080 --highlight-all-users \
    --camera-mode track --stop-at-end -s 0.5 -i 63072000 --log-format custom \
    ciholas-svn-plain-gource-sorted.log --output-ppm-stream - | ffmpeg -y -b \
    3000K -r 60 -f image2pipe -vcodec ppm -i - -vcodec libx264 -vpre default \
    ciholas-svn-gource-sorted-1080p.mp4

#### FFmpeg with h264 Codec

In order for the script to work, you need to have the h264 codec libraries and ffmpeg compiled with the h264 codec flag.
Unfortunately, using ffmpeg with the libx264 option wouldn't work for me in Ubuntu. I believe this was because ffmpeg on
ubuntu doesn't have the h264 option enabled by default for legal reasons. You would have to compile ffmpeg from the
source yourself, and there are instructions [here](https://wiki.ubuntu.com/ffmpeg) on how to do that. Fortunately, I
found a set of scripts [here](http://code.google.com/p/x264-ffmpeg-up-to-date/) that automatically
update both x264 and ffmpeg to the latest versions available from their SVN and git repos. You might still have to
install some dependencies but the scripts should handle all the compiling.

[gource]: http://code.google.com/p/gource/
[gource-logo]: /assets/images/gource-logo.png
[svn2gource]: ftp://borg.uu3.net/pub/archive/Gource/svn2gource
