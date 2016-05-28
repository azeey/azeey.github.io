---
title: Unit testing with Python on the Vortex86DX
layout: post
kind: article
tags:
- cython
- linux
- python
- python32
- sysio
- ubuntu
- vortex86
excerpt: Using sysio to manipulate GPIO ports on the Vortex86
---

One of the tasks I have to carry out at work lately is test custom Vortex86DX based boards for build defects such as
malfunctioning input pins, relays, serial ports, etc.  I used this as an opportunity to use python's unittest framework.
I am not writing about the unit tests themselves but how I got them to work on the Vortex86DX.  We are running a fairly
recent version of Linux with a customized (stripped out) Ubuntu distro on this board and it has Python-2.6 installed on
it.  The tests involved using GPIO ports and so I had to use some kind of module that would expose the sysio c functions
such as outb,inb and iopl so Cython came to mind.  I found a nice pyx file for
[sysio](http://pypi.python.org/pypi/sysio/1.0) on pypi and I was set until I realized my development environment was 64
bit Ubuntu and that cross compiling using distutils as far as I know, is impossible without installing 32 bit python.
So I set out to do that but compiling 32 bit python wasn't trivial either.  Thanks to some google searching, I was
finally successful.

Compiling 32 bit python on 64 bit Linux host:
---------------------------------------------

Once I downloaded the python source from python.org here is the command I run to successfully build python.

    $ CC="gcc -m32" LDFLAGS="-L/lib32 -L/usr/lib32 \
    -L`pwd`/lib32 -Wl,-rpath,/lib32 -Wl,-rpath,/usr/lib32" \
    ./configure --prefix=/usr/local/python32 --enable-unicode=ucs4
    $ make
    $ make install

This would only work if you have gcc-multilibs and all the necessary 32 bit libraries installed.  It was necessary to
have the ``--enable-unicode`` flag because the python on the Vortex86DX was compiled with this flag.

Using Scons to build Cython modules:
---------------------------------------

At this point, I could have used my newly installed python32 and use distutils to build the module but it didn't occur
to me until later on so I used scons.  Scons understands how to build shared libraries with all the correct gcc flags so
it was easier to use it than to write a makefile.  Annoyingly scons adds the "lib" prefix to the libraries it builds so
I had to change sysio.pyx to libsysio.pyx or else python wouldn't import the resulting .so file.

``` python
libname='libsysio'
src=libname+'.c'
pyx=libname+'.pyx'
bld = Builder(action ='cython $SOURCE')
env = Environment(BUILDERS = {'cython': bld})
cython =env.cython(pyx)
SharedLibrary(libname, src, CCFLAGS='-m32',LINKFLAGS='-m32',CPPPATH='/usr/local/python32/include/python2.6')
Requires(src, cython)
Clean(cython,src)
```

I am really new to cython and scons so I am not sure if this is the best way to build cython modules but the user guide
at the cython website had a "to be completed..." entry for scons.

Using the sysio module:
----------------------

Now that I had a 32 bit module, I was able to start up the python shell and toggle some pins and proceed with my unit
tests.

``` python
from libsysio import *
# Refer to the vortex86dx datasheet
port1dir = Device(0x99)
port1data = Device(0x79)
# Set all ports to output
port1dir.outb(0xff)
# Drive all pins high
port1data.outb(0xff)
```
