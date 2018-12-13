---
layout: post
title: packetdrill
date: 2015-03-02
description:
category:
Tags: []
---

Install:
---
1. yum install flex bison glibc-static
2. git clone https://code.google.com/p/packetdrill/
   or https://github.com/borkmann/packetdrill.git
   or https://github.com/gdetal/packetdrill_mptcp.git
3. ./configure && make

issues:
1. /usr/bin/ld: cannot find -lpthread
    packetdrill use -static when compile, so we need libxxx.a instead of
    libxxx.so, after install glibc-static, this issue resolved.

Usage:
---
./packetdrill test.pkt
test.pkt为按Packetdrill语法编写的测试脚本。
成功：无输出，表示脚本正确，一切都符合预期。
失败：指出脚本的错误地方，以及原因。

Syntax:
---
The tool supports four types of statements: packets, system calls, shell
commands, and Python scripts.
Each statement is timestamped and is executed by the interpreter in real time,
verifying that events proceed as the script expects.
脚本中可以包含四种语句：数据包、系统调用、shell命令、python语句。
每条语句都必须以时间戳开头，指明它的执行时间。

(1) Packets
数据包分为：输入的数据包、输出的数据包，格式类似于tcpdump的，
支持TCP、UDP、ICMP，以及TCP的大部分选项。

输入的数据包(input packets)
对于输入的数据包（<表示输入），packetdrill会构造一个真实的数据包，然后注入协议栈。
< denotes an input packet to construct and inject into the system under test.
Here's an example of a TCP SYN packet, which packetdrill creates and injects into the
network stack under test 100ms after the start of the test：
0.100 < S 0:0(0) win 32792 <mss 1000, nop, nop, sackOK, nop, wscale 6>

输出的数据包(outbound packets)
对于输出的数据包（>表示输出），packetdrill会检查协议栈是不是真的发出了这样一个包。
> denotes an output packet to sniff and verify, to expect the system to send.
Here's an example of an outbound UDP packet expected to be sent immediately after
a prior event(denoted by +0), which packetdrill sniffs and then verifies for matching
specification:
+0 > udp (1472)

(2) System Calls
系统调用的格式类似于strace。
对于每个系统调用，packetdrill会在指定的时间给予执行，并检查返回值是否和预期的一样。
Here's an example of a bind() system call invocation in packetdrill notation:
+0 bind(3, ..., ...) = 0
In this example, 3 denotes the file descriptor number to pass in, and the = 0 denotes the expected
return value (i.e.., the user expects the system call to succeed).
The ellipsis (...) allows scripts to omit irrelevant details.

(3) Shell Commands
允许在脚本中使用shell命令，用反引号括起来。
+0 `sysctl -q net.ipv4.tcp_timestamps=0`

(4) Python Commands
允许在脚本中使用Python命令，用%{和}%括起来。
Packetdrill allows inline Python code snippets to print information and to make assertions about the
internal state of a TCP socket using the TCP_INFO getsockopt() option.
The following Linux-based example asserts that the sender's congestion window is 10 packets:
+0 %{ assert tcpi_snd_cwnd == 10 }%

(5) 时间戳
每条语句都必须以时间戳开头，指明它的执行时间，或者预期事件的发生时间。
时间戳可以使用多种格式：
Absolute（绝对时间）：0.75
Relative（相对时间)：+0.2
Wildcard（任意时间)：*
Range（绝对时间区间)：0.750~0.900
Relative Range（相对时间区间）：+0.1~+0.2
Loose（允许误差值）：--tolerance_usecs=800
Blocking（阻塞时间区间）：0.750...0.900

如果在规定的时间戳，对应的事件并没有发生就会报错，并告知该事件的实际发生时间。
+1.0 > S. 0:0(0) ack 1 <mss 1460,nop,nop,sackOK,nop,wscale 6>
预期在1s以后TCP应该发送一个SYNACK包。
在实际的使用中，一般指定--tolerance_usecs=405000，也就是允许4ms的时间误差。


(6) 完整例子
验证TCP的快速重传功能，fast retransmit说白了就是收到3个重复的ACK或SACK后马上重传一个数据包
（对于FACK来说只要孔>=3个包即可）。
脚本中服务器端的协议栈是要观测的对象，对应的是输出的数据包(outbound packet)。
脚本中客户端对应的是输入的数据包(inbound packet)，用于注入协议栈。
完整例子如下：
```
// Establish a connection. 服务端socket函数调用
0 socket(..., SOCK_STREAM, IPPROTO_TCP) = 3
+0 setsockopt(3, SOL_SOCKET, SO_REUSEADDR, [1], 4) = 0
+0 bind(3, ..., ...) = 0
+0 listen(3, 1) = 0

// 客户端的socket函数调用不用显式指出
// 客户端构造SYN包，注入协议栈
+0 < S 0:0(0) win 32792 <mss 1000, sackOK, nop, nop, nop, wscale 7>

// 预期协议栈发送SYNACK包
+0 > S. 0:0(0) ack 1 <...>

// 客户端构造ACK包，注入协议栈，完成三次握手
+.1 < . 1:1(0) ack 1 win 257

// 服务端接受连接
+0 accept(3, ..., ...) = 4

// Send 1 data segment and get an ACK，构造收发包场景
+0 write(4, ..., 1000) = 1000
+0 > P. 1:1001(1000) ack 1
+.1 < . 1:1(0) ack 1001 win 257
+0 %{ print tcpi_snd_cwnd }%

// Write 4 data segments
+0 write(4, ..., 4000) = 4000
+0 > P. 1001:5001(4000) ack 1

// Get 3 SACKs，构造快速重传场景
+.1 < . 1:1(0) ack 1001 win 257 <sack 2001:3001, nop, nop>
+0 < . 1:1(0) ack 1001 win 257 <sack 2001:4001, nop, nop>
+0 < . 1:1(0) ack 1001 win 257 <sack 2001:5001, nop, nop>

// We've received 3 duplicate ACKs, so we do a fast retransmit.
// 收到3个SACK后，预期协议栈会快速重传
+0 > . 10001:2001(1000) ack 1

// Receiver ACKs all data.
+.1 < . 1:1(0) ack 6001 win 257
```

实现
---
Packetdrill是一个用户态应用程序，主要用C语言编写。
使用flex构造词法分析器，使用bison构造语法分析器。
脚本解释器包括一个主线程和一个用于执行阻塞的系统调用的线程。
使用packet socket来验证输出的数据包，使用TUN device来注入输入的数据包。
具体代码可见项目。

Notes:
偶尔有timing error，是正常现象，可用--tolerance_usecs=405000指定允许的时间误差。


Reference:
---
Mail List: https://groups.google.com/forum/#!forum/packetdrill
[1] packetdrill: Scriptable Network Stack Testing, from Sockets to Packets
[2] Drilling Network Stacks with packetdrill
https://www.usenix.org/system/files/login/articles/10_cardwell-online.pdf
https://www.usenix.org/conference/atc13/technical-sessions/presentation/cardwell
