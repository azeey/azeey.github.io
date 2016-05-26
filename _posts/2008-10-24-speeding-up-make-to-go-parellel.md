---
title: Speeding up make to go Parallel
layout: post
kind: article
tags:
- make
- parallel
excerpt: There is a cool write up on how you can speed you build process by using multiple processes (parellel processing).
---

There is a cool write up on how you can speed you build process by using multiple processes (parellel processing).  The
command is pretty simple. Once you have configured your source, you just run: ``make -j n`` where n is the
number of jobs. If you leave n empty, it will use infinite number of jobs to compile your program.  On my dual core pc
on linux my compile speed almost doubled when I used this with infinite number of jobs.  Unfortunately, this may not
work for all programs. Also, if the software being compiled is large, the system tends to freeze until its done
compiling. Here is the link to the article:[
http://developers.sun.com/solaris/articles/parallel_make.html](http://developers.sun.com/solaris/articles/parallel_make.html)
