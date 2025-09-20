# TurboC Change Log  

https://sandroid.org/TurboC/changes.html

**Porting Borland Turbo C source code to GNU gcc**

| Date | Change |
| --- | --- |
| 08/28/10 | Dick Borrett has pointed out that the library no longer compiles in Ubuntu 9.x and later, due (apparently) to changes in the native gettext, getmaxx, and getmaxy functions and/or macros.  This is now hopefully fixed in the latest development snapshot.  I'm able to build in 64-bit Ubuntu 10.04, and to run the measily excuse for test programs I've provided, but I admit that I've made no extensive effort to test. |
| 12/26/05 | Fixed some instructions on the home page, wherein "TurboC.a" was referenced rather than "libTurboC.a".  (Thanks to Swapnil Kamble for pointing this problem out.) |
| 02/18/04 | Fixed PR #40, which doesn't affect anybody but new users. |
| 02/14/04 | Fixed PR #39, which doesn't affect anybody but new users, I think. |
| 11/03/02 | Fixed PR #38, which involved a gettext conflict in some newer Linux versions. |
| 12/16/02 | Fixed PR #37, which keeps TurboC from compiling with **gcc** 3.x and/or newer library versions. |
| 06/09/02 | Added outtext. |
| 06/08/02 | outtextxy, textwidth, textheight, settextstyle , and settextjustify have now been implemented for DEFAULT\_FONT (the bitmapped font).   Stroke fonts still need to be implemented. |
| 06/02/02 | 20020602 snapshot released.   The graphics.h functionality is now essentially complete, except for the absence of text-display functions (which can be worked around with **Xib**).   The text-display functions are next on the to-do list. |
| 05/26/02 | Added IBM-PC/Unicode translation table, in preparation for implementing outtext. |
| 05/25/02 | Added the functions getcolor, setallpalette, sector, graphdefaults. |
| 05/24/02 | Greatly improved the rationale for Pixmap allocation with getimage , so that the freeimage function is no longer needed (and has been dropped).  Added the functions grapherrormsg, getpalettesize , getmodename, getmaxmode, getmaxcolor. |
| 05/23/02 | Added the getimage, putimage, and imagesize functions.  These functions need to be used wtih great care, and unfortunately require image buffers to be explicitly freed with the newly-invented freeimage function. |
| 05/22/02 | detectgraph now actually works more-or-less as documented by Borland, and is used by initgraph when appropriate.  restorecrtmode , setgraphmode, fillpoly have been added.  A fix was made in closegraph. |
| 05/21/02 pm | The user now cannot manually close the graphics window (hopefully!).  Added the functions bar3d, drawpoly, getdrivername . |
| 05/21/02 am | The functions listed in the item below are still "preliminary" but no longer "buggy".  Also, pieslice and fillellipse now correctly use the fill pattern/style, rather than just filling with the solid foreground color. |
| 05/20/02 | Added preliminary but buggy versions of setfillstyle, setfillpattern, getfillstyle, getfillsettings, and bar. |
| 05/19/02 | Lots of stuff contributed by Igor Bujna was added.  Thanks Igor!  These include:  sopen, struct date , dostounix,unixtodos, getdate, an alternate version of gettime (gettime\_d), and biosprint.  As a side effect, there is now a bios.h header file.  delay was added, and clock/CLK\_TCK were adjusted to TurboC values rather than UNIX values. |
| 05/14/02 | Added getpalette, getdefaultpalette, getcolor , and getbkcolor. |
| 05/13/02 | Fixed an unexpected interaction between setviewport and setvisualpage.   Added the cleardevice and clearviewport functions. |
| 05/10/02 | Line-styles and write-modes now implemented. |
| 05/09/02 | arc, circle, ellipse, fillellipse , pieslice, getarccoords added.  getch now works from within a graphics window, for "normal" keys. |
| 05/08/02 | Added a lot of new graphics functions.Made snapshot tarball much smaller by removing pointless Source Navigator project files. |
| 05/07/02 | Crude but workable support for a few graphics functions now available. |
| 04/29/02 | Began adding support for graphics.h.  (Doesn't do anything interesting yet.) |
| 04/20/02 | Fixed some missing escape-key sequences for F1-F5,F10 when running remotely.  Fixed a timing problem with initial screen sizing that appeared only when running remotely.  Screen redraw after resizing finally reliable, I think.  Fixed the FreeBSD compile, which was broken. Also, added advice on running in FreeBSD. |
| 04/19/02 | 20020419 snapshot released.  Incorporated delays to account for timing problems in screen resizing. |
| 04/18/02 | The strcpy and strncpy now compile correctly if implemented as macros.  Big deal:  The very annoying screen-sizing problem is apparently now fixed under **xterm**. |
| 03/31/02 | A callback function, ConioResizeCallback is now activated upon console resizing.  Apps no longer segfault on being resized too small (I _think_).  **ncurses** is now terminated properly if the ported app is sent a KILL or TERM signal. |
| 03/24/02 | Fixed PR#22 (strings like strcmp implemented as macros fail to compile if optimization is on).  Display speed substantially increased by delaying wrefresh during cputs.  **xterm** screen resizing is now bypassed if there's no TERM=xterm environment variable.  Fixed PR#21 (tabbing broken). |
| 03/23/02 | Now builds under Mac OS X (but doesn't fully work) if ncurses is installed.  20020323 snapshot released.  Fixed PR#20 (improper update of non-fullscreen window after movetext & puttext).  The getftime function is now working.  Added the \_setcursortype function.   Added the directvideo, \_directvideo, and \_wscroll variables. |
| 03/21/02 | Added the getpass function.  Fixed cgets bugs PR#17-19.  In fact, cgets had been shockingly screwed up.   The functions insline and delline have been added. |
| 03/20/02 | The functions findfirst and findnext have been added. |
| 03/19/02 | Now provides a constant \_\_libTurboC\_\_ for Turbo C backward compatibility.  Now builds both libTurboC.a (translated integer types) and libTurboCu.a (untranslated). |
| 03/18/02 | Fixed TurboC header files so that they work with C++ programs.   Fixed Makefile so that TurboC headers don't have to be system headers to build the library. |
| 03/17/02 | 20020317 snapshot released.   Added the gettext, puttext, and movetext functions.   Fixed a serious bug (PR#14) in window management.   Linedraw and other weird displayable characters added to putch _et al_. |
| 03/16/02 | Greatly enhanced special-key support, including all Alt-keys and improved F_n_ keys.   Fixed PR#11 (non-working unshifted keys on numeric keypad).   Partial fix PR#9:  Physical console is now automatically resized with **xterm**. |
| 03/12/02 | 20020312 snapshot released. |
| 03/02/02 | Many changes, particularly involving integer datatypes. |
| 02/03/02 | Web page created. |

_©2002,2004 Ronald S. Burkey.  Contact [me](mailto:info@sandroid.org)._
