---
layout: post
title: systemtap
date: 2016-07-28
description:
category:
Tags: []
---

## install

```
# yum install -y systemtap
# yum install -y kernel-devel kernel-debuginfo
```

Need config debug repo, e.g.
[beaker-Workstation-debug]
name=beaker-Workstation-debug
baseurl=http://download.eng.pek2.redhat.com//pub/rhel/nightly/latest-RHEL-7/compose/Workstation/x86_64/debug/tree/
enabled=1
gpgcheck=0

## Probing points

syscall.read
	When read() system call
Syscall.close.return
	when returning from close() system call
module("floppy").function("*")
	when entering any function from floppy module
kernel.function("*@net/socket.c").return
	when returning from any function in socket.c
kernel.statement("*@kernel/sched.c:2917")
	when hitting line 2917 in sched.c
syscall.read
	When entering read() system call
Syscall.close.return
	when returning from close() system call
module("floppy").function("*")
	when entering any function from floppy module
kernel.function("*@net/socket.c").return
	when returning from any function in socket.c
kernel.statement("*@kernel/sched.c:2917")
	when hitting line 2917 in sched.c
timer.ms(200)
	every 200 milliseconds
process("/bin/ls").function("*")
	when entering any function in /bin/ls
kernel.function("*exit*").return
	when returning from any function which has exit in its name


## Usage

1. oneline test

stap -ve 'probe begin { log("hello systemtap!") exit() }'

1. Who is calling open
```
probe syscall.open
{
	printf("%s(%d) open(%s)\n", execname(), pid(), argstr)
}

```

1. SCTP listen backlog
```
probe module("sctp").function("sctp_listen_start")
{
	if ($backlog != 0)
		printf("The backlog is %d\n", $backlog);
}
```

1. Typecasting
```
probe kernel.function("xfrm_output")
{
	printf("The gso_size is %d\n", @cast($skb->head + $skb->end, "skb_shared_info", "kernel<include/linux/skbuff.h>")->gso_size)
}
```



## Reference
[] https://sourceware.org/systemtap/
[] http://www.cnblogs.com/hazir/p/systemtap_introduction.html

