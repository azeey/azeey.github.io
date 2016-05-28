---
title: 'Bash Tip: Create Several copies of a file'
layout: post
kind: article
tags: [bash, linux]
excerpt: Here is a small command that can be used to copy a file to several other files.  That is, if you have a file called test and you want 5 copies of it names test1,test2,...test5, you would use this command...
---

Here is a small command that can be used to copy a file to several other files.  That is, if you have a file called test and you want 5 copies of it names test1,test2,...test5, you would use this command:

```bash
for i in {1..5}; do cp test{,$i};done
```

I have submitted it to [commandlinefu](http://www.commandlinefu.com/commands/view/2111/create-several-copies-of-a-file") which, is proving to be a pretty useful website.
