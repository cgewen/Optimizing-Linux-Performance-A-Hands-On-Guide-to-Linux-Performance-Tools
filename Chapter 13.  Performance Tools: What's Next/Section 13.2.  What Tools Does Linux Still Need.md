### 13.2\. What Tools Does Linux Still Need?

As we toured some of the Linux performance tools, we saw some holes in the overall performance-investigation functionality. Some of these holes are the result of kernel limitations, and some exist just because no one has written a tool to solve the problem. However, filling some of these holes would make it dramatically easier to track down and fix Linux performance problems.

<a name="ch13lev2sec1"></a>

#### 13.2.1\. Hole 1: Performance Statistics Are Scattered

One glaring hole <a name="iddle2836"></a>is that Linux has no single tool that provides all relevant performance statistics for a particular process. <tt>ps</tt> <a name="iddle2837"></a>was meant to fill this hole in the original UNIX, and on Linux, it is pretty good but it does not cover all the statistics that other commercial UNIX implementations provide. Some statistics are invaluable in tracking down performance problems—for example, <tt>inblk</tt> (I/O blocks read in) <a name="iddle2838"></a>and <tt>oublk</tt> (I/O blocks written out), <a name="iddle2839"></a>which indicate the amount of disk I/O a process is using; <tt>vcsw</tt> (voluntary context switches) <a name="iddle2840"></a>and <tt>invcsw</tt> (involuntary context switches), <a name="iddle2841"></a>which often indicate a process was context-switched off the CPU; <tt>msgrcv</tt> (messages received on pipes and sockets) <a name="iddle2842"></a>and <tt>msgsnd</tt> (messages sent on pipes and sockets), <a name="iddle2843"></a>which show the amount of network and pipe I/O an application is using. An ideal tool would add all these statistics and combine the functionality of many performance tools presented so far (including <tt>oprofile</tt>, <tt>top</tt>, <tt>ps</tt>, <tt>strace</tt>, <tt>ltrace</tt>, and the <tt>/proc</tt> file system) into a single application. A user should be able point this single application at a process and extract all the important performance statistics. Each statistic would be updated in real time, enabling a user to debug an application as it runs. It would group statistics for a single area of investigation in the same location.

For example, if I were investigating memory usage, it would show exactly how memory was being used in the heap, in the stack, by libraries, shared memory, and in <tt>mmap</tt>. If a particular memory area was much higher than I expected, I could drill down, and this performance tool would show me exactly which functions allocated the memory. If I were investigating CPU usage, I would start with overall statistics, such as how much time is spent in system time versus user time, and how many system calls a particular process is making, but then I would be able to drill down into either the system or user time and see exactly which functions are spending all the time and how often they are being called. A smart shell script that used the appropriate preexisting tools to gather and combine this information would go a long way to achieving some of this functionality, but fundamental changes in the behavior of some of the tools would be necessary to completely realize this <a name="iddle2844"></a>vision.

<a name="ch13lev2sec2"></a>

#### 13.2.2\. Hole 2: No Reliable and Complete Call Tree

The next performance <a name="iddle2845"></a><a name="iddle2846"></a>tool hole is the fact that there is currently no way to provide a complete call tree of a program's execution. Linux has several incomplete implementations. <tt>oprofile</tt> <a name="iddle2847"></a>provides call-tree generation, but it is based on sampling, so it will not catch every call that is made. <tt>gprof</tt> <a name="iddle2848"></a>supports call trees, but it will not be able to profile the full application unless every library that a particular process calls is also complied with profile support. This most promising tool, <tt>valgrind</tt>, <a name="iddle2849"></a>has a skin called <tt>calltree</tt>, described in the section, "[5.2.5](ch05lev1sec2.html#ch05lev2sec5) [kcachegrind](ch05lev1sec2.html#ch05lev2sec5)," in [Chapter 5](ch05.html#ch05), "Performance Tools: Process-Specific Memory," which has a goal of providing a completely accurate call tree. However, it is still in development and does not work on all binaries.

This call-tree tool would be useful even if it dramatically slowed down application performance as it runs. A common way of using this would be to run <tt>oprofile</tt> to figure out which functions in an application are "hot," and then run the call-tree program to figure out why the application called them. The <tt>oprofile</tt> <a name="iddle2850"></a>step would provide an accurate view of the application's bottlenecks when it runs at full speed, and the call tree, even if it runs slowly, would show how and why the application called those functions. The only problem would be if the program's behavior was timing sensitive and it would change if it was run slowly (for example, something that relied on network or disk I/O). However, many problems exist that are not timing sensitive, and an accurate call-tree mechanism would go a long way to fixing <a name="iddle2851"></a><a name="iddle2852"></a>these.

<a name="ch13lev2sec3"></a>

#### 13.2.3\. Hole 3: I/O Attribution

The final and <a name="iddle2853"></a><a name="iddle2854"></a><a name="iddle2855"></a>biggest hole in Linux right now is that of I/O attribution. Right now, Linux does not provide a good way to track down which applications are using the highest amounts of disk or even network I/O.

An ideal tool would show, in real time, the amount of input and output bytes of disk and network I/O that a particular process is using. The tool would show the statistics as raw bandwidth, as well as a percentage of the raw I/O that the subsystem is capable of. In addition, users would also be able to split up the statistics, so that they could see the same statistics for each individual network and disk <a name="iddle2856"></a><a name="iddle2857"></a><a name="iddle2858"></a>device.