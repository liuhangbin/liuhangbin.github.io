---
title: eBPF and XDP
date: 2019-03-13
---

### What is BPF
Reference: [bpf](https://docs.cilium.io/en/v1.4/bpf/)

The extended Berkeley Packet Filter (eBPF) has first appeared in Kernel 3.18.
The original version is being referred to "classic" BPF (cBPF) . cBPF is
known to many as being the packet filter language used by tcpdump.

Nowadays, the Linux kernel runs eBPF only and loaded cBPF bytecode is
transparently translated into an eBPF representation in the kernel before
program execution.

The eBPF networking specific use including loading BPF programs with tc (traffic
control) and XDP (eXpress Data Path).

### XDP/TC BPF

XDP BPF programs are attached at the earliest networking driver stage and
trigger a run of the BPF program upon packet reception. By definition, this
achieves the best possible packet processing performance since packets cannot
get processed at an even earlier point in software. However, since this
processing occurs so early in the networking stack, the stack has not yet
extracted metadata out of the packet. On the other hand, tc BPF programs are
executed later in the kernel stack, so they have access to more metadata and
core kernel functionality. Apart from tc and XDP programs, there are various
other kernel subsystems as well which use BPF such as tracing (kprobes,
uprobes, tracepoints, etc).
