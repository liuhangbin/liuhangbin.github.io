---
layout: post
title: interview prepare
date: 2014-11-10
description:
category:
Tags: []
---

1. links:
https://github.com/torvalds/linux/commits/master?author=liuhangbin@gmail.com
https://github.com/kernelslacker/trinity/commits/master?author=liuhangbin@gmail.com
https://github.com/borkmann/lksctp-tools/commits/master?author=liuhangbin@gmail.com

1. pci_register_driver(&e100_driver)
    alloc_etherdev()	// alloc dev memory
    register_netdev()

1. receive message:
    1. netif_rx_schedule -> napi_schedule -> netif_recive_skb

1. Send messag:
    1. dev_queue_xmit -> qdisc_run -> hard_start_xmit
    1. hard_start_xmit()

1. Diff NAPI/Net Poll
    1. NAPI: stop interrupt and start poll mode, increase performance for
    device which will receive huge of packages per seconds.
    1. Netpoll: let kernel send package with unintact network, e.g. netconsole

1. Locks
    1. spin lock: used for multi processors, and can only be hold by one
    kernel task, incase multi processor visit critical Section. should only be
    uses with little time. Not allowed sleep, can be used in interrupt context
    1. mutex: sleep lock, can only be used in Process Context, could not be
    used in interrupt context.
    1. RCU(Read-Copy Update): read much and write little, like route query
    1. rwlock

1. memory management:
    1. First 1M physical memory store BIOS info, linux start from 1M
    1. Segmentation
    1. Page: 4K/per page
    1. X86_64: 0-3G user space, 3-4G kernel space
      1. kernel space: 0-896M, lower memory, > 896M, High memory
      1. the last 128K for page mapping

1. malloc:
    1. malloc: for user space
    1. kmalloc: for kernel, but with consecutive memory, fast
    1. vmalloc: for kernel, but may not onsecutive, slow

1. block, char device
    1. char device only be called like stream.
    1. block device can be read from anyware.

1. show driver print info, module info, and interrupt info
    1. dmesg, lsmod, cat /proc/interrupt

1. copy_to_user/copy_from_user:
    1. check whether user space pointer is valid
    1. incase of do_page_fault

1. register char device
    1. cdev_init
    1. register_chrdev()
    1. mknod to create a device under /dev

1. DMA, interrupt
    1. DMA: no need cpu, copy date from kernel to device directly
    1. interrupt: need cpu, when have IRQ, cpu stop current job and deal with IRQ
