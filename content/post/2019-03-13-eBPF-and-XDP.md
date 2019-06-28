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

### XDP programing

Install kernel-devel to include the kernel headers

Reference: [xdp intro](https://developers.redhat.com/blog/2018/12/06/achieving-high-performance-low-latency-networking-with-xdp-part-1/)
[xdp_code](https://github.com/pabeni/xdp_walkthrough_examples)
Use [xdp_dummy.c](https://git.kernel.org/pub/scm/linux/kernel/git/davem/net.git/tree/tools/testing/selftests/bpf/progs/xdp_dummy.c)
as example. The first include line, provided by the kernel-heade package of
most distributions. The second header, which still is part of the Linux kernel but is not usually packaged by most distributions, , contains a list of the available eBPF helpers and
the definition of the SEC() macro. The current solution is, unfortunately, to download the full Linux sources, unpack them somewhere on your local disc.

And we need a Makefile like

```
#KDIR ?= /lib/modules/$(shell uname -r)/source
KDIR ?= /home/net
CLANG ?= clang
LLC ?= llc
ARCH := $(subst x86_64,x86,$(shell arch))

BIN := xdp_dummy.o
CLANG_FLAGS = -I. -I$(KDIR)/arch/$(ARCH)/include \
	-I$(KDIR)/arch/$(ARCH)/include/generated \
	-I$(KDIR)/include \
	-I$(KDIR)/arch/$(ARCH)/include/uapi \
	-I$(KDIR)/arch/$(ARCH)/include/generated/uapi \
	-I$(KDIR)/include/uapi \
	-I$(KDIR)/include/generated/uapi \
	-D__KERNEL__ -D__BPF_TRACING__ -Wno-unused-value -Wno-pointer-sign \
	-D__TARGET_ARCH_$(ARCH) -Wno-compare-distinct-pointer-types \
	-I$(KDIR)/tools/testing/selftests/bpf/ \
	-Wno-gnu-variable-sized-type-not-at-end \
	-Wno-address-of-packed-member -Wno-tautological-compare \
	-Wno-unknown-warning-option \
	-O2 -target bpf -emit-llvm
#	-include $(KDIR)/include/linux/kconfig.h \

all: $(BIN)
xdp_dummy.o: xdp_dummy.c
	$(CLANG) $(CLANG_FLAGS) -c $< -o - | $(LLC) -march=bpf -mcpu=$(CPU) -filetype=obj -o $@

clean:
	rm -f *.o

```

Where the KDIR is the kernel source code path.

*Note*: If you got error like "Makefile:27:  missing separator.  Stop". Then check if the new separate line of Makefile has spaces instead of a tab after copy/paste.

