# TurboC Functions, Variables, Constants, and Headers  

https://sandroid.org/TurboC/functionlist.html

**TurboC Usage**

**Porting Borland Turbo C source code to GNU gcc**

# Contents

> [Introduction](#Intro)  
> [Using TurboC](#Using)  
> [Licensing](#License)  
> [Running a ported program](#Running)  
> [Running a ported program remotely](#Remote)  
> [Supported Turbo C entities](#Entitites)  
> [Handling of integer datatypes](#Integers)  
> [Special-function keys](#Keys)  
> [Text screen resizing](#Resizing)  
> [Graphical display characters in text mode](#Characters)  
> [Graphics mode](#Graphics)  
> [Alternative graphics-mode functions](#Alternative)  
> [Endian conversions](#Endian)  
> [Examples](#Example)  
> [Trouble-shooting](#Trouble)

- - -

# Introduction

> No explanation of the exact functionality of the various TurboC library elements is offered here, except for noting differences between Borland's version and my version.  As Pablo Vidal points out on his **UConio** web page, if you don't know how to use these functions already, then you won't need the TurboC library, since you shouldn't be using it for new program development anyway.  Consult Borland's _Turbo C Reference Guide_ as needed.
> 
> But if you are porting or supporting a Turbo C program that somebody else has created, and don't have access to Borland's printed materials yourself, I'd offer the following advice:  Download the _free_ Turbo C development environment that Borland has kindly provided on their ["museum" website](http://community.borland.com/museum/).    (Requires you to register, but registering is free.)  The IDE has context-sensitive help, and you can find out what most functions do by typing the name of the thing (such as clrscr)  into the source-code editor and then hitting ctrl-F1.  Of course, this requires you to have some means of running an MS-DOS program.
> 
> Alternately, the online help for Borland's C++Builder program actually provides much fuller documentation of Turbo C library functions (even though it doesn't support a lot of them), and while C++Builder itself is not free, Borland allows you to download the Win32-based helpfile for free from their [website](ftp://ftp.borland.com/pub/bcppbuilder/techpubs/bcb5/b5rtl.zip) .   After you download it, the particular helpfile you want is the one which Borland calls the _DOS Reference_.  Sadly, there's no _guarantee_ that this documentation corresponds exactly to the functionality provided in Turbo C.

# 

- - -

# Using TurboC

Compile the Turbo C source you're trying to port, using GNU **gcc**. Add command-line switches to link to TurboC, to **ncurses**, to **Xlib** , and to **pthreads**:

> gcc \[-O0\] _... your switches ..._ -lTurboC -lm -lncurses -L/usr/X11R6/lib -lX11 -lpthread
> 
> > or
> 
> gcc -DDoNotFixIntegers ... _your switches_ ... -lTurboCu -lm -lncurses -L/usr/X11R6/lib -lX11 -lpthread

In FreeBSD, you'll have to say "-lc\_r" rather than "-lpthread".  Note that the actual directories containing header files and libraries of the X-window system may vary, so you may need to alter the "-L" switch or add a "-I" switch to access the appropriate X files.

The command-line switch "-DDoNotFixIntegers" is optionally used to turn off TurboC's [integer datatype conversion macros](#Integers) and (as you may notice) uses a slightly different version of the library.  In the past, some GNU "functions" actually implemented as macros have failed to compile with optimization level higher than -O0, when integer datatype conversion is turned on.  That's why one of the examples includes -O0 and the other does not.  However, this precaution is probably no longer necessary, and you needn't use -O0 unless experiencing problems.

You can see samples of both types of compilation in the provided Makefile.  If there are complaints from the compiler, refer to the [trouble-shooting](#Trouble) section below.

The switch "-lm" is required by only a couple of functions, but there's no harm in using it for everything.  The switch "-lncurses" is needed only if you are using the Turbo C functions prototyped in conio.h, while the switches "-L/usr/X11R6/lib -lX11 -lpthread" are needed only if using the functions prototyped in graphics.h.  You'll want to remove these switches if they're not used, to enhance your ability to compile the ported program on more platforms.  For example, neither **ncurses** nor **Xlib** are typically present in a Mac OS X system (though they can be installed with some effort), and compilation of your ported program will therefore typically fail if these compiler switches are included.

Although I'm not entirely sure why you'd want to do it, and though I haven't tried it myself, I think you can probably disable the graphics.h functions in the TurboC library, and replace them with those in the [GRX 2D](http://www.gnu.de/software/GRX/grx244um.htm#bgi) graphics library.  In the TurboC library Makefile, remove the compiler switch "-DWITH\_X" before compiling the TurboC library.  Then install **GRX** and follow the instructions in **GRX**'s readme.bgi file.  I'd be happy to hear from anyone with [feedback](mailto:info@sandroid.org) (pro/con/howto) on this topic, and will post those comments if they seem to be of general interest.

- - -

# Licensing

Since TurboC is provided under the [LGPL](http://www.fsf.org) , you are free to license your own program however you like.  In other words, your program _does not_ have to be GPL'd.  Of course, it would be nice to do so, and you might want to consider GPL'ing your code.  You'll still want to read the LGPL to make sure that you conform to its (rather minimal) requirements.

If your code is linked to **ncurses** or **X**, as described in the previous section, you'll also have to conform to _their_ licenses, namely the MIT license and the X-Consortium license respectively.  These licenses are highly non-restrictive, and require merely that you include their copyright notices.  Both licenses can be viewed at the Free Software Foundation's [website](http://www.fsf.org).

- - -

# Running a ported program

The ease with which you can run a program ported to \*nix with the TurboC library depends on _which_ Turbo C capabilities have been used.  The important points to consider are whether the "console i/o" capabilities (as represented by the header file conio.h) and/or the "graphics" capabilities (as represented by the header file graphics.h) have been used.

While text-based programs ported with the TurboC library run to some extent in almost any \*nix text terminal, it is highly advisable to run the ported program under the X-window system, explicitly using **xterm**.  I'd recommend invoking your program as follows, either from a command line or as a desktop icon:

> xterm +sb -tn linux -e _PortedProgram_ _PortedProgramOptions_ ...

(See "man xterm".)  The "+sb" command-line switch removes scrollbars from the **xterm** console.  The "-tn linux" command-line switch insures that the **xterm** color settings, function-key interpretation, etc., match what's used in Linux (where I've been developing the TurboC library).  This is useful, for example, if the ported program is being run in FreeBSD, where **xterm**'s default settings aren't necessarily the same as in Linux.

The deficiencies which you may encounter if **xterm** is _not_ used are [numerous](bugs.html), but other kinds of text consoles (even a real text console without the X-window system) may prove to be satisfactory for your purposes.  You'll simply have to try it and see, if you're so inclined.

Graphical capabilities, on the other hand, are supported only under the X-window system, and require availability of an X server.  In general, this means that the ported program can be run on any \*nix box except bare-bone ones (like firewalls) or quirky ones (like Mac OS X) that don't provide **X**.

- - -

# Running a ported program remotely

Because text-handling capabilities of the TurboC libary are based on standard i/o streams (stdin/stdout) and on **ncurses**, the text-based programs work just fine when run remotely but hosted on another computer:

> xterm +sb -tn linux  
> telnet/rlogin/ssh/etc. _(to connect to host)_  
> _PortedProgram PortedProgramOptions ..._

Actually, there is some difficulty correctly sizing the screen when **ssh** is used for the remote login, though **telnet** and **rlogin** seem to work properly in the cases I've tried.  What happens with **ssh** is that if the **xterm** window is manually resized, it doesn't automatically resize itself until a key is pressed.  I imagine that the desire to remotely run a ported Turbo C program is rare, so this is unlikely to pose a problem for many people.

To a certain extent, graphically-based programs can also be run remotely, because the TurboC library's graphical capabilites are based on the X-window system.  However, exploiting this capability is hardly a user-friendly process.  The following steps are necessary:

> 1.  You must set up the X-server at the user's location to accept input from the remote computer hosting the ported TurboC program.  This is done with the command-line program **xhost**, and you need to know the ip address of the remote computer:
> 
> xhost _RemoteIpAddress_
> 
> 3.  The DISPLAY environment variable must be properly initialized on the remote computer to indicate the user's computer.  Typically this is done automatically when you log in (with **telnet**/**rlogin**/**ssh** ), but not _necessarily_.  So after logging in, but before running the ported program, you might need to set up DISPLAY.  In the bash shell, this would look something like this:
> 
> export DISPLAY=_UserIpAddress_:0.0

Putting it all together, the complete sequence of steps for running the program remotely might become something like this:

> xterm +sb -tn linux  
> xhost _RemoteIpAddress_  
> telnet _RemoteIpAddress_  
> export DISPLAY=_UserIpAddress_:0.0  
> _PortedProgram_ _PortedProgramOptions_ ...

Unfortunately, firewalls and/or network-address-translation may wreak havoc with this scheme, and you may find that the graphics capabilities of the ported Turbo C programs simply don't work remotely.  Text-only ported programs are fortunately less affected (i.e., _not_ affected)  by these problems.

- - -

# Supported Turbo C entities

Of course, there are a lot of functions in Turbo C that are not only supported in GNU **gcc**, but that are even supported identically, such as fopen , fclose, fread, printf, time, etc.  On the other hand, a generic function like putchar may operate similarly only over a certain range.  For example, putchar(0xA5) is unlikely to display characters that appear to be identical in Borland Turbo C on an IBM PC and in GNU **gcc** in Linux.  I'm sorry about that, in a theoretical way, but life is too short for me to expend a lot of energy fixing it.

Anyway, the table below isn't a complete list of all the Turbo C entities you can use, but simply those that the TurboC library treats specially. Also, I'm sure there are Turbo C functions that need special treatment which I've simply not encountered yet in my porting activities, and so aren't included here.  
 

| Turbo C Entity | Entity Type | Supported | Notes |
| --- | --- | --- | --- |
| \_\_libTurboC\_\_ | constant | n/a | Provided by the TurboC library, but not normally present in actual Turbo C.  Can be used with conditionaly compilation to insure that any changes you make to your code in porting to libTurboC are discarded if the program is recompiled later in actual Turbo C.  :-) |
| alloc.h | header file | Yes | This is supported in the sense that a file of this name is provided.  However, not all of the functions are necessarily supported. |
| arc | function | Yes |     |
| bar | function | Yes |     |
| bar3d | function | Yes |     |
| bios.h | header file | Yes | This is supported in the sense that a file of this name is provided.  However, not all of the functions are necessarily supported. |
| biosprint | function | Yes | Thanks to Igor Bujna!  At the moment, this one relies on Linux specifically (not FreeBSD or other Unices). |
| BypassResizeXterm | variable | n/a | This is not present in real Turbo C.  Normally, the TurboC library will attempt to physically resize the text console when you call textmode .  However, this does not work unless you are running **xterm**.  If you set BypassResizeXterm to a non-zero value before calling textmode\-- say, on the basis of some environment variable setting -- then the attempt to resize the window is bypassed.  However, the window must still be resized by some other mechanism or else the program may segfault. |
| cgets | function | Yes |     |
| circle | function | Yes |     |
| cleardevice | function | Yes |     |
| clearviewport | function | Yes |     |
| clock | function | Yes | This is already a standard \*nix function, but the existing \*nix function is not suitable for Turbo C.  Instead the existing function is rename clockUnix and a new clock (clockTurbo) function is created. The CLK\_TCK constant may also pre-exist in \*nix, but it may (or may not) be assigned a different value than needed for clock .  If TurboC.h or any other header files from the TurboC library are included, CLK\_TCK will automatically be reassigned (within that source file) to an appropriate value.  However, it may not be the same value for CLK\_TCK used in true Turbo C (variously, 18 or 1000).  For example, in my experiments it is equal to 100, but there's no guarantee that it won't be 1000000 on some system.  This means that the output of clock must always be assigned to a variable of type clock\_t , because it may overflow an int or unsigned.  clock and CLK\_TCK are supposed to be prototyped in time.h -- and _are_ -- but won't have the behavior expected in TurboC unless a TurboC library header file is included also.  In other words, if time.h is included but no TurboC header file is included, clock will behave incorrectly but there will be no errors or warnings.  Finally, clock is supposed to measure the time since program startup, but in TurboC it actually measures the time since clock itself was first called by the program. |
| closegraph | function | Yes |     |
| clreol | function | Yes |     |
| clrscr | function | Yes |     |
| COLS | variable | n/a | This is not present in true Turbo C.  It is an **ncurses** variable that tells the current number of text columns in the display console. |
| conio.h | header file | Yes | Most conio.h functions are supported.  Currently, only cscanf is omitted.  Support is through ncurses. |
| ConioResizeCallback | function | n/a | This is not present in true Turbo C.  It is a function which the TurboC library calls when it has detected that the user has resized the console.  By default, it does nothing, but you can override this behavior by providing your own version of this function (which will be preferentially chosen at linktime over the version already within the TurboC library).  You can do anything you like with this function, but one possible use is to refresh all of the text data on the screen.  Use the LINES and COLS variables to determine the current screen height and width. |
| cprintf | function | Yes |     |
| cputs | function | Yes |     |
| cscanf | function | No  | It may seem naively that this function is a simple combination of cgets with sscanf.  However, on closer inspection it really isn't_._  I don't presently see how to implement it without messing with the guts of the scanf function family (which I have no desire to do). <br><br>Workaround:  For the present time, I'd suggest writing cscanf out of your code in favor of cgets and sscanf .  Actually, I've seen Borland docs that recommend this also. |
| struct date | data type | Yes | Thanks to Igor Bujna. |
| delay | function | Yes | (Igor Bujna provided this function but I haven't used it, because his version relies on **ncurses**.  Thanks but sorry, Igor!) The function parameter has arbitrarily been changed from 16-bit to 32-bit. |
| delline | function | Yes |     |
| detectgraph | function | Yes |     |
| dir.h | header file | Yes | This is supported in the sense that a file of this name is provided.  However, not all of the functions are necessarily supported. |
| directvideo  <br>\_directvideo | variable | Yes | I provide variables of this name, which you can manipulated as desired, but they don't affect any conio functionality.  (It seems to me that variables of both names have been provided in different versions of Turbo C, but they behave the same.) |
| dos.h | header file | Yes | This is supported in the sense that a file of this name is provided.  However, not all of the functions are necessarily supported. |
| dostounix | function | Yes | Contributed by Igor Bujna. |
| drawpoly | function | Yes |     |
| ellipse | function | Yes |     |
| far | type modifier | Yes | Requires TurboC.h.  There's no need for "far pointers" with a 32-bit compiler, so this is simply an empty macro. |
| farcalloc | function | Yes | This is the same as calloc. |
| farcoreleft | function | Yes | This is supposed to determine how much space remains on the heap. There's no way to do this, since the heap size is not limited in any practical sense.  Hence, it is a macro that simply always has the value 512K. |
| farfree | function | Yes | This is the same as free. |
| farmalloc | function | Yes | This is the same as malloc. |
| fcloseall | function | Linux: Yes  <br>FreeBSD: No  <br>Other: TBD | Requires TurboC.h.  This is not a function for which I provide any code.  It's simply a standard library function. |
| struct ffblk | data type | Yes |     |
| fillellipse | function | Yes |     |
| fillpoly | function | Yes |     |
| findfirst | function | Yes | While substantially a functional replacement for the Turbo C function of the same name, it is also subtly different in a number of ways:<br><br>*   Most importantly, please read about the findlast function.<br>*   Long filenames (not just 8.3) are supported. <br>*   Regular expressions are used for the filename patterns.  This means that expressions like "\*.c" and "foobar.?" will work mostly -- but not _entirely_ -- as expected.  For example, the pattern "\*" in Linux corresponds more closely to the pattern "\*.\*" in DOS than does "\*.\*" in Linux.  (In Linux, "\*.\*" implies that the filename _really_ contains a dot, and so it wouldn't match a filename like "foobar".)<br>*   Since \*nix filenames are case-sensitive, patterns like "\*.c" and "\*.C" are _not_ interchangeable.<br>*   Among the flags, only FA\_DIREC corresponds directly to the DOS FA\_DIREC  flag. FA\_ARCH and FA\_LABEL flags are not supported at all.  I've arbitrarily defined FA\_RDONLY to correspond to files for which the user-write-permission bit is not set, and FA\_SYSTEM to correspond to files for which the user-read-permission bit is not set. FA\_HIDDEN is defined to correspond to files whith names beginning in '.'.  However, this does not work on all systems.  (It works in Linux, but not presently in BSD.) |
| findlast | function | n/a | This is not a function provided by true Turbo C, but is provided to correct a problem in the libTurboC emulation.  The "normal" use of findfirst/findnext, as defined by me, is that the entire file list is read, and then terminates when findnext cannot locate any more files.  If the functions are not used in this way -- in other words, if findnext never locates the _last_ file -- then libTurboC has no way to know that it should free the memory which has been allocated.  With each "abnormal" use of findfirst/findnext this memory will accumulate.  The symptom is that eventuallyfindfirst/findnext will not be able to locate files even though they exist.  This problem can be fixed by explicitly adding a call to findlast after the last use of findnext, but before deallocating its struct ffblk .   The prototype is:  <br>void findlast (struct ffblk \*); |
| findnext | function | Yes | See the notes for findfirst. |
| floodfill | function | TBD | I may prove to be too darned lazy for this.  However, there's a **GRX** function that does this.  If anyone wants to adapt it ... |
| fnkeys.h | header file | n/a | This is a file I've provided, but there is not any equivalent Turbo C header file.  This header simply provides symbolic names for all of the special-function keys recognized by MS-DOS.  Unfortunately, these are not all supported by ncurses.  See the discussion [below](#Keys). |
| getarccoords | function | Yes | Requires the "-lm" compiler switch. |
| getaspectratio | function | Yes | There is a function of this name, but at this point it acts as though the pixels are always square. |
| getbkcolor | function | Yes |     |
| getch | function | Yes | There is an ncurses function of the same name, but somewhat different functionality.  In any source file using conio.h, the conio version of getch applies below the #include "conio.h", while the ncurses version of getch applies above it.  The ncurses functionality continues to be available, however, as a new function getchNcurses .<br><br>Please note that getch will work in textmode or in graphics mode, but will not presently work otherwise.  Conversely, getchar will work only when _not_ in textmode .  (getchar will work in graphics mode if textmode has not been previously called, but will not echo to graphics screen). |
| getche | function | Yes |     |
| getcolor | function | Yes |     |
| getdate | function | Yes | Contributed by Igor Bujna.  In some newer system libraries, there actually is a function called getdate, which works differently and conflicts with this function.  That function, if it exists, will be accessible in TurboC programs as getdateSystem. |
| getdefaultpalette | function | Yes | The true Turbo C function returns a pointer to a struct palettetype , whereas the TurboC library function returns a pointer to const struct palettetype. |
| getdrivername | function | Yes | This functions returns a const char \*, whereas the true Turbo C function returns char \*. |
| getfillpattern | function | Yes |     |
| getfillsettings | function | Yes |     |
| getftime | function | Yes | I initially believed that this function did not work, because the linker could not find the fstat function on which it is based.  Later, this problem mysteriously disappeared.   I mention this just FYI. |
| getgraphmode | function | Yes |     |
| getimage | function | Yes | Borland's documentation does not specify the format of the buffer into which the image is placed, beyond fact that the first two words contain the width and height, in pixels.  If you use this function with those restrictions in mind, you'll be okay.<br><br>_On the other hand ..._ I think it's very likely that programs containing  this function might exploit knowledge of the graphics controller in the PC, and thus may (improperly if cleverly) directly manipulate date within the output graphics buffer.  Realize that _no_ effort has been made to duplicate these  undocumented buffer formats. If your program depends on the size or format of the graphical area of the output buffer, I guarantee that it _will not_ work!<br><br>As a corollary, be sure to allocate image buffers dynamically using imagesize, per Borland's docs, with malloc or calloc.  I suspect that many existing programs statically allocate the buffers, on the incorrect notion that the buffer format is known.<br><br>Finally, you should not expect to be able to save the image buffer as a file and to reload it later. |
| getlinesettings | function | Yes |     |
| getmaxcolor | function | Yes |     |
| getmaxmode | function | Yes | See the comments for getmoderange. |
| getmaxx | function | Yes |     |
| getmaxy | function | Yes |     |
| getmodename | function | Yes | Since the TurboC library does not limit the graphics modes to those of a particular "graphics driver" as Borland Turbo C does, and since there are many more graphics modes, it is not practical to make the name strings identical to those reported in an actual Turbo C program. |
| getmoderange | function | Yes | Since the TurboC library rejects the concept of a "graphics driver", _every_ graphics mode is allowed for _every_ driver.  Furthermore, while various graphics modes are available under the same symbolic constant names as in true Turbo C (such as VGAHI, VGALO, etc.), the symbolic constants don't have the same numeric values as in true Turbo C, and many more modes are available than are assigned symbolic constants.  (To understand this, refer to the graph.h file.)  The upshot of all this is that getmoderange returns a huge range of allowable modes, in comparison to true Turbo C.  At this writing, the range is from 0 to approximately 1439, whereas true Turbo C topped out at about 5; this maximum mode presently provides a 1280x1024 screen with 256 colors and 4 pages.  Whether this presents a problem remains to be seen. |
| getpass | function | Yes |     |
| getpixel | function | Yes | In true Turbo C, we are guaranteed that the on-screen color corresponds to an actual color in our palette.  In the TurboC library, this isn't true.  The closest-match paletted color is returned.  If there is more than one match, the first one encountered is returned. |
| getpalette | function | Yes |     |
| getpalettesize | function | Yes |     |
| gettext | function | Yes | The format of the text buffer created is the same as in true Turbo C.  The same bytes are produced, and they are stored in the same order.  <br>  <br>11/02/03 and later:  There is a function called gettext in the internationalization library (libintl).  To avoid a conflict, the internationalization library's function will be accessible instead by the name gettextIntl. |
| gettextinfo | function | Yes |     |
| gettextsettings | function | Yes |     |
| gettime | function | Yes | There are two versions of this, one by me, and one by Igor Bujna (called gettime\_d).   I'm not aware of the pros and cons of the two. |
| getviewsettings | function | Yes |     |
| getx | function | Yes |     |
| gety | function | Yes |     |
| gotoxy | function | Yes |     |
| graphics.h | header file | Yes | At present, most graphics.h functionality is supported in one form or another except text output, which is next on the to-do list.<br><br>Use of graphics requires the X-window system. |
| graphdefaults | function | Yes |     |
| grapherrormsg | function | Yes |     |
| \_graphfreemem | function | Yes | This function is not used in any way by the TurboC library -- or hopefully, by the user code -- but has nevertheless been provided. |
| \_graphgetmem | function | Yes | See the comments for \_graphfreemem. |
| graphresult | function | Yes |     |
| highvideo | function | Yes |     |
| huge | type modifier | Yes | Requires TurboC.h.  There's no need for "huge pointers" with a 32-bit compiler, so this is simply an empty macro. |
| imagesize | function | Yes | Important:  see the warnings for the getimage function above. |
| initgraph | function | Yes | Causes a separate graphics window to open.  (The parent text-based window remains visible.)  The location of this graphics window (i.e., locally or remotely) is dependent on the value of the DISPLAY environment variable.  Typically (but not always) this variable is set automatically for you by the system, and so you usually don't have to worry about it.  The graphdriver and pathtodriver input-parameters of initgraph are (almost) completely ignored, though the constants used as names for the various drivers are available for your use (DETECT, CGA, MCGA, EGA, etc.).  As far as the graphmode input-parameter is concerned, every mode applicable to any graphics driver is accepted.  All graphics modes through Borland C++ 5.0 are supported:  CGAC0, CGAC1, CGAC2, CGAC3, CGAHI, MCGAC0, MCGAC1, MCGAC2, MCGAC3, MCGAMED, MCGAHI, EGALO, EGAHI, EGA64LO, EGA64HI, EGAMONOHI, HERCMONOHI, ATT400C0, ATT400C1, ATT400C2, ATT400C3, ATT400MED, ATT400HI, VGALO, VGAMED, VGAHI, PC3270HI, IBM8514HI, IBM8514LO. |
| insline | function | Yes |     |
| installuserdriver | function | Yes (sort of) | Graphics drivers as such are not used by the TurboC library (which allows every defined graphics mode to be used by every "driver" anyway).  This function, therefore, does nothing.  It always returns a value of IBM8514. |
| installuserfont | function | TBD |     |
| io.h | header file | Yes | This is supported in the sense that a file of this name is provided.  However, not all of the functions are necessarily supported. |
| kbhit | function | Yes |     |
| line | function | Yes |     |
| linerel | function | Yes |     |
| LINES | variable | n/a | This is not present in true Turbo C.  It is an **ncurses** variable that tells the current number of text rows in the display console. |
| lineto | function | Yes |     |
| lowvideo | function | Yes |     |
| mkdir | function | Yes | A function of the same name is provided by GNU **gcc**, but behaves somewhat differently in the Linux environment.  The version provided by TurboC corrects that behavior. |
| moverel | function | Yes |     |
| movetext | function | Yes |     |
| moveto | function | Yes |     |
| normvideo | function | Yes |     |
| outtext | function | Yes | See the comments for outtextxy. |
| outtextxy | function | Yes | See the comments for settextstyle. <br><br>Borland seems to perform clipping on text by not displaying any _characters_ that aren't wholly within the viewport.  The TurboC library performs clipping on text by not displaying any _pixels_ of the characters that are outside of the viewport.  In other words, the TurboC library may display partial characters in some cases, whereas true Turbo C will not do so. |
| pieslice | function | Yes |     |
| putch | function | Yes |     |
| putimage | function | Yes | Important:  see the warnings for the getimage function above. |
| putpixel | function | Yes |     |
| puttext | function | Yes | See the notes for gettext. |
| random | function | Yes | Requires TurboC.h. |
| randomize | function | Yes | Requires TurboC.h. |
| rectangle | function | Yes |     |
| registerbgidriver | function | Yes (sort of) | This function serves no purpose in the TurboC library, since there is no need to load separate graphics "drivers".  Hence, this function actually does nothing.  It always returns a value of IBM8514, which probably doesn't correspond to what you might have wanted.  Therefore, it is probably best to write this function out of your code. |
| registerbgifont | function | TBD |     |
| restorecrtmode | function | Yes | Note that Borland's documentation does not specify whether the resulting text-mode screen is cleared or not.  With the TurboC library, it will _not_ be cleared. |
| sector | function | Yes | Requires the "-lm" compiler switch. |
| setactivepage | function | Yes |     |
| setallpalette | function | Yes |     |
| setaspectratio | function | Yes | There's a function of this name, but it does nothing, on the assumption that the pixels are always square. |
| setbkcolor | function | Yes | See the comment for setpalette below. |
| setcolor | function | Yes |     |
| \_setcursortype | function | Yes | This function was not present in Turbo C 2.0, but appeared in some later versions.  The cursor type, as in Turbo C, is one of the following: \_NOCURSOR, \_NORMALCURSOR, \_SOLIDCURSOR.  However, ncurses does not provide these choices as such; it provides only "invisible", "visible", and "very visible", with the differences between these being dependent on the target platform.  For example, on my iMac Linux box, both \_NORMALCURSOR and \_SOLIDCURSOR appear as indistinguishable block cursors.  In other words, your mileage may vary. |
| setfillpattern | function | Yes |     |
| setfillstyle | function | Yes |     |
| setftime | function | No  | The closest \*nix function corresponding to this that I can find is utimes, which unfortunately requires knowing the filename.  On the other hand, setftime requires the _handle_ of an open file.  There's no clean way I know to determine a filename given a file handle.  I don't see any clean way to implement this without intercepting all open and fopen calls.<br><br>Workaround:  Rewrite your code to use utimes rather than setftime.<br><br>**Note**:  Actually, there is a way of writing such a function without trapping fopen, but it's _so_ unclean that I'd hesitate to do it.  I'd be happy to [receive](mailto:info@sandroid.org) someone else's code for it, though.  Given the file handle, the fstat function can reveal the file's "inode".  There's no way to do a reverse lookup and determine a filename given an inode, but a search of the entire file-system (using the **find** function from the command line, with the appropriate switches) can give a list of the filenames of all files with a given inode.  The search can be made _relatively_ efficient by first searching just the current directory, and then searching the home directory only on failure, and then searching the entire file-system only on failure.  (Unfortunately, the inode numbers are not necessarily unique.  They are only unique within a given file-system, and if multiple file-systems are mounted, they could each have files with the same inode.)  Finally, after **find** has determined the filename, the utimes functions could be used.  Ick! |
| setgraphbufsize | function | Yes ... and no | Yes there is a function of this name.  No, it doesn't do anything, though it does return the correct values.  I don't allow you to alter the size of any memory buffers used internally by the TurboC library. |
| setgraphmode | function | Yes |     |
| setlinestyle | function | Yes |     |
| setpalette | function | Yes | In true Turbo C, changes to the palette may instantly be seen on the graphics display, because true hardware modes within the graphics controller are affected.  With the TurboC library, only future drawing operations are affected, but pixels which have already been drawn are unaffected. |
| setrgbpalette | function | Yes | See the comment for setpalette above. |
| settextjustify | function | Yes | Borland Turbo C 2._x_ seemingly has a bug in which if the text style is HORIZ\_DIR, then LEFT\_TEXT is treated as RIGHT\_TEXT .  This bug has not been duplicated in the TurboC library. |
| settextstyle | function | Yes ... and no | So far (20020608), only the bitmapped font (DEFAULT\_FONT) has been implemented. <br><br>Eventually, all stroked fonts defined in Borland C++ 5.0 will be supported, supposing that I can find appropriate open-source fonts with which to implement them:    TRIPLEX\_FONT, SMALL\_FONT , SANS\_SERIF\_FONT, GOTHIC\_FONT, SCRIPT\_FONT, SIMPLEX\_FONT, TRIPLEX\_SCR\_FONT, COMPLEX\_FONT, EUROPEAN\_FONT, BOLD\_FONT |
| setusercharsize | function | TBD |     |
| setviewport | function | Yes |     |
| setvisualpage | function | Yes |     |
| setwritemode | function | Yes | Supports not only the modes COPY\_PUT and XOR\_PUT specified by Borland, but also the modes OR\_PUT, AND\_PUT, and NOT\_PUT (which in true Turbo C were used only for the putimage function). |
| sopen | function | Yes | This function (implemented as a macro) was contributed by Igor Bujna.  Never having used it myself, I don't know how closely it matches the Turbo C function.  However, O\_TEXT is defined identically to O\_BINARY, so it clearly doesn't distinguish between text and binary modes. |
| \_stklen | global variable | Yes | Requires TurboC.h.  In Turbo C, this was used to define the size of the stack.  This is pointless in **gcc**, so a variable of this name is provided, but assigning it a value does nothing. |
| strcmpi | function | Yes | Requires TurboC.h.  This is simply a macro that renames the function as strcasecmp. |
| stricmp | function | Yes | Requires TurboC.h.  This is simply a macro that renames the function as strcasecmp. |
| strlwr | function | Yes | Requires TurboC.h. |
| strncmpi | function | Yes | Requires TurboC.h.   This is simply a macro that renames the function as strncasecmp. |
| strupr | function | Yes | Requires TurboC.h. |
| const int TcUnicodeMappings\[256\] | global variable | n/a | This is a resource not available in true Turbo C, but is helpful if you choose to use **Xlib** functions rather than graphics.h functions to display text on a graphics-mode screen.  Except for the usual ASCII printable characters (0x20-0x7E), the character set in normal \*nix fonts is not the same as the normal character set in MS-DOS.  However, you can fake up this character set if a Unicode font (one with font descriptions ending in 10646-1) is used.   The array simply provides a decent Unicode equivalent for MS-DOS characters.  Every character is represented,  but not every character is necessarily present in every Unicode font.  The so-called GNU Unifont supports every character we reference.  Note that lots of characters were missing from the original Borland-supplied stroked fonts anyhow. |
| textattr | function | Yes | (See notes for textcolor.) |
| textbackground | function | Yes | (See notes for textcolor.) |
| textcolor | function | Yes | For color control, Borland's color constants are used, and are assigned the same numerical values as in Turbo C:  BLACK, BLUE, GREEN, CYAN, RED, MAGENTA, BROWN, LIGHTGRAY, DARKGRAY, LIGHTBLUE, LIGHTGREEN, LIGHTCYAN, LIGHTRED, LIGHTMAGENTA, YELLOW, WHITE, BLINK.  Whether or not you _actually_ get blinking when using BLINK seems to depend on the console being used.  For me, it seems to blink with KDE's **konsole** but not with **xterm**.  Anyhow, your mileage may vary. |
| textheight | function | Yes |     |
| struct text\_info | data type | Yes |     |
| textmode | function | Yes | All of the Turbo C 2.0 text modes are supported, and I've included many added later, up to those defined in Borland C++ 5.5.  The same constants, with the same numerical values are used as in Turbo C.  (BW40, C40, BW80, C80, MONO, C4350, ....) <br><br>If you don't explicitly call textmode before beginning to use the various console-i/o operations, there is an implicit call textmode(C80). Unfortunately, even though the initial call to textmode is optional, there is a non-optional call to textmode at the _end_ of the program, not needed by the original Turbo C program but needed by the TurboC library.  A mode called EXITMODE not found in Turbo C is provided to allow ncurses to gracefully shutdown.  You must call textmode(EXITMODE) prior to exiting your program, or else you risk losing control of the console from within which you're running the program.<br><br>TurboC does not actually distinguish between B&W and Color modes; all modes are color modes. <br><br>The rationale for sizing of the text console differs somewhat between Turbo C and ncurses, in a way that makes it difficult to reconcile them with perfect accuracy.  You'll want to read the section titled "[Screen resizing](#Resizing)" for an explanation. |
| textwidth | function | Yes |     |
| struct time | data type | Yes |     |
| TurboC.h | header file | n/a | There is no Turbo C header file of this name, but this is sort of a catch-all header I've created to cover everything not directly covered in conio.h and the other actual Turbo C header files.  For example, it contains the [integer datatype conversion macros](#Integers) and the prototypes for the strupr and strlwr functions (which are handled in Turbo C by the string.h header file, but can't be handled in **gcc** that way because string.h already exists). This file is automatically included if any of the other header files (conio.h, io.h, dir.h, etc.) are included. |
| ungetch | function | Yes | There is an ncurses function of the same name, but somewhat different functionality.  In any source file using conio.h, the conio version of ungetch applies below the #include "conio.h", while the ncurses version of ungetch applies above it.  The ncurses functionality continues to be available, however, as a new function ungetchNcurses . |
| unixtodos | functions | Yes | Contributed by Igor Bujna. |
| wherex | function | Yes |     |
| wherey | function | Yes |     |
| window | function | Yes |     |
| \_wscroll | variable | Yes | This global variable variable was not present in Turbo C 2.0, but is present in some later versions. |

- - -

# Handling of integer datatypes

Since the MS-DOS based Turbo C compiler is 16-bit and GNU **gcc** is inherently 32-bit, there are some unfortunate discrepancies in the sizes of integer datatypes between them.  This _usually_ doesn't matter, but sometimes does matter -- for example, in disk i/o or where the programmer has counted on certain types of rollover.

The TurboC library allows you to handle this problem in two separate ways, at your option.  The first way, of course, is simply to ignore the problem and to assume that an int is an int is an int.  :-)   In other words, datatypes can be left unchanged, and it's up to you to figure it out later, if your program doesn't work.  By _default_, though, TurboC redefines the words short, int , unsigned, and long using macros so that they are replaced by GNU datatypes specifically corresponding to the Turbo C datatypes, namely:

> #define short int16\_t  
> #define int int16\_t  
> #define unsigned uint16\_t  
> #define long int32\_t

(You may wonder why there's a macro for long, since it's a 32-bit datatype on both Turbo C and **gcc**.  If so, you've fallen into the Turbo-thinking trap!  The long datatype is 64-bit for some CPU types supported by **gcc**.)

This scheme has the advantage of transparently fixing almost all data declarations in your program.  Those declarations which are not fixed properly, such as unsigned long or the use of unsigned to define bitfields in a struct, are _broken_.  This is actually good news, because the compiler gives you an error message and you're allowed to fix the problem, rather than just allowing you to assume everything is swell.  The resources you have to fix the problems with are the various explicit datatypes provided by GNU **gcc** (int8\_t,uint8\_t, int16\_t, uint16\_t, int32\_t, and uint32\_t) and some new datatypes defined by the TurboC library corresponding to the original (non-macro-replaced) GNU datatypes: gchar, gschar , guchar, gshort, gushort, gint, guint, glong, and gulong.

For example, after automatic macro conversion, things like the following will be correct

> int i, j, k;  
> unsigned u;  
> char c;  
> short is;  
> long n;

but things like this will be wrong:

> unsigned int i, j, k; // Change to unsigned or to uint16\_t.  
> unsigned char c; // Change to uint8\_t.  
> struct MyStruct  
> {  
>   int i; // Okay.  
>   unsigned x:1; // change to guint.  
>   unsigned y:2; // change to guint.  
> };  
> int main (int argc, char \*argv\[\]) // Change to gint.  
> {  
>   ...  
> }

The automatic conversions are enabled by default, but can be disabled with the "-DDoNotFixIntegers" compiler switch.  You have to decide for yourself which is more appropriate.  Enabling the automatic conversions means that your program is more likely to work correctly once ported.  But it also means that it may be more difficult to maintain afterward because it will be filled with things that _look_ like int but aren't really.

Note that if you want to use "-DDoNotFixIntegers" to compile the code you're porting, a different version of the library is used -- libTurboCu.a rather than libTurboC.a.  Both library versions are automatically built, for versions 20020319 or later.  (For earlier versions, I'm afraid you'll have to build libTurboCu.a yourself by manually changing the Makefile to have the **gcc** switch -DDoNotFixIntegers).

Here is a tabular summary of how the integer datatype declarations are handled if converted automatically by the TurboC library:  
 

| Turbo C type | Size | Equivalent  <br>GNU gcc type | Handling by TurboC |
| --- | --- | --- | --- |
| char | 8   | char | Correct |
| signed char | 8   | signed char | Correct |
| unsigned char | 8   | uint8\_t | Causes compile-time error.  Replace manually with uint8\_t . |
| short | 16  | int16\_t | Correct |
| unsigned short | 16  | uint16\_t | Causes compile-time error.  Replace manually with uint16\_t . |
| short int | 16  | int16\_t | Causes compile-time error.  Replace manually with short . |
| unsigned short int | 16  | uint16\_t | Causes compile-time error.  Replace manually with uint16\_t . |
| int | 16  | int16\_t | Correct |
| int bitfields in structs | varies | int | Causes a compile-time error.  Replace manually with gint . |
| unsigned int | 16  | uint16\_t | Causes compile-time error.  Replace manually with unsigned . |
| unsigned | 16  | uint16\_t | Correct |
| unsigned bitfields in structs | varies | unsigned | Causes a compile-time error.  Replace manually with guint . |
| long | 32  | int32\_t | Correct |
| unsigned long | 32  | uint32\_t | Causes compile-time error.  Replace manually with uint32\_t . |
| long int | 32  | int32\_t | Causes compile-time error.  Replace manually with long . |
| unsigned long int | 32  | uint32\_t | Causes compile-time error.  Replace manually with uint32\_t . |

- - -

# Special-function keys

In Turbo C, special keyboard character such as F1 or Ctrl-PgUp are indicated by a two-byte sequence with the getch function:   If the first byte returned is 0, then the next byte indicates which special-function key has been pressed.  The values for this second byte have been summarized in the fnkeys.h file, which also provides symbolic names for all of them.

Unfortunately, many function-key sequences available using getch in MS-DOS are not available at all using the ncurses library.  (For example, F1 is available, but Ctrl-PgUp is not.)  Others may or may not be available, depending on various factors.   Consequently, while the TurboC library has mimicked this functionality as best it can -- or rather, as best _I_ can -- you may still be in the position of having to change some of the special-function keys used by your program.

Here's a list of all special keys that I believe are handled by Turbo C's getch function, along with my observations of how they are handled by the TurboC library's getch function.  _Your_ mileage may vary.  
 

| Special Key | Symbol provided in fnkeys.h | Under xterm | Under KDE Konsole | Under a text-mode login |
| --- | --- | --- | --- | --- |
| Digits on numeric keypad | n/a (0-9, of course) | Ok  | Ok  | No!  Treated as arrows, PgUp/PgDn. |
| Ctrl-A through Ctrl-Z | C\_A-C\_Z | Ok  | Ok  | Ok, except there is no Ctrl-Z. |
| Alt-A through Alt-Z | A\_A-A\_Z | Ok  | Ok, except that Alt-O has a slight delay before registering. | Ok  |
| F1 through F10 | N\_F1-N-F10 | Ok  | Ok  | Ok  |
| Shift-F1 though Shift-F10 | S\_F1-S\_F10 | Ok, except Shift-F5 terminates program | Ok, except:  Shift-F5 terminates program, and F9-F10 don't work. | Ok, except:  Shift-F5 terminates program, and F9-F10 don't work. |
| Alt-F1 through Alt-F10 | A\_F1-A\_F10 | No!  (These are KDE hotkeys.) | No!  (These are KDE hotkeys.) | No!  (Switches between consoles.) |
| Ctrl-F1 through Ctrl-F10 | C\_F1-C\_F10 | No!  (These are KDE hotkeys.) | No!  (These are KDE hotkeys.) | No! |
| Alt-0 through Alt-9 | A\_0-A9 | Ok  | Ok  | Ok  |
| Arrows | N\_UP, N\_DOWN, N\_LEFT, N\_RIGHT | Ok  | Ok on standalone arrow keys, but not on numeric keypad. | Ok  |
| PgUp, PgDn | N\_PGUP, N\_PGDN | Ok  | Ok on standalone arrow keys, but not on numeric keypad. | Ok  |
| Home, End | N\_HOME, N\_END | No! | Ok on standalone arrow keys, but not on numeric keypad. | No! |
| Ins, Del | N\_INS, N\_DEL | Ok  | Ok on standalone arrow keys, but not on numeric keypad. | Ok  |
| Pad-minus, Pad-plus | N\_PADPLUS, N\_PADMINUS | Ok  | No, treated as regular +/-. | No, treated as regular +/-. |
| Ctrl-right, Ctrl-left | C\_LEFT, C\_RIGHT | Ok on standalone arrow keys, but not on numeric keypad. | No, treated as regular right/left. | No, treated as regular right/left. |
| Ctrl-PgUp, Ctrl-PgDn | C\_PGUP, C\_PGDN | No! | No, treated as regular PgUp/PgDn. | No, treated as regular PgUp/PgDn. |
| Ctrl-Home, Ctrl-End | C\_HOME, C\_END | Ok on standalone arrow keys, but not on numeric keypad. | No, treated as regular home/end. | No! |
| Alt-minus, Alt-equals | A\_MINUS, A\_EQUALS | Ok  | Ok  | Ok on main keyboard, but not on the numeric keypad. |
| Esc | N\_ESC | Ok, though there is a short delay before the keystroke registers. | Ok, though there is a short delay before the keystroke registers. | Ok, though there is a short delay before the keystroke registers. |

I'd be happy to [hear from](mailto:info@sandroid.org) anyone with a clear idea on how make this work more consistently, perhaps by using termcap/terminfo, of which I'm completely ignorant.

- - -

# Text Screen resizing

In true Turbo C, the resolution of the text console is completely under the control of the programmer.  The textmode function sets the resolution, in terms of the number of text rows and columns, and sets the graphics modes appropriately.  If a Turbo C program is run under Microsoft Windows, the same situation applies, in essence, because even though Windows renders the text console by means of graphical operations, the number of text rows and columns is fixed, and cannot be changed by the user.

The situation is different in ncurses -- at least, if using the X-Window system -- because the size of the text console is entirely under the control of the user.  The user can resize the text console at will, and the program has little effective control over the resizing.

This presents a difficulty, because Turbo C programs have naturally been written under the suppositions that the text console size has been set by the program itself, and that the text console is _not_ going to be unexpectedly resized during program execution.  Yet, these suppositions are both rather dodgy with code that has been ported using the TurboC library.

There are several possible approaches to the problem:

1.  For TurboC versions 04/18/02 and later, the screen size is now _correct_ if the ported program is run under **xterm**.  TurboC physically resizes the **xterm** console correctly upon starting the program.  While it cannot prevent the user from manually resizing the window later, it will correct any manual resizing by automatically restoring the window to its correct size.  (Sadly, it is possible for a persistent user to corrupt the window by trying hard enough to resize it, but the behavior is usually pretty acceptable.)  Or,
2.  If for some reason it's impractical to use **xterm**, or if the window manager being used resists TurboC's attempts to resize **xterm** , then you may need to use some method outside of TurboC (such as instructions to the user) to prevent resizing of the window; or
3.  Set the BypassResizeXterm variable to 1, and recode those portions of your program that depend on knowledge of the screen size (such as those using gotoxy and window functions) so that they are more flexible.   For example, don't use things like gotoxy(1,25) , but use gotoxy(1,LINES) instead.

In implementing approach #3, several tools are at your disposal. Firstly, the variables LINES and COLS are available for your use.  These variables always contain the number of text rows and text columns, respectively, in the text console.  Secondly, the TurboC library can detect (in versions 20020331 and later) if the user has resized the console.   When it detects that such resizing has occurred, it calls a function called ConioResizeCallback.  You can optionally provide this function yourself (though the TurboC library provides a default version of the function which does nothing), and can use this function in a variety of ways to take care of the screen resizing.  Perhaps the most straightforward way to do so is to simply allow ConioResizeCallback to completely redraw the screen.

In the latter case, by the way, if the user manually resizes the text console, then your current window (as determined by the windowor textmode function) will automatically be reduced in size (if necessary) to fit the new console size.  However, it will not be correspondingly increased in size of the user later enlarges the text console.  To override this behavior, you'll need to provide an alternative to the ResizeTurboC function.   You'll need to examine the source code for any further explanation.

- - -

# Graphical display characters in text mode

When using functions such as printf, putchar, etc., no attempt is made to translate displayed characters so that they appear identical to those on an IBM PC.  In other words, you're just using the native \*nix functions, and you get whatever you get.  Typically, you should expect only the characters in the numerical range 0x20-0x7E to display accurately.  However, when using the various conio.h functions, such as cprintf , putch, etc., we attempt to match the output characters as closely as possible to those in true Turbo C on an IBM PC.  
  
When I say that we attempt to map the characters as closely as possible, I mean that given a character with a certain numerical code, we attempt to display a character that looks as much as possible like the IBM PC character with that same numerical code.  On the other hand, we have no way to translate string characters appearing in your source code.  What do I mean by this?  Well, consider the character 'é'.  This happens to correspond to character 0x82 in the IBM PC character set.  Suppose you have source code like this:  

  
putch ('é');     /\* bad! \*/  
putch (0x82);    /\* good! \*/  

What makes the first of these two lines "bad" is that the numerical code which the compiler creates for 'é' is based on the character sets and languages installed on the computer doing the compiling, and is very likely not 0x82.  So the first form is almost guaranteed not to work properly.  Even so, calling the second form "good" is stretching a point, because (as it happens), the TurboC library isn't presently able to display the character 'é' anyway.  True Turbo C is able to display any of the characters in the IBM PC character set -- i.e. the standard printable 7-bit ASCII characters, _plus_ additional characters in the numerical ranges 0-31 and 128-255.  However, the TurboC library does not fully support this capability in text mode, because the **ncurses** library does not.  A subset of the IBM PC character set is supported, including linedraw characters.  (However, all linedraw characters representing double-lines or combinations of double/single lines are represented simply in terms of single lines.)  
 

|     |     |
| --- | --- |**Screenshots of displayable Characters**
| [![True Turbo C display characters -- click to enlarge.](sm-RealTurboChars.png)](RealTurboChars.png)  <br>True Turbo C | [![TurboC library display characters -- click to enlarge.](sm-FakeTurboChars.png)](FakeTurboChars.png)  <br>TurboC library |

([Click here](TurboCChars.c) to see the program that generated this output.)  As you may notice, non-supported characters are rendered as dots.  Actually, this can be changed within the ported program to any other character as follows:

> /\*  
>   Add this code prior to the FIRST use of any conio function.  
>   The default character cannot be changed after initialization.  
>   The values used are any integer character code recognized by  
>   ncurses (NOT IBM PC character codes).  
> \*/  
> extern gint DefaultChar;  
> DefaultChar = ' ';              /\* or whatever \*/

If this still isn't good enough for you, you can individually control the mapping of every character.  For example:

> /\*  
>   Add this code AFTER the first use of textmode.  
>   It will be overridden if placed BEFORE textmode.  
> \*/  
> extern gint TranslatedChar\[256\];  
> TranslatedChar\[0x9d\] = 'Y';     /\* support YEN as 'Y'. \*/

- - -

# Graphics mode

**Note**:  The material in this section and the next describes both features that exist and features that are planned for the future.  Consult the [change log](changes.html) for more info about the support provided in specific code versions.

In true Turbo C, graphics-mode screens (as opposed to text-mode screens) are handled by something called the Borland Graphics Interface, or BGI for short.  The BGI is an API containing 80+ functions.  Note that the graphics functions provided with the TurboC library are somewhat less portable than the remainder of the library, in that the X-window system is required.  If you need graphics functions that can operate without **X**, I'd suggest reading the [next section](#Alternative).

Some of the more important points of departure from true Turbo C are these:

*   _External files_.  True Turbo C relies on the existence of certain plug-in files, specifically the BGI "drivers" (such as EGAVGA.BGI, HERC.BGI, etc.) and the fonts (such as TRIP.CHR, SANS.CHR, etc.).  The TurboC library has no such dependence, though it may eventually be possible to load new fonts at runtime.  Functions related to manipulation of these files are provided, so that you don't have to take the effort of writing them out of your code, but often they are empty stubs.
*   _Missing functions_.  Implementation of the floodfill function is not presently planned.
*   _Screen hiding_.  Since true Turbo C ran under MS-DOS, a non-windowed non-graphical environment, the way it had to operate was to take over the entire display screen, and to place the hardware graphics controller itself either in "text mode" or in "graphics mode".  In other words, if the graphics controller was in text mode then only textual functions (like those in stdio.h or conio.h) could be used, whereas in graphics mode only graphical functions (graphic.h) could be used.  Material written to the screen in text mode disappeared when graphics mode was started, and vice-versa.   With the TurboC library, because a windowed environment is used, both text-mode windows and graphics-mode windows can appear simultaneously.  In other words, when graphics mode is started, a new graphics-mode window is created, but no effort is made to hide the old text-mode window.
*   _Palettes_.  Since true Turbo C dealt with actual modes of the hardware graphics controller, palettes were built into the graphics controller itself, and _changes_ to palettes (for example, reassignment of a palette color, like the background color) were instantly reflected on-screen.  With the TurboC library, changes to the paletted (and specifically, to the background color) are reflected only in future screen-writes, and don't affect anything already written to the screen.
*   _Text_.  The bitmapped font is supported.  For various reasons, sadly -- having spent a considerable amount of work on it -- I find that it's not convenient to support Borland's "stroked font" (CHR) format.  (These reasons include the following:  Borland's license does not allow using the Borland-supplied fonts with the TurboC library; nobody has made any free CHR fonts available on the Internet; and, while there are TrueType-to-CHR programs available, they cost more than I'm willing to pay -- i.e., more than zero.)  Therefore, the functionality otherwise normally provided by the stroked fonts will eventually be supplied indirectly by means of \*nix system fonts.  Currently, this has not yet been implemented.  Or, you may draw text directly using **Xlib** functions, as described a few lines below.  You may also find the [TcUnicodeMappings](#TcUnicode) array useful when using **Xlib**.
*   _The_ getimage _and_ putimage _functions_ .  This is described in detail [above](#getimage).  Briefly, these functions are used to fetch a rectangular area of the display into an image buffer, or to write the image back to the screen.  The formats of the image buffers were undocumented by Borland, but actually corresponded to the memory maps used by the specific graphics controllers and graphics modes.  These formats are _not_ duplicated by the TurboC library.
*   _Flexibility_.  You don't need to confine yourself to the BGI functions.  You can also use X-window library functions.  For example, \*nix system fonts -- which BGI obviously doesn't support -- can be used in a TurboC-ported program via **Xlib** functions like XLoadFont , XDrawString, etc.  All you have to know -- other than basic X operations -- is that the TurboC library exposes the following X objects for your use:

Display \*TcDisplay;       // The X-window "display".  
Window TcWindow;          // The X-window "window".  
gint TcScreen;            // The X-window "screen".  
GC TcGc;                  // The X-window "graphics context".

It's necessary to modify the graphics context (TcGc) for any serious drawing.  If you don't restore it to its original settings, the TurboC library will become seriously confused.  Therefore, it's best to _copy_ the graphics context (with X-window function XCreateGC followed by XCopyGC), and work only with your copy of the graphics context rather than with the original.  Also, to avoid threading conflicts, always wrap your X operations as follows:

XLockDisplay (TcDisplay);  
// ... your X code goes here ...  
// For Example, draw a text string at x=10, y=20:  
XDrawString (TcDisplay, TcScreen, TcGc,  
             10, 20, "I am text", 9);  
XSync (TcDisplay);  
XUnlockDisplay (TcDisplay);

- - -

# Alternative graphics-mode functions

If the graphics-mode discrepancies described above are too awful for you to live with, it is possible in theory to disable the TurboC-provided graphics functions and instead to use alternate functions.  (I've not actually tried any of this myself, so if you make it work I'd appreciate it if you could [send me](mailto:info@sandroid.org) detailed instructions.)

For example, there is a 2D graphics library called the [GRX library](http://www.gnu.de/software/GRX/) ( by Csaba Biegl, Michael Goffioul, and Hartmut Schirmer).  The GRX website does not advertise (or even mention) Turbo C compatibility. However, it contains quite a few functions of the same name (and, apparently, the same functionality) as Turbo C library functions.   To find out more, download the library using the link above.  After unpacking the tarball, you'll find the Borland replacement functions in the src/bgi directory, and information about them in the doc/readme.bgi file.

Disabling the graphics functions present in the TurboC library involves these steps:

1.  Edit the TurboC library's Makefile, remove the compiler switch "-DWITH\_X", and rebuild the TurboC library.
2.  Eliminate the TurboC version of graphics.h from the system includes (probably /usr/include).
3.  Install GRX.

Realize that I have not really evaluated the GRX library, and so do not understand its ins and outs.  For example, I don't know how many BGI functions it implements.  I _believe_ that the main advantage of GRX relative to TurboC is that in a system without **X** it can directly used the graphics controller. I believe that the main disadvantage of GRX relative to TurboC is that it is not integrated with the other non-graphics Turbo C functions like getch.  Since the TurboC library's version of getch (for example) depends on the TurboC library's implementation of graphics functionality, it will not work properly with GRX.

- - -

# Endian conversions

Since all Turbo C functions have been run only on Intel 'x86 processors, it is not uncommon in Turbo C code to find that the programmer has simply _assumed_ that the CPU is little-endian -- i.e., that the LSB of multi-byte integers precedes the MSB.  In porting such code to GNU **gcc**, one has to recognize that it _may not_ necessarily be run on a little-endian processor.  For example, I do a lot of work on an iMac, which is based on a big-endian PowerPC processor.  This is not important in most cases, but does affect various operations such as the matching of bytes within union objects and, especially, reading/writing files.

To make it easier to deal with this problem, I've provided a number of functions not originally present in Turbo C that can be used to convert endian types where required.  Unfortunately, it's still up to you to figure out that there's a problem and to add these function calls to the program.

There are two separate groups of functions, those which convert the endian type of an integer value already stored in memory, and those which convert data on-the-fly whilst reading/writing a file.  All of the functions require the TurboC.h header file.  
 

| Function prototype | Description |
| --- | --- |
| void FixLittle16 (uint16\_t \*); | Performs an in-place conversion of a 16-bit integer value stored in memory from little-endian format to the natural CPU endian format, _or vice-versa_ .  Using this function twice in succession gets back whatever you started with.  There's no harm using this function if the CPU happens to be little-endian, since the value is passed through unchanged in that case. |
| void FixLittle32 (uint32\_t \*); | Same as FixLittle16, but 32-bit instead. |
| void FixBig16 (uint16\_t \*); | Same as FixLittle16, but big-endian instead. |
| void FixBig32 (uint32\_t \*); | Same as FixLittle16, but 32-bit big-endian instead. |
| int ReadLittle16 (FILE \*fp, uint16\_t \*Value); | Reads a 16-bit little-endian integer from a file, and delivers it in the natural endian-format of the CPU.  Returns 0 on success, non-zero on failure. |
| int ReadBig16 (FILE \*fp, uint16\_t \*Value); | Same as ReadLittle16, but big-endian instead. |
| int ReadLittle32 (FILE \*fp, uint32\_t \*Value); | Same as ReadLittle16, but 32-bit instead. |
| int ReadBig32 (FILE \*fp, uint32\_t \*Value); | Same as ReadLittle16, but 32-bit big-endian instead. |
| int WriteLittle16 (FILE \*fp, uint16\_t Value); | Takes a 16-bit integer value in the natural endian format of the CPU, and writes it to a file as a 16-bit little-endian integer.  Returns 0 on success and non-zero on failure. |
| int WriteBig16 (FILE \*fp, uint16\_t Value); | Same as WriteLittle16, but big-endian instead. |
| int WriteLittle32 (FILE \*fp, uint32\_t Value); | Same as WriteLittle16, but 32-bit instead. |
| int WriteBig32 (FILE \*fp, uint32\_t Value); | Same as WriteLittle16, but 32-bit big-endian instead. |

- - -

# Examples

_Hello, world!_  Consider the following Turbo C program:

> #include <conio.h>  
> #include <time.h>
> 
> void  
> main (void)  
> {  
>   time\_t t, newt;  
>   int i = 0, x, y;  
>   time (&t);  
>   clrscr ();  
>   cprintf ("Hit any key to continue: ");  
>   x = wherex ();  
>   y = wherey ();  
>   // Wait for a keypress, flashing the message  
>   // "I'm waiting!" on and off once per second.  
>   // After any key is hit, display "Hello, world!"  
>   while (!kbhit ())  
>     {  
>       time (&newt);  
>       if (t != newt)  
>         {  
>           t = newt;  
>           i++;  
>           if (0 == (i & 1))  
>             cprintf ("I\\'m waiting!");  
>           clreol ();  
>           gotoxy (x, y);  
>         }  
>     }  
>   getch ();  
>   cprintf ("\\r\\nHello, world!\\r\\n");  
> }

This program almost compiles with GNU **gcc** (and the TurboC library), and almost works as-is,  except for a couple of points:

*   You will want to add the following statement at the very end of the program:

textmode (EXITMODE);

*   The "Hello, world!" message won't actually be seen, because the textmode(EXITMODE)you have to end with will instantly restore whatever was on the screen prior to running the program.  So, you might want to add another getch() printing the "Hello, world!" message.
*   GNU **gcc** expects main to be of type int rather than void, and of course you can't actually use int because of the automatic conversion of integer datatypes.  So use gint instead.

Having done these things, you'll find that you can compile the program and run it without any errors:

> gcc -o Hello Hello.c -lTurboC -lncurses  
> xterm +sb -e Hello

Compiling without warnings is actually a little unusual, because there are quite a few things that can cause non-fatal warning messages.  See the [trouble-shooting](#Trouble) section for more detail.

- - -

# Trouble-shooting

Well, as you've seen, there are _many_ factors that come into play porting from Turbo C to gcc/TurboC.  Some of these are describe above, but there are others that haven't even been hinted at.  For the sake of completeness, here's a somewhat-complete list of the difficulties I've personally encountered:

_Summary or illumination of trouble-shooting items covered earlier_

*   Whenever possible, run the ported program under **xterm**, with the command-line switches "+sb -tn linux".
*   Functions needed by Turbo C may not have been provided in the TurboC library [function list](#Entitites).  Solution:  Write them and [send them to me](mailto:info@sandroid.org).
*   Integer datatypes may not have been converted properly.  Solution:  Handle as described [earlier](#Integers).
*   Some functions, such as strupr, strlwr, and fcloseall are not handled by any of the Turbo C header files.  Solution:  #include <TurboC.h> .
*   The TurboC library cannot physically resize the console unless the ported program is being run under **xterm**.  Solution:  Manually resize the console, rewrite the code to adjust automatically to size changes, or [send me a fix](mailto:info@sandroid.org).
*   The ported program does not release control of the console on exit.  Solution:  Add a call to textmode(EXITMODE) before exiting the program.
*   Functions don't behave as expected.  Quite a few functions are common to Turbo C and to **gcc**, but act somewhat differently.  An example is printf.   In **gcc** this supports the carriage-return character ('\\r'), but in true Turbo C does not.  I've chosen _not_ to correct this.  Other functions, like getch , ungetch, and mkdir, are different enough that I've had to correct for their differences.  Typically this is done by means of macros, so that these functions have their **gcc** interpretations above the point in the source file where their associated header files are included, and have their Turbo C interpretations below that point.  Solution:  Learn to recognize it, I guess.  The **gcc** functions are always available, even after macro assignment, by means of a new name, such as getchNcurses.
*   Not all [special-function keys](#Keys) on the keyboard are supported. Solution:  Rewrite the code to eliminate the keys that aren't supported, or [send me a fix](mailto:info@sandroid.org) .
*   Not all [graphics characters](#Characters) are supported in console i/o mode (cprintf/putch/etc.), and _none_ of them are supported in regular text mode (printf/putchar /etc.).  Solution:  Rewrite the code to eliminate those characters, or [send me a fix](mailto:info@sandroid.org).
*   char is signed but is supposed to be unsigned (or vice versa).  Solution:  Both Turbo C and **gcc** have options/switches for setting whether characters are signed or unsigned by default.  Make sure you set the switch in gcc the same way as the option in Turbo C.
*   Main function is of the wrong type.  The **gcc** compiler expects main to be of type int, whereas in Turbo C these are often (always?) of type void.  Solution:  Change it.
*   You may experience some compiler errors if integer datatype conversion is turned on but a compiler optimization level higher than -O0 is used when compiling your code.  Solution:  reduce the optimization level to -O0 and [report the problem](mailto:info@sandroid.org).  This applies only to compiling your own code, and not to building the TurboC library.
*   Allocating the image buffers for the getimage function:  The proper sequence of operations, as defined by Borland's docs, is this:

// Good code, approved by Borland and by the TurboC library.  
void MyFunction (void)  
  {  
    void \*bitmap;  
    bitmap = farmalloc (imagesize (left, top, right, bottom));  
    getimage (left, top, right, bottom, bitmap);  
    ... do stuff, such as call the putimage function ...  
    farfree (bitmap);  
    return;  
  }

If instead your program does stuff like the following, it will probably work, but you'll have a memory leak in which the memory allocated for the image buffers won't be _fully_ deallocated:

// Bad code, by any standards.  
void MyFunction (void)  
  {  
    char bitmap\[4 + X\_SIZE \* Y\_SIZE \* COLOR\_DEPTH\];  
    getimage (left, top, left + X\_SIZE - 1, top + Y\_SIZE - 1, bitmap);  
    ... do stuff, such as call the putimage function ...  
    return;  
  }

The problem is that getimage has to transparently allocate memory of which you're not aware -- specifically, X-window system Pixmap structures.  In the olden days, programmers (such as myself) were tempted to write this kind of bad code because they believed that they understood the format of the graphical data in the image buffers -- even though Borland did not specify or even hint at its nature.  This assumption is now revealed as being false.*   Manipulation of the image buffers used by the getimage and putimage functions:   Borland does not specify the format of the data in the image-buffer structures used by the getimage and putimage functions.  (See the item above.)  All that is specified is that this:

struct {  
  unsigned short width;  
  unsigned short height;  
  ... some undocumented stuff representing graphical data ...  
};

For practical reasons, it is likely that some (perhaps many) programmers -- such as myself -- hacked the format of the graphical-data area, and chose to manipulate the area directly.  Programs based on this principle will not work, because the format of this area -- at least in the X-window based implementation of graphics.h functions -- is not the same in the TurboC library as it was in Borland's Turbo C.    (**Note**:  it is possible to rewrite the code for getimage and putimage so that the old Borland formats are preserved.  The formats were dependent on the graphics controller and the graphics mode.  Anyone who cares to hack all of these and to rewrite the code accordingly is very welcome to do so.  Please send me the fixes.  As of Borland C++ v5.0, the graphics.h file listed 43 graphics controllers and 29 graphics modes by name.  The comments in the next trouble-shooting item below may be of assistance to you, if you try to undertake this task.)  However, it _is_ possible to manipulate the graphical data in the TurboC library image buffers, because I will tell you the format.  You need to use X-window drawing functions such as XDrawLine, as applied to X-windowPixmap structures.  The format of the image buffer, for the X-window implementation of the TurboC library, is as follows:

struct TcImageBuffer  
{  
  gushort Width;  
  gushort Height;  
  Pixmap Handle;  
};

*   Saving getimage image buffers to disk, or loading putimage image buffers from disk:  If you read the discussion above, it will be obvious to you that this is next-to-useless, since the image buffers themselves no longer contain graphical data.  What needs to be save or loaded is the Pixmap structures themselves, whereas the image buffers contain only the handles of the Pixmap structures.  I believe that it is possible to get around this problem by rewriting the getimage /putimage functions in terms of the X-window functions XGetImage and XPutImage rather than Pixmap structures.  If you want to do this and send me the patches, [tell me about it](mailto:info@sandroid.org).

_Trouble-shooting items not covered earlier_

*   Various standard C library "functions" may actually be implemented as macros on some target platforms, and as true functions on others.  A common example is strcpy.  When such macros contain casts (such as "(unsigned int)") that are questionable as described above under [handling of integer datatypes](#Datatypes), code containing them will fail to compile. It's difficult for me to catch all of these, since they may be perfectly fine on my test machines.  For clues as to how to work around such problems, look at the implementation of strcpy in TurboC.h.  Sometimes, changing the compiler optimization level to "-O0" fixes the problem.  Or, just don't use the automatic datatype conversion.
*   Header files are messed up.  The TurboC header files all include TurboC.h, which by default redefines short, int, unsigned , and long.  Solution:  Any standard system headers, must be included _before_ any TurboC header, or else the reassignments of the integer datatypes will destroy them.  Actually, TurboC.h itself includes a variety of standard system headers -- like stdlib.h, stdio.h, string.h, ctype.h, and so on -- and this problem won't arise for _those_ headers.   But your typical \*nix system has a cadjillion header files, and TurboC.h can't include them all.
*   Filenames don't work in fopen.   MS-DOS expected filenames like C:\\MYDIR\\MY.FIL, whereas \*nix expects names like /MYDIR/MY.FIL.  Also, \*nix is case-sensitive, whereas MS-DOS is not.  So if you have any hardcoded filenames that violate these rules, you'll have to fix them. I suppose I could have fixed fopen to get around some of these problems, but I have not done so.
*   scanf/gets/getchar doesn't work.   In Turbo C, to a certain extent you can get away with mixing and matching getchar & getch or cgets & gets.  This doesn't work with the TurboC library because once the conio text console is active you can't use scanf/gets/getchar until closing the conio console.  Solution:  Rewrite the code to eliminate gets/getchar in favor of cgets/getch (or vice versa).  But note this:  In my experience, a large motivator for using conio was often simply that Turbo C's print/puts functions did not correctly support '\\r'. **gcc** 'sprintf/puts functions do correctly support '\\r', so you might not even need conio.
*   The compiler gives weird errors for printf/scanf/(etc.) commands.  The **gcc** compiler is intelligent enough to find many discrepancies between format strings and value lists in printf statements.  For example, it can recognize that this is an error:

long n;                // a long  
printf ("%d\\n", n);    // an int

But it's not smart enough to necessarily recognize that the following _isn't_ an error:

uint32\_t n;            // a long  
printf ("%ld\\n", n);   // a long

That's okay, though, because **gcc** produces only warning message rather than fatal errors for these problems.  Besides, you'll want to check all of your i/o statements carefully anyway, since stuff like the following is correct in Turbo C but not in gcc/TurboC:

int n;                 // 16-bits  
printf ("%d\\n", n);    // 16-bit in Turbo C but 32-bit in gcc.

*   printf/scanf/(etc.) don't work, but there's no compiler error.  See the item above.
*   File i/o is messed up.  File i/o is _not_ messed up, but since different datatypes are supported in Turbo C than in gcc/TurboC, the data formats you're reading/writing may be different.  This can happen in numerous ways:

*   Integer datatypes are the [wrong size](#Integers).
*   Integer datatypes are the [wrong endian type](#Endian).
*   printf/scanf/(etc.) may use the wrong format strings (see above).
*   Floating-point formats differ.  Sorry, but I've no clue about this.
*   Structures are packed differently.  For example, a structure like this in Turbo C

struct MyStruct {  
  char c;  
  short s;  
  int i;  
  long n;  
};

might have to be rewritten as follows in gcc/TurboC if you expect to use it for direct i/o to a file:

struct MyStruct {  
  int8\_t c \_\_attribute\_\_ ((packed));  
  int16\_t s \_\_attribute\_\_ ((packed));  
  int16\_t i \_\_attribute\_\_ ((packed));  
  int32\_t n \_\_attribute\_\_ ((packed));  
};

I can't guarantee that this _always_ works, but I've ported a reasonably substantial [text-indexing system](http://www.sandroid.org/RIP/index.html) from MS-DOS (16-bit, little-endian) to LinuxPPC (32-bit, big-endian) that made heavy use of directly inputting/outputting structures to files, and the file formats were preserved.  That seems to me to be a good sign.

*   Interrupt service routines.  Sorry, I don't even want to think about this.
*   Inline assembler code.  It can be done, but I don't know how, and the syntax will be different.  I personally don't care about this problem, because I'm interested only in completely portable code. Sorry!
*   Making DOS or BIOS system calls.  Obviously, you can't.
*   Turbo C pragmas.  These will get warning messages from the compiler, but will be otherwise ignored.  But they're confusing, so unless you expect to go back to MS-DOS someday, I'd suggest removing them manually. :-)

- - -

_©2002,2004 Ronald S. Burkey.  Last updated 02/14/04 by RSB.  Contact [me](mailto:info@sandroid.org)._
