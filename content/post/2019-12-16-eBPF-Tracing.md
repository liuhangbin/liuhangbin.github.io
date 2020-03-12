---
title: eBPF Tracing
date: 2019-12-16
---

## Get start with eBPF Tracing


TODO: learn how to use bcc/trace and bpftrace

### bcc

1. install bcc on RHEL
   `dnf install -y bcc-tools`
1. The tools list under `/usr/share/bcc/tools/`, go through the
   [tutorial](https://github.com/iovisor/bcc/blob/master/docs/tutorial.md)
   to familiar each tools.
   1. Some tools that may interested, like bpflist, tcp*, memleak
   1. *funccount* command traces functions, tracepoints, or USDT probes that match a
      specified pattern, e.g. funccount 'tcp_*'
   1. *trace* command probes functions you specify and displays trace
      messages, e.g. trace 'do_sys_open "%s", arg2',
      trace -I 'net/sock.h' 'udpv6_sendmsg(struct sock *sk) (sk->sk_dport == 13568)'

1. How to develope a new tool, seel [developer tutorial](https://github.com/iovisor/bcc/blob/master/docs/tutorial_bcc_python_developer.md)

### bpftrace
1. install bpftrace on RHEL
   `dnf install -y bpftrace`

1. The tools list under `/usr/share/bpftrace/tools/`
   1. Some tools that may interested, like gethostlatency.bt, tcp*,

1. [bpftrace Tutorial](https://github.com/iovisor/bpftrace/blob/master/docs/tutorial_one_liners.md)
1. [bpftrace networking example](https://lwn.net/Articles/793749/)

### How to deal with the errors

Q: bcc: "Unable to find kernel headers. Try rebuilding kernel with CONFIG_IKHEADERS=m (module)"
A: you need to install kernel-devel package if you use RHEL8

Q: bpftrace got errors like "'net/sock.h' file not found".
A: you need to install kernel-devel package if you use RHEL8

Q: bcc: # ./trace 'sys_execve "%s", arg1'
cannot attach kprobe, probe entry may not exist
Failed to attach BPF program b'probe_sys_execve_1' to kprobe b'sys_execve'
A: Check '/proc/kallsyms' and see if it contains the probe you want to
attache, in this case it should be '__x64_sys_execve' on x86_64 system.

## Reference
[](http://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html)
[](http://www.brendangregg.com/ebpf.html)
