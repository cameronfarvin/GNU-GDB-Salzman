# Using GNU's GDB Debugger
###### By Peter Jay Salzman
 
# Chapter 0: Administrata
## Chapter 0.0: Why Write This Tutorial?
This is one of the most comprehensive GDB tutorials on the Internet. It's more than you'd find in most books, which tend to discuss GDB as a lightning-fast afterthought. I wrote this document because I couldn't find a good GDB tutorial. The only comprehensive source of information about GDB is GNU's GDB User's Manual, but learning GDB from it is like learning a foreign language from a dictionary.

I'll be using sample programs, and there will be links to the source code in each section that uses them, along with compilation instructions. I urge you to download the code and follow along with the examples. Following along, doing it yourself as you read, is really the best way to learn.

## Chapter 0.1: Acknowledgements and Dedication
This tutorial is sincerely and respectfully dedicated to Richard M. Stallman, the most important and under appreciated hero of the Free Software movement.

I'm in a perpetual state of learning, and thanks goes to the following people who've helped me understand C and GDB:
* Will Deutsch: For answering questions about GDB.
* Mike Simons: For answering questions about GDB.
* Paul Hinton: of Wolfram Research for convincing me to try this crazy thing called "GNU/Linux".
* Jeff Newmiller: Who has yet to be stumped by any question I throw at him.
* Norm Matloff: Who seems to know everything that I don't know (which is a LOT!)
* Mark K. Kim: Who never tires of my questions and has an amazing ability to incorporate out-of-box thinking with formal learning. A true hacker, good friend, and humble guy.

## Chapter 0.2: Authorship and Copyright
This entire tutorial is copyright (c) 2004 Peter Jay Salzman, p@dirac.org. Permission is granted to copy, distribute and/or modify it under the terms of The Open Source License, version 1.1. You can find a copy of this license at opensource.org/licenses/osl.php

The canonical and most updated version of this document can be found at www.dirac.org/linux/gdb.

If you want to create a derivative work or publish this document for commercial purposes, I would appreciate it if you contacted me first. This will give me a chance to give you the most recent version. It'll also stroke my ego. I'd also appreciate either a copy of whatever it is you're doing or a spinach, garlic, mushroom, feta cheese and artichoke heart pizza.

## Chapter 0.3: About Exercises
There are exercises at the end of most sections. The exercises are mandatory. These exercises are designed to both cover topics I don't formally cover, and give you experience using your new-found skills.

There are topics I don't cover except for in the exercises. This isn't because I'm lazy. It's because I want you to think. Use your noggin to begin understanding concepts in your own words, not in my words. I want you to develop intuition. The best debugging tool is not GDB. And it certainly isn't printf(). The best debugging tool is your brain.

## Chapter 0.4: Thank You's
The following people sent in corrections:
* Nick
* Jason E. Siefken
* Eric T. Stuebe
* Jeff Terrell
* Lawrence Poorman
* Yi Yang
* Aaron Mayerson
* Doug Yoder

## Chapter 0.5: A Plug For The EFF
If you're not a member of the EFF, you must stop everything you're doing and become a member right this moment. 9/11 was a horrible tragedy; I was in New York City at the time and witnessed the chaos with my own two eyes. I love my country, and am a very proud United States citizen, but the steady erosion of our freedoms and civil liberty is another tragic casualty of the post 9/11 era. I'm very worried for my country.

The EFF is the most important defense we have in protecting our on-line and digital rights. If you have any interest in protecting your civil liberties in a digital age that has gone out of balance, please read their very short mission. Please consider becoming a member of the EFF. Honestly, it's only the price of a pizza. Or the cost of two movie tickets plus popcorn.

## Chapter 0.6: A Request For Help
This tutorial took (takes?) more time than I care to admit. It's a tremendous job. If you found this tutorial to be at all useful, please consider helping me maintain and actively develop it. There are many ways you can help. Pick one that suits you or your talents (in no particular order):

