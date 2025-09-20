# TurboC library

**Porting Borland Turbo C source code to GNU gcc**

https://sandroid.org/TurboC/

[Usage](USAGE.md)
[Change log](CHANGES.md)
[Bug list](BUGS.md)

# Contents

> [TurboC in a nutshell](#Introduction)  
> [Why was TurboC created?](#Gutenberg)  
> [Alternate Approaches](#Other)  
> [Features and Deficiencies of TurboC](#ProsAndCons)  
> [Download and Install TurboC](#Download)  
> [Mac OS X](#MacOSX)  
> [Future plans](#FuturePlans)  
> [What's with the cute logo?](#Logo)

- - -

# TurboC in a nutshell

TurboC is a linkable library and a set of C header files that make it easier to port C code originally written for Borland's MS-DOS based Turbo C compiler to GNU **gcc** -- and therefore to a wide variety of \*nix computer platforms.   Generally speaking, it is the following:

*   An implementation via **ncurses** of conio.h from Turbo C.
*   An implementation via **Xlib** of graphics.h from Turbo C.
*   A few other functions and time-saving features thrown in for convenience.
*   A means of overcoming the integer datatype discrepancies between Turbo C and GNU **gcc**.

It is not, by any means, a complete solution, but you may find it helpful.  The support for graphics.h is in its relatively early stages.

TurboC is available as [free software under the GNU Lesser General Public License (LGPL)](http://www.fsf.org).

- - -

# Why was TurboC created?

I have written quite a lot of MS-DOS based software using Borland's Turbo C 2.0 compiler.  When the inclination finally hit me to make some of this otherwise-deceased code available as free software, my first innocent idea was to release it under the GPL, simply labeling it honestly as MS-DOS code, and then later to port the code to \*nix as time and interest permitted.  It was obvious that directly porting to \*nix up-front would be a time-consuming task.

But I ran into a snag.  It's no good to release software if there's no way for anybody to find out about it.  My preferred channel for announcing open-source software is [freshmeat](http://www.freshmeat.net), but freshmeat doesn't "do" MS-DOS (and therefore politely declined to accept my MS-DOS projects).

Apparently, necessity really is the mother of invention.  It occurred to me that if I could provide appropriate Turbo C compatible libraries and header files, then the effort of porting Turbo C code to \*nix would be greatly simplified.  In other words, it would be _relatively_ easy to provide \*nix versions of the old MS-DOS code.  Whether it's _absolutely_ easy or not depends a lot on the program you're trying to port.

- - -

# Alternate Approaches

For conio.h functionality, you might consider Pablo Vidal's [UConio library](http://crazylovetrain.hypermart.net/) instead.  I've not used it, and don't know its advantages and disadvantages.  It certainly supports a much smaller subset of conio functionality than TurboC, and requires you to modify your source code much more extensively.

[Borland](http://www.borland.com) supposedly has (or will soon have) a Linux version of their C++ compiler.  At this point, the nature, price, and target platforms of this compiler are mysteries.  One _presumes_ that (like [Kylix](http://www.borland.com)) it will be [Wine](http://www.winehq.org)\-based, and therefore will work only on 'x86 systems.  Nevertheless, if it contains the various library functions implemented in the TurboC library -- and frankly, that's a _very big_ "if" -- it might provide a simpler means of porting your Turbo C programs to Linux.  Speaking for myself, even though I have been a long-time Borland user, I philosophically prefer to compile my program with GNU **gcc**.

For graphics.h functionality, there is the [GRX library](http://www.gnu.de/software/GRX/) of Csaba Biegl, Michael Goffioul, and Hartmut Schirmer.  I didn't discover this library until I had only a few graphics.h functions left to write, and I haven't fully evaluated it.  Its stated aim is to be a multi-platform 2D graphics library.  Its website does not advertise (or even mention) Turbo C compatibility.  However, it contains quite a few functions of the same name (and, apparently, the same functionality) as Turbo C library functions.

Of course, if you are only interested _running_ your program on \*nix systems -- as opposed to _porting_ the program to \*nix systems in general -- you might be able to simply run your existing executable using one of the freely available DOS emulators for Linux.  DOS emulation using **dosemu** with **freedos** works very well on 'x86 systems, while **bochs** with **freedos** works well (if somewhat slowly) on non-'x86 \*nix systems.

- - -

# Features and Deficiencies of TurboC

## Features

*   TurboC provides most of the console-i/o functionality of Turbo C's conio.h header, mapping it to related ncurses functionality.  (Currently, every conio function except cscanf is supported.)
*   TurboC provides most of the "BGI" functionality of Turbo C's graphics.h header.  At present (20020608), implementation of these features is mostly complete except for the temporary lack of stroked-font support.
*   TurboC provides a sprinkling of other missing functionality like the strupr and strlwr functions.
*   Check out the [complete list](USAGE.md) of available functions.

## Deficiencies

*   Refer to the [complete list](BUGS.md) of bugs and issues.  So far, the library is only as complete as it needs to be for the kind of programs I typically used to write in Turbo C.  As I port more of my old programs to \*nix, the TurboC libraray will presumably become more complete.  Presently, support for conio.h is essentially complete, and support for graphics.h is essentially complete except for displaying text.
*   In the ported program, the text-console window is logically sized properly, but is not physically sized properly unless **xterm** is used.  If other terminals are used (such as KDE **Konsole**), the window must be resized manually by the user.
*   Borland's Turbo C is a 16-bit compiler, whereas the GNU **gcc** compiler I'm targeting is 32-bit.  This results in a discrepancy in the integer datatypes.  TurboC handles the (hopefully) most common cases by means of macros; unhandled cases will cause compile-time errors and must be fixed manually.  This is covered in detail on the [usage](USAGE.md) page.
*   Mixing and matching stream-based functionality (like getchar ,printf, and so on) with "console" based functionality (like getch, cprintf, and so on) probably won't work.  Again, look at the [usage](USAGE.md) page.

- - -

# Download and Install TurboC

Within this repository, you can build the library simply by running **make**.  This must be GNU **make**, and may be called **gmake** (for example, on FreeBSD or Mac OS X).   (**Note**:  In FreeBSD, you may need to edit the Makefile and comment/uncomment a couple of lines at the top, in a way that will be obvious to you.)   I've verified that the most recent release snapshot (as opposed to the development snapshot) compiles and that a test program works on the following systems:

> Linux Intel 'x86 (SuSE 8.0)  
> Linux PowerPC (SuSE 7.3)  
> FreeBSD (4.5)

(Early versions of the TurboC library which indicated that they worked with FreeBSD were probably over-optimistic in that assessment.  Please upgrade to a version of at least 20020420, and be sure to follow the advice on [running](USAGE.md#Running) the ported programs.)  The most current development snapshot has been tried only on SuSE 8.2.

Put the libraries (libTurboC.a and libTurboCu.a) wherever libraries normally go on your system -- usually /usr/lib, I suppose.  Put all of the header files wherever they normally would go -- /usr/include, I suppose.  Or, you can just symbolically link them in these directories.  Or, put them wherever you like and add appropriate -L and -I switches to your **gcc** command line.

Here's the way I would do it on my own system:

> cd ~  
> tar -xzvf TurboC-dev.tar.gz  
> cd TurboC-source  
> ... edit Makefile in FreeBSD ...  
> make (or gmake)  
> su  
> ln -s \*.h /usr/include  
> ln -s libTurboC.a /usr/lib  
> exit

You may find (for example, in FreeBSD) that if you #include TurboC header files in the programs you're trying to port, you get compiler errors about symbolic links being "too deep".  In this case, remove the "-s" switch from the **ln** commands above.

Also, the Makefile makes certain assumptions about the locations of the X-window system header files and libraries; if these assumptions are wrong, you may need to alter the compiler switches "-L" and/or "-I" within the makefile.  The X-window system is needed to compile the complete library, or to run a ported program, if you use any functions from graphics.h.  If you don't intend to use these functions, you can use ported programs or compile the library on a system without X, by editing the Makefile and removing the compiler switch "-DWITH\_X".

- - -

# Mac OS X

**\[Note**:  The following instructions were written before graphics.h support was added to the TurboC library.  This support requires the X-window system.  Therefore, you must either disable TurboC graphics support (by removing the command-line switch "-DWITH\_X" from the Makefile), or you must install the X-window system.  At this point, I've not tried either of these with Mac OS X, so you're pretty much on your own.\]

As most people in the \*nix world know, Mac OS X is based on FreeBSD.  Knowing this, you might suppose that if the TurboC library works on FreeBSD then it will work on Mac OS X.  Alas! this optimistic appraisal is revealed as simplistic when viewed in the of harsh light of reality.

The TurboC library has indeed reached the stage where some versions (though not necessarily the current snapshot) compile and _partially_ work under Mac OS X, if **ncurses** happens to be installed.  Apple does not include **ncurses** in Mac OS X -- at least not as of the version 10.1 release.  But ncurses available as a [download](http://macosx.forked.net/ports/ncurses-5.2.pkg.tgz) from [macosx.forked.net](http://macosx.forked.net).

If you download and install **ncurses**, you should then be able to build the TurboC library as follows:

> gunzip TurboC-dev.tar.gz  
> tar -xvf TurboC-dev.tar  
> cd TurboC-source  
> make

What you do _after_ this, is a mystery to me, though.  I don't know how get the TurboC libraries nor the header files _installed_, because I can't figure out how to get the root-level permissions required to do so!   Also, as I've said, the library only partially works.  Perhaps it's a problem in the TurboC library ... perhaps it's a problem in the ncurses port.  If anyone wants to explain this stuff to me, I'd be happy to know about it.  Otherwise, for me, working with Mac OS X is too much like pulling teeth, so it's going to be quite a while before I work out the problems on my own initiative.

- - -

# Future plans

Well, it's pretty obvious that this kind of project has almost nowhere to go, since its whole purpose is to port code from a long-obsolete compiler. However, I'll probably add new functions as the need for them arises in my own projects.  I might not be motivated to waste my energies writing functions that don't appear in _my_ Turbo C programs, but if you've created any such missing functions for yourself, I'd love to add them to the library (crediting you appropriately) if you only [tell me](mailto:info@sandroid.org) about them.

Of course, feel free to tell me about _bugs_ in TurboC.  (Before doing so, of course, check out the list of [already-known bugs](BUGS.md).)  I'll certainly make every effort to fix them.

- - -

_©2003,2004,2010 Ronald S. Burkey.  Last updated 08/28/2010 by RSB.  Contact [me](mailto:info@sandroid.org)._
