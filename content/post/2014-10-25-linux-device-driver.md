---
layout: post
title: linux device driver
date: 2014-10-25
description:
category:
Tags: []
---

### kernel modules

1. Linux kernel Licence
  1. GPLv2
1. kernel subsystem
  1. Process Managerment
  1. Memory Managermanet
  1. Filesystems
  1. Device control
  1. Networking
1. Classes of device and modules
  1. char module
    1. Character Device
      1. /dev/console, /dev/ttyS0
      1. systemcall: open, close, read, write
      1. diff with regular file
        1. you can move back and forth in the regular file
	1. you can only access sequentially in char device
  1. block module
    1. Block Device
      1. disk
      1. handle I/O operations
  1. network module
    1. Network interfaces
      1. NIC, loopback

1. Basic module
```c
#include <linux/init.h>
#include <linux/module.h>
MODULE_LICENSE("GPL v2");

static int hello_init(void)
{
	printk(KERN_ALERT "Hello, world\n");
	return 0;
}

static void hello_exit(void)
{
	printk(KERN_ALERT "Goodby\n");
}

module_init(hello_init);
module_exit(hello_exit);
```
```bash
$ cat Makefile
obj-m += hello.o
all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```

If you have multi file, then would be
```
obj-m := module.o
module-objs := file1.o file2.o
```

1. parameters
```
#include <linux/moduleparam.h>
module_param(variable, type, perm);

static char *whom = "world";
static int howmany = 1;
module_param(howmany, int, S_IRUGO);
module_param(whom, charp, S_IRUGO);
```

1. lsmod
  1. read /proc/modules
  1. info about modules /sys/modules


1. kernel symbol tables
  1. export by: EXPORT_SYMBOL("name"); EPORT_SYSBOL_GPL("name");
  1. /proc/kallsyms


### Char device

1. dev_t type
  1. defined in <linux/types.h>
  1. 12 bits set aside for the major number and 20 for the minor number.
  1. MAJOR(dev_t dev); / MINOR(dev_t dev); / MKDEV(int major, int minor);

1. register_chrdev_region
  1. <linux/fs.h>
  1. know which major numbers
    1. `int register_chrdev_region(dev_t first, unsigned int count, char *name);`
  1. Do not know which major numbers
    1. `int alloc_chrdev_region(dev_t *dev, unsigned int firstminor,
    unsigned int count, char *name);`
  1. `void unregister_chrdev_region(dev_t first, unsigned int count);`
  1. use alloc_chrdev_region instead of register_chrdev_region

1. Data Structures
  1. sturct file_operations
  1. struct file
    1. <linux/fs.h>
  1. struct inode
    1. unsigned int iminor(struct inode *inode);
    1. unsigned int imajor(struct inode *inode);

1. Char Device Registration
  1. <linux/cdev.h>
  1. void cdev_init(struct cdev *cdev, struct file_operations *fops);
  1. int cdev_add(struct cdev *dev, dev_t num, unsigned int count);
  1. void cdev_del(struct cdev *dev);

1. Memory Usage
  1. <linux/slab.h>
  1. void *kmalloc(size_t size, int flags); (ref: vmalloc)
    1. vmalloc: allocates a contiguous memory region in the virtual address space.
      returns a pointer to a linear memory area of size at least size .
  1. void kfree(void *ptr);

1. read/write
  1. ssize_t read(struct file *filp, char __user *buff,
  size_t count, loff_t *offp);
  1. ssize_t write(struct file *filp, const char __user *buff,
  size_t count, loff_t *offp);
  1. <asm/uaccess.h>
    1. unsigned long copy_to_user(void __user *to,
 	 const void *from,
	  unsigned long count);
    1. unsigned long copy_from_user(void *to,
	  const void __user *from,
	  unsigned long count);
1. Lock, mutual exclusion
  1. Semaphore
    1. mutex
    1. read/write semaphore
  1. Spinlocks
    1. used in code that can not sleep, such as interrupt handlers.
    1. Reader/Writer Spinlocks
  1. seqlocks
  1. Read-Copy-Update(RCU)

### Memory

1. Memory Zone
  1. DMA-capable memory, first 16MB of RAM
  1. normal memory, user space? 0-3G
  1. low memory, kernel physic address, 0-896M, other 128M are left for I/O dev
  1. high memory, physic memory that kernel cant mapped directly

### Network Driver

1. struct net_device
  1. <linux/netdevice.h>
  1. struct net_device *alloc_netdev(int sizeof_priv,
    const char *name,
    void (*setup)(struct net_device *));
  1. struct net_device *alloc_etherdev(int sizeof_priv);
    1. <linux/etherdevice.h>
  1. register_netdev() / unregister_netdev() / free_netdev()
  1. void netif_start_queue(struct net_device *dev);
  1. void netif_stop_queue(struct net_device *dev);
  1. hard_start_transmit()
  1. ether_setup()
    1. driver/net/net_init.c
