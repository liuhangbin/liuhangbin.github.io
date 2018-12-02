---
layout: post
Title: linux console
Date: 2013-08-28
tags: [linux, console]
---

5 Linux / Unix Commands For Connecting To The Serial Console


Find out information about your serial ports
---
```
$ dmesg | grep tty
[    0.000000] console [tty0] enabled
[    1.409184] 00:06: ttyS0 at I/O 0x3f8 (irq = 4) is a 16550A
```

#1 cu command
---
The cu command is used to call up another system and act as a dial in
terminal. cu command is installed on many Unix like systems including
OpenBSD/AIX/Solaris and so on. You can use it as follows:

`cu -l /dev/device -s baud-rate-speed`

In this example, Im using /dev/ttyS0 with 9600 baud-rate:

`cu -l /dev/ttyS0 -s 9600`

To exit enter tilde dot (~.).


#2 screen command
---
Use the screen command as follows:
```
$ screen /dev/device baud-rate
$ screen /dev/ttyS0 9600
$ screen /dev/ttySUSB0 9600,cs8
```


#3 minicom command
---
minicom command is a communication program which somewhat resembles the
shareware program TELIX from old good MS-DOS days. Type minicom to connect:

`$ minicom`

See [how to install and use minicom][http://www.cyberciti.biz/tips/connect-soekris-single-board-computer-using-minicom.html] under Linux operating systems.


#4 putty command
---
PuTTY is a free and open source gui X based terminal emulator client for the
SSH, Telnet, rlogin, and raw TCP computing protocols and as a serial console
client. It works under Linux, Unix, Mac OS X, MS-Windows and few other
operating systems. See [how to configure and use putty for cisco routers][http://www.cyberciti.biz/tips/replace-windows-vista-hyperterminal-with-putty.html]:


#5 tip command
---
The tip command is used as a serial terminal. tip command can be found under
AIX/HP-UX/Solairs/*BSD/Linux operating systems. The syntax is:

```
tip -9600 device
tip -9600 s0
tip -9600 ttys0
```


Reference:
http://www.cyberciti.biz/hardware/5-linux-unix-commands-for-connecting-to-the-serial-console/
