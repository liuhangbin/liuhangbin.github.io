---
title: debug tips
date: 2021-07-13
---


#### Q: What's the code i40e_xmit_frame_ring+0x5667/0x5d50 related

A: Please install debuginfo for the kernel that was running and provide the following output
```
# gdb /usr/lib/debug/usr/lib/modules/<yourkernel>/kernel/drivers/net/ethernet/intel/i40e/i40e.ko.debug
(gdb) list *i40e_xmit_frame_ring+0x5667
```

But for buildin functions which don't have .ko.debug file, just use vmlinux
file, e.g.
```
# gdb /usr/lib/debug/lib/modules/<yourkernel>/vmlinux
(gdb) list *icmp_rcv+0x193/0x3b0
```

#### Q: how to print pr_debug info
A: Usually you should able to print it when `DEBUG` defined. just
`echo 8 > /proc/sys/kernel/printk` should be enough.
See [printk](https://www.kernel.org/doc/html/v5.13/core-api/printk-basics.html) for more info.

#### Q: Dynamic debug
A: When your kernel has config "CONFIG_DYNAMIC_DEBUG=y", `pr_debug` will not print anything by default. You need to use `/sys/kernel/debug/dynamic_debug/control` to control the `pr_debug` info.

`cat /sys/kernel/debug/dynamic_debug/control` could show all the dynamic debug lines. The control format looks like `"file:line [module]function flags format"` e.g.

1. Open file's dynamic debug
```
echo 'file net/ipv4/ping.c +p' > /sys/kernel/debug/dynamic_debug/control
```

  * "+" add the given flags
  * "-" remove the given flags
  * "=" set the flags to the given flags
  * p    enables the pr_debug() callsite.
  * f    Include the function name in the printed message
  * l    Include line number in the printed message
  * m    Include module name in the printed message
  * t    Include thread ID in messages not generated from interrupt context
  * _    No flags are set. (Or'd with others on input)

1. Enable module's dynamic debuginfo
```
echo 'module bonding +p' > /sys/kernel/debug/dynamic_debug/control
```

1. Use line number
```
/* enable line 12 debug */
echo 'file kxtj2 line 12 +p' > /sys/kernel/debug/dynamic_debug/control
echo 'module akm line 12 +p' > /sys/kernel/debug/dynamic_debug/control
/* enable line 12-230 debug */
echo 'file kxtj2 line 12-230 +p' > /sys/kernel/debug/dynamic_debug/control
echo 'module akm line 12-230 +p' > /sys/kernel/debug/dynamic_debug/control
/* enable line 12 to the end */
echo 'file kxtj2 line 12- +p' > /sys/kernel/debug/dynamic_debug/control
echo 'module akm line 12- +p' > /sys/kernel/debug/dynamic_debug/control
/* starts from begining to line 230 */
echo 'file kxtj2 line -230 +p' > /sys/kernel/debug/dynamic_debug/control
echo 'module akm line -230 +p' > /sys/kernel/debug/dynamic_debug/control
```

1. enable function debugs
```
echo 'file kxtj2 func kxtj2_probe +p' > /sys/kernel/debug/dynamic_debug/control
echo 'module akm func init +p' > /sys/kernel/debug/dynamic_debug/control
```

For more usage info, see [Dynamic debug](https://www.kernel.org/doc/html/v4.11/admin-guide/dynamic-debug-howto.html).
