---
title: Inserting SVN revision number in your C/C++ code
layout: post
kind: article
tags:
- c
- eclipse
- svn
excerpt: Here is a quick tip on how to insert the svn revision number in your c/c++ code.
---

Here is a quick tip on how to insert the svn revision number in your c/c++ code. This is a simple Makefile trick that will define a preprocessor constant that holds the revision number of your project.  It only works if you are using gcc as your compiler, and GNU version of make.  You will also need a small utility called svnversion (On ubuntu, it should come with the subversion package).

gcc has a command line option "-D" which will define a preprocessor constant.

So doing:

    gcc -DHELLO_THERE ...

is similar to defining the constant in your code as such:

    #define HELLO_THERE

So we are going to use that trick.
On your make file:

``` makefile
SVNDEV := -D'SVN_REV="$(shell svnversion -n .)"'
CFLAGS := $(SVNDEV) ...
```

That was taken from [here](http://subversion.tigris.org/faq.html#version-value-in-source)

**Note:** in the definition of SVNDEV, notice the dot after svnversion -n.  The dot means in the current directory in which your building your code, so if you are issuing the make command outside of the svn'd directory, it won't work. You will get "export" instead of a revision number.

## In Eclipse:

In Eclipse, the building directory is different from the src directory and so if you don't have that directory in svn, you will get "export" instead of a version number.  So in eclipse the easiest way I found to do this is to replace the dot with the project_loc variable. 
You would do this in `` Project->Properties->C/C++ Build->Setting->Preprocessor  ( or  Symbols  in case of C) ``:

    'SVN_REV="$(shell svnversion -n ${project_loc})"'

After you do all that, you can use ``SVN_REV`` as any preprocessor define. So you can print out the version number like this:

``` cpp
cout << "Version: SVN " << SVN_REV << endl;
//or
printf ("Version: SVN %s\n", SVN_REV);
```

**NB**: ``SVN_REV`` is not just a number in c/c++, its a text (const char *).

### More info:

* [http://subversion.tigris.org/faq.html#version-value-in-source](http://subversion.tigris.org/faq.html#version-value-in-source) (has another way of doing this)
* [http://svnbook.red-bean.com/en/1.1/re57.html](http://http://svnbook.red-bean.com/en/1.1/re57.html)

**EDIT:**
I found out that putting the ``SVN_REV`` define in eclipse's project build settings slowed down my building process extremely.  The reason was that make was running svnversion to evaluate ``SVN_REV`` for every file it was compiling.  Instead, I found a much better way.  If you look at the makefiles that Eclipse CDT generates, it includes a makefile.defs file that's non-existent.

``` 
...
ifneq ($(strip $(C_UPPER_DEPS)),)
-include $(C_UPPER_DEPS)
endif

-include ../makefile.defs

# Add inputs and outputs from these tool invocations to the build variables

# All Target
all: pre-build main-build
...
```

This I believe is left for users to define their own symbols, best place for ``SVN_REV``.   This way, ``SVN_REV`` is evaluated once.  So you would do this in the makefile.defs

``` makefile
SVNREV:='"$(shell svnversion -n ${project_loc})"'
```

and set

``` makefile
SVN_REV=$(SVNREV)
```

in the projects build settings.

**Notice** how ``SVNREV:=`` has the colon before the equal sign.  This tells make to evaluate ``SVNREV`` only once.  If we used just the equal sign, ``SVNREV`` will evaluated for every file defeating the purpose of moving it to makefile.defs.

Moreover, I found a better definition for ``SVN_REV`` reading through a project called coreboot (open source bios).  This definition finds the file with the latest commit version number

``` makefile
SVNREV:='"$(shell LC_ALL=C svnversion -cn ../ | sed -e "s/.*://" -e "s/\([0-9]*\).*/\1/" | grep "[0-9]" )"'
```
