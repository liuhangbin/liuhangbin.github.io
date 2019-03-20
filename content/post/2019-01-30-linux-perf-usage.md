---
title: linux perf usage
date: 2019-01-30
---

Brendan D. Gregg [perf examples](http://www.brendangregg.com/perf.html)

# Install

```
# yum install -y perf
```

TODO: How to build, Kernel build options

# Usage

## one line cmd

`statistics/count`: increment an integer counter on events

`sample`: collect details (eg, instruction pointer or stack) from a subset of events (once every ...)

`trace`: collect details from every event

### list event

List events: `perf list`

List specific tracepoint event:
```
# perf list "net:*"

List of pre-defined events (to be used in -e):

  net:napi_gro_frags_entry                           [Tracepoint event]
  net:napi_gro_receive_entry                         [Tracepoint event]
  net:net_dev_queue                                  [Tracepoint event]
  net:net_dev_xmit                                   [Tracepoint event]
  net:netif_receive_skb                              [Tracepoint event]
  net:netif_receive_skb_entry                        [Tracepoint event]
  net:netif_rx                                       [Tracepoint event]
  net:netif_rx_entry                                 [Tracepoint event]
  net:netif_rx_ni_entry                              [Tracepoint event]

# perf list "skb:*"

List of pre-defined events (to be used in -e):

  skb:consume_skb                                    [Tracepoint event]
  skb:kfree_skb                                      [Tracepoint event]
  skb:skb_copy_datagram_iovec                        [Tracepoint event]
```

### count event
Count net events for the entire system, for 10 seconds:
```
perf stat -e 'net:*' -a sleep 10
```
Count all net events, printing a report every second:
```
perf stat -e 'net:*' -a -I 1000
```

### Static Tracing

Trace new processes, until Ctrl-C:
```
perf record -e sched:sched_process_exec -a
```
Sample context-switches, until Ctrl-C:
```
perf record -e context-switches -a
```

### Dynamic Tracing

`Note` the functions need to be in /proc/kallsyms. If it's in a module, please load
the module first.

```
# Add a tracepoint for the kernel tcp_sendmsg() function entry ("--add" is optional):
perf probe --add tcp_sendmsg

# Remove the tcp_sendmsg() tracepoint (or use "--del"):
perf probe -d tcp_sendmsg

# Add a tracepoint for the kernel tcp_sendmsg() function return:
perf probe 'tcp_sendmsg%return'

# Show available variables for the kernel tcp_sendmsg() function (needs debuginfo):
perf probe -V tcp_sendmsg

# Show available variables for the kernel tcp_sendmsg() function, plus external vars (needs debuginfo):
perf probe -V tcp_sendmsg --externs

# Show available line probes for tcp_sendmsg() (needs debuginfo):
perf probe -L tcp_sendmsg

# Show available variables for tcp_sendmsg() at line number 81 (needs debuginfo):
perf probe -V tcp_sendmsg:81

# Trace previously created probe when the bytes (alias) variable is greater than 100:
perf record -e probe:tcp_sendmsg --filter 'bytes > 100'

# Add a tracepoint for tcp_sendmsg() return, and capture the return value:
perf probe 'tcp_sendmsg%return $retval'

# Add a tracepoint for tcp_sendmsg(), and "size" entry argument (reliable, but needs debuginfo):
perf probe 'tcp_sendmsg size'

# Add a tracepoint for tcp_sendmsg(), with size and socket state (needs debuginfo):
perf probe 'tcp_sendmsg size sk->__sk_common.skc_state'

# Trace previous probe when size is non-zero, and state is not TCP_ESTABLISHED(1) (needs debuginfo):
perf record -e probe:tcp_sendmsg --filter 'size > 0 && skc_state != 1' -a

# Add a tracepoint for tcp_sendmsg() line 81 with local variable seglen (needs debuginfo):
perf probe 'tcp_sendmsg:81 seglen'

# Add a tracepoint fro the team_port_disable() function entry in module team
perf probe -m team -a team_port_disable

# show the lines of a tracepoint in module team
```

### Usage example
if you only care the count number
```
# perf stat -e "probe:team_add_slave"
```
or the stack trace
```
# modprobe team
# perf probe -m team -a "team_add_slave"
# perf probe -m team -a "team_del_slave"
# perf record -e "probe:team_add_slave" -e "probe:team_del_slave" -ag
# perf report --stdio
# perf script
```
or a parameter in this function
```
# perf probe -m team -L team_port_enable
# perf probe -m team -V team_port_enable:15
# perf probe -m team -a "team_port_enable:15 team->en_port_count port->index"
# perf probe -m team -a "team_port_disable:15 team->en_port_count port->index"
# perf record -e "probe:team_port_disable" -e "probe:team_port_enable" -ag
# perf script
```

# Handle errors

When use dynamic tracing:
```
# perf probe --add tcp_sendmsg
# perf probe -V tcp_sendmsg
Failed to find the path for kernel: Invalid ELF file
  Error: Failed to show vars.
```

If you are using a kernel provided by vendor. e.g. RHEL/CentOS kernel, please
install `kernel-debuginfo-common` and `kernel-debuginfo package`.

If you build the kernel your self. With strace we could know perf is trying to
find the vmlinux under
```
open("vmlinux", O_RDONLY)               = -1 ENOENT (No such file or directory)
open("/boot/vmlinux", O_RDONLY)         = -1 ENOENT (No such file or directory)
open("/boot/vmlinux-4.20.0", O_RDONLY)  = -1 ENOENT (No such file or directory)
open("/usr/lib/debug/boot/vmlinux-4.20.0", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/lib/modules/4.20.0/build/vmlinux", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/lib/debug/lib/modules/4.20.0/vmlinux", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/lib/debug/boot/vmlinux-4.20.0.debug", O_RDONLY) = -1 ENOENT (No such file or directory)
```

So you should make sure vmlinux is under one of the directories. e.g. /boot/

if you got error like:
```
# perf probe -L tcp_sendmsg
Failed to find source file path.
  Error: Failed to show lines.

```

also from strace we could know, perf is seeking for the build dir.
```
access("/home/net/net/ipv4/tcp.c", R_OK) = -1 ENOENT (No such file or directory)
```

So, keep your build dir and do not remove or edit the files...