* Become a member of the EFF, or buy their merchandise.
* Become a member of the FSF, or buy their merchandise.
* If you're handy with HTML or PHP, please send an email offering to help with the website. I don't want fancy pages (I want lynx/links users to be able to use this site), but I'm really just a "hack" at HTML and PHP. If you're handy with design or formatting, please offer some advice on how to make my pages more readable and good looking.
* Report spelling errors, technical errors, and broken links.
* Email me questions. Tell me if something isn't clear.
* Send me a picture postcard from where you live. Better yet, send me a letter with either a picture of yourself or the area where you live. Let me know how you got involved with free software and what you use GDB for. My mailing address is Peter Jay Salzman, 111 Montague Street, Brooklyn NY 11201, USA.
* Proofread.
* If you live in Italy, email me pictures of pizza from your country. No, this isn't a joke. I really love pizza.

I may not respond to all email. It really depends on how busy my life is at the moment (this isn't my whole life, you see). If I have time, I'll try to reply. But please don't get upset if I can't reply. It means I'm swamped with work and can't afford to reply. Rest assured, even if you don't hear from me, receiving an email from you is still helpful. It reassures me that people are reading and benefiting from my work.

# Chapter 1: Introduction
## Chapter 1.0: What Is A Debugger?
A debugger (or more accurately, symbolic debugger), is an application that runs your program, just like you can, when you type the name of your program. The difference is, a debugger can step through your source code, line by line, executing each line only when you want it to. You can even step through your program machine instruction by machine instruction (try that with printf())! At any point, you can inspect and even change the value of any variable at run-time. If your program crashes, a symbolic debugger tells you where and why the program crashed so you can deduce what went wrong. You can go through the program and see what source code lines get executed and in what order.

Do you have an infinite loop? No problem! Use a debugger to step through the loop and see why your conditional fails to do what you had expected. Did the program crash on a variable access? No problem! The debugger will tell you all sorts of information about the variable you tried to access and the value you assigned (or perhaps didn't assign) to it. Is there a line in your code which isn't executing? No problem! Use the debugger to see what gets executed, in what order, and why a particular line isn't getting reached! Other than a compiler, the debugger is the most useful tool a programmer can use.

## Chapter 1.1: Why Not Use printf()?
Most people use the printf() debugging method. This is called adding "trace code" to your program. Simply put, they sprinkle their code with printf() to view the value of variables at certain strategic points and also to examine the order of execution of lines of source code.

There are a few reasons why this may not be the best way of doing things:

* Sometimes you need a lot of printf()'s, and it can get tedious putting them in and taking them out. Inserting and deleting superfluous code all the time is really distracting. It draws attention away from what you're doing. It's like trying to implement a linked list while someone is talking to you about last night's Futurama episode.
* A symbolic debugger can do an awful lot that printf() can't. You can do just about anything you can think of, including changing the value of variables at run-time, halt the program temporarily, list source code, printo the datatype of a variable or struct that you don't recognize, jump to an arbitrary line of code, and much, much more.
* You can use a symbolic debugger on a running process; you don't even have to kill the process! Try that with printf()!
* You can use a symbolic debugger on a process that has already crashed and died without having to re-run the program. You'll see the state the program was in at the time of death and can inspect all the variables.
* A knowledge of GDB will increase your knowledge of programs, processes, memory and your language of choice.

You'll be able to find and fix your bugs faster using a symbolic debugger like GDB. However, this isn't to say that printf() has no use in debugging. Sometimes it's the best way to go. However, for real code, a debugger can almost always get the job done orders of magnitude faster and easier. And using a debugger is always more elegant, and if you don't care about elegance, you should quit programming on Linux and start using Visual C++.

## Chapter 1.2: What Is GDB?
In the previous section I told you what a symbolic debugger is. There are actually MANY symbolic debuggers, and in the next section I'll mention some of them. However, this tutorial is about one particular debugger which I use, called GDB.

GDB is a debugger which is part of the Free Software Foundation's GNU operating system. Its original author is Richard M. Stallman (affectionately known as "RMS", one of the finest heroes of the free software movement), and has a long and impressive list of contributors, including some interesting corporate sponsorship for support under various architectures. It's a wonderful piece of software and outclasses nearly every other debugger I've seen, including commercial ones.

GDB can be used to debug C, C++, Objective-C, Fortran, Java and Assembly programs. There's partial support for Modula-2 and Pascal. It'll run on any architecture you can think of that supports Unix, so learning GDB on your home PC will give you the power to debug code anywhere Unix can run!

Way back when, dbx was the canonical debugger people used on Unix systems. With the advent of GNU being the standard by which all Unix systems are measured, GDB became the canonical debugger of the debugging world. As a result, even commercial debuggers have a tendency to be command compatible (or even idea compatible) with GDB, so learning GDB will enable you to use a whole slew of other debuggers. In short, if you learn GDB, you will be able to debug anything almost anywhere with any debugger in the Unix world.

GDB's homepage is located at www.gnu.org/software/gdb/gdb.htmland as of Nov 2006, is up to version 6.4.

GDB is copyleft software (meaning that not only is GDB free software, but all publicly released derivatives and enhancements people make to GDB must also be free) and is licensed under the GNU GPL

## Chapter 1.3: Other Symbolic Debuggers
This section documents other debuggers, both actively developed and long gone. I give a short history when the information is available. Any additions (history, debuggers not listed here, other front ends, screenshots), please let me know.

### Chapter 1.3.0: Debuggers
* The first debugger that I know of was called dbx, and like GDB, was command line driven. The text UI of GDB was written to resemble dbx, although the two debuggers are not command line compatible. Other symbolic debuggers were written so that their UI resembled dbx (or GDB) as well. For this reason, you'll find many command line debuggers to be quite similar. If you learn to use GDB, you'll largely be able to navigate through most other debuggers.
* ups is another debugger originally developed by Mark Russell but is now updated by Rod Armstrong. It also comes with its own theme song. Ups includes a C interpreter which allows you to add fragments of code simply by editing them into the source window (the source file itself is not modified). Perversely, this lets you add debugging printf() calls without recompiling, relinking or even restarting the target program. ups supports C, C++ and limited FORTRAN debugging on SunOS, Solaris, Linux and FreeBSD.
* The Portland Group sells an excellent high-quality GUI debugger named pgdbg. pgdbg specializes in debugging all kinds of parallel code on many different kinds of clusters (distributed memory, SMP servers, etc). While pgdb is a very high-powered debugger, it's also expensive.

### Chapter 1.3.1: Front Ends
* xwpe.
* The most popular GDB front end is DDD, the Data Display Debugger which uses the Motif widget set. DDD has some nice features: it can give you graphical representations of linked lists, ADT's and trees. In addition, DDD is a front end to the Python, Java and Perl debuggers as well. I personally don't use DDD much, but I still appreciate it. DDD used to be quite buggy. Over the years it has stopped crashing regularly(!) on me, but as of March 2003, still crashes on a blue moon. In addition, the pop-up command tool definitely has "issues" with window managers that have multiple screens, like Enlightenment.
* tgdb is a Tcl/Tk front end for GDB first written in 1994 by a company named HighTec EDV-Systeme GmbH, in Germany. It was shareware (asking price was $30). Development and support seems to have ended many years ago. It shouldn't be confused with "trivial gdb" which is also called tgdb.
* xdbx is a front end to dbx that was created by Po Cheung of Microelectronics and Computer Technology Corporation (MCC) in March 10, 1989. It uses the old X Athena widget set (libxaw). It has its own license which is open source but not copyleft. Development died a long, long time ago.
* xxgdb is a front end to GDB that was created in December 1990 by Pierre Willard. It has its own license which is open source but not copyleft. It's built from the source code for xdbx; basically, xxgdb is xdbx adapted to GDB instead of dbx. xxgdb uses the old X Athena widget set (libxaw). It currently doesn't run on any system that uses unix98 posix TTYs. Development died in 2002. It most likely doesn't work with current versions of GDB.
* mxgdb is a Motif based front end for GDB written by Jim Tsillas back in January 3 1992. mxgdb is based on xxgdb: Jim ported xxgdb from the Athena widget set to the Motif widget set (in turn, xxgdb was a GDB port of xdbx). It's licensed under the GNU GPL and was last maintained (I think) by Robert Stockmann. It most likely doesn't work with current GDB versions.

# Chapter 2: Memory Layout and the Stack
## Chapter 2.0: Where Are We Going To Go?
To effectively learn how to use GDB, you must understand frames, which are also called stack frames because they're the frames that comprise the stack. To learn about the stack, we need to learn about the memory layout of an executing program. The discussion will mainly be theoretical, but to keep things interesting we'll conclude the chapter with an example of the stack and stack frames using GDB.

The material learned in this chapter may seem rather theoretical, but it does serve a few very useful purposes:

* Understanding the stack is absolutely necessary for using a symbolic debugger like GDB.
* Knowing the memory layout of a process will help us understand what exactly a segmentation fault (or segfault) is, and why they happen (or sometimes, more importantly) don't happen when they should. In brief, segfaults are the most common immediate cause for a program to bomb.
* A knowledge of a program's memory space can often allow us to figure out the location of well-hidden bugs without the use of print() statements, a compiler or even GDB! In the next section, which is a guest written piece by one my friends, Mark Kim, we'll see some real Sherlock Holmes style sleuthing. Mark homes in on a well hidden bug in somewhat lengthy code. It only took him about 5 or 10 minutes, and all he did was look at the program and use his knowledge of how a program's memory space works. It's really impressive!

So without futher ado, let's take a look at how programs are laid out in memory.

## Chapter 2.1: Virtual Memory
Whenever a process is created, the kernel provides a chunk of physical memory which can be located anywhere at all. However, through the magic of virtual memory (VM), the process believes it has all the memory on the computer. You might have heard "virtual memory" in the context of using hard drive space as memory when RAM runs out. That's called virtual memory too, but is largely unrelated to what we're talking about. The VM we're concerned with consists of the following principles:

* Each process is given physical memory called the process's virtual memory space.
* A process is unaware of the details of its physical memory (i.e. where it physically resides). All the process knows is how big the chunk is and that its chunk begins at address 0.
* Each process is unaware of any other chunks of VM belonging to other processes.
* Even if the process did know about other chunks of VM, it's physically prevented from accessing that memory.

Each time a process wants to read or write to memory, its request must be translated from a VM address to a physical memory address. Conversely, when the kernel needs to access the VM of a process, it must translate a physical memory address into a VM address. There are two major issues with this:
* Computers constantly access memory, so translations are very common; they must be lighting fast.
* How can the OS ensure that a process doesn't trample on another process's VM?

The answer to both questions lies in the fact that the OS doesn't manage VM by itself; it gets help from the CPU. Many CPUs contain a device called an MMU: a memory management unit. The MMU and the OS are jointly responsible for managing VM, translating between virtual and physical addresses, enforcing permissions on which processes are allowed to access which memory locations, and enforcing read/write permissions on sections of a VM space, even for the process that owns that space.

It used to be the case that Linux could only be ported to architectures that had an MMU (so Linux wouldn't run on, say, an x286). However, in 1998, Linux was ported to the 68000 which had no MMU. This paved the way for embedded Linux and Linux on devices such as the Palm Pilot.

#### Exercises

* Read a short Wikipedia blurb on the MMU
* Optional: Read the Wikipedia article on Virtual Memory


## Chapter 2.2: Memory Layout
## Chapter 2.3: Stack Frames And The Stack
## Chapter 2.4: The Symbol Table
## Chapter 2.5: Preparing An Executable For Debugging
## Chapter 2.6: Investigating The Stack With GDB

# Interlude: Debugging With Your Brian
# Chapter 3: Initialization, Listing And Running
# Chapter 4: Breakpoints and Watchpoints
# Chapter 5: Stepping and Resuming
# Chapter 6: Debugging A Running Process
# Chapter 7: Debugging Ncurses Programs
# Chapter 8: Other Stuf
