---
title: tc/BPF and XDP/BPF
date: 2019-03-13
---

### What is eBPF
Reference: [bpf](https://cilium.readthedocs.io/en/latest/bpf/)

The extended Berkeley Packet Filter (eBPF) has first appeared in Kernel 3.18.
The original version is being referred to "classic" BPF (cBPF) . cBPF is
known to many as being the packet filter language used by tcpdump.

Nowadays, the Linux kernel runs eBPF only and loaded cBPF bytecode is
transparently translated into an eBPF representation in the kernel before
program execution.

The eBPF networking specific use including loading BPF programs with tc (traffic
control) and XDP (eXpress Data Path).

----
### Difference between tc/BPF and XDP/BPF

On a high-level there are three major differences when comparing XDP BPF programs to tc BPF ones:

  - `XDP hook is earlier, hence faster performance`. tc hook is later and hence has access to the sk_buff structure and fields. This is a significant contributor to the performance difference between the XDP and tc hooks. Access to sk_buff can be useful, but comes with an associated cost of the stack performing this allocation and metadata extraction, and handling the packet until it hits the tc hook. By definition, the xdp_buff doesn’t have access to sk_buff metadata and fields because the XDP hook is called before this work is done. 
  - `tc has better packet mangling capability`.  The BPF input context is a sk_buff for tc and not a xdp_buff for XDP.  Generally, the sk_buff is of a completely different nature than xdp_buff where both come with advantages and disadvantages. When the kernel’s networking stack receives a packet, after the XDP layer, it allocates a buffer and parses the packet to store metadata about the packet. This representation is known as the sk_buff. This structure is then exposed in the BPF input context so that tc BPF programs from the tc ingress layer can use the metadata that the stack extracts from the packet. With sk_buff, tc has the advantage that it is rather straightforward to mangle associated metadata. Therefore, BPF programs attached to the tc BPF hook can, for instance, read or write the skb’s mark, pkt_type, protocol, priority, queue_mapping, napi_id, cb[] array, hash, tc_classid or tc_index, vlan metadata, the XDP transferred custom metadata and various other information. All members of the struct __sk_buff BPF context used in tc BPF are defined in the linux/bpf.h system header. However, the xdp_buff case has the disadvantage that sk_buff metadata is not available for mangling at this stage. XDP has raw packet data and some some transferred custom metadata to play with. 
  - `XDP is better for complete packet rewrites`. The sk_buff case contains a lot of protocol specific information (e.g. GSO related state) which makes it difficult to simply switch protocols by solely rewriting the packet data. This is due to the stack processing the packet based on the metadata rather than having the cost of accessing the packet contents each time. Thus, additional conversion is required from BPF helper functions taking care that sk_buffinternals are properly converted as well. The xdp_buff case however does not face such issues since it comes at such an early stage where the kernel has not even allocated an sk_buff yet, thus packet rewrites of any kind can be realized trivially. However, the xdp_buff case has the disadvantage that sk_buff metadata is not available for mangling at this stage. 
  - `tc/ebpf and xdp as complementary programs`. If the usecase requires both packet rewrite and intricate mangling of data then the limitations of each program type can be overcome by operating complementary programs of both types.   XDP program at the ingress can rewite the complete packet  and pass custom metadata from XDP BPF to tc BPF, where tc can perform the packet mangling using the XDP metadata and sk_buff fields.
  - `tc/eBPF programs on ingress and egress, XDP is ingress only`Compared to XDP, tc BPF programs can be triggered out of ingress and also egress points in the networking data path as opposed to ingress only in the case of XDP.
  - `tc/BPF does not require HW driver changes, XDP typically uses native driver mode for best performance`
  - `Offloaded tc/ebpf and Offloaded XDP offer similar performance advantages`

On programing part:

 - XDP takes `struct xdp_md *ctx` as parameter, which points to the raw data.
   tc/ebpf takes `struct __sk_buff *skb`, as parameter, which could use more
   info supplied by __sk_buff.
---
### XDP programing

Here is a minimal example

```
#include <linux/bpf.h>

#ifndef __section
# define __section(NAME)                  \
	__attribute__((section(NAME), used))
#endif

__section("prog")
int xdp_drop(struct xdp_md *ctx)
{
	return XDP_DROP;
}

char __license[] __section("license") = "GPL";
```

Build the program and load xdp via ip link
```
# clang -O2 -g -Wall -target bpf -c xdp_mini.c -o xdp_mini.o
# ethtool -i enp3s0
driver: mlx4_en
version: 4.0-0
firmware-version: 2.40.5000
expansion-rom-version:
bus-info: 0000:03:00.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: yes
# ip link set enp3s0 up
# ip link set enp3s0 xdp obj xdp_mini.o verb
# bpftool prog show
26: xdp  tag 57cd311f2e27366b  gpl
        loaded_at 2019-09-18T02:32:50-0400  uid 0
        xlated 16B  jited 64B  memlock 4096B
# llvm-objdump -S -no-show-raw-insn xdp_mini.o
# llvm-objdump -h xdp_mini.o	// only show section headers

xdp_mini.o:     file format ELF64-BPF

Disassembly of section prog:
0000000000000000 xdp_drop:
; {
       0:       r0 = 1
; return XDP_DROP;
       1:       exit

# ip link set enp3s0 xdp off	// to remove the existing XDP program from the interface
```

---
### TC programing

Here is a tc-example.c example that can be loaded with tc and attached to a
netdevice’s ingress and egress hook. It accounts the transferred bytes into
a map called acc_map, which has two map slots, one for traffic accounted on
the ingress hook, one on the egress hook.

```
#include <linux/bpf.h>
#include <linux/pkt_cls.h>
#include <stdint.h>
#include <iproute2/bpf_elf.h>

#ifndef __section
# define __section(NAME)                  \
	__attribute__((section(NAME), used))
#endif

#ifndef __inline
# define __inline                         \
        inline __attribute__((always_inline))
#endif

#ifndef lock_xadd
# define lock_xadd(ptr, val)              \
        ((void)__sync_fetch_and_add(ptr, val))
#endif

#ifndef BPF_FUNC
# define BPF_FUNC(NAME, ...)              \
        (*NAME)(__VA_ARGS__) = (void *)BPF_FUNC_##NAME
#endif

static void *BPF_FUNC(map_lookup_elem, void *map, const void *key);

struct bpf_elf_map acc_map __section("maps") = {
        .type           = BPF_MAP_TYPE_ARRAY,
        .size_key       = sizeof(uint32_t),
        .size_value     = sizeof(uint32_t),
        .pinning        = PIN_GLOBAL_NS,
        .max_elem       = 2,
};

static __inline int account_data(struct __sk_buff *skb, uint32_t dir)
{
        uint32_t *bytes;

        bytes = map_lookup_elem(&acc_map, &dir);
        if (bytes)
                lock_xadd(bytes, skb->len);

        return TC_ACT_OK;
}

__section("ingress")
int tc_ingress(struct __sk_buff *skb)
{
        return account_data(skb, 0);
}

__section("egress")
int tc_egress(struct __sk_buff *skb)
{
        return account_data(skb, 1);
}

char __license[] __section("license") = "GPL";

```

The code can be compiled and loaded via iproute2 as follows:
```
# clang -g -O2 -Wall -target bpf -I ~/iproute2/include/ -c tc-example.c -o tc-example.o

// not sure why, but if I build it without -g, then there will be a failure like
// BTF debug data section '.BTF' rejected: Invalid argument (22)!
// when load it. But the program is actually load and we can find it via
// # tc filter show dev enp3s0 ingress

# tc qdisc add dev enp3s0 clsact
# tc filter add dev enp3s0 ingress bpf da obj tc-example.o sec ingress
# tc filter add dev enp3s0 egress bpf da obj tc-example.o sec egress
# tc filter show dev enp3s0 ingress
filter protocol all pref 49152 bpf chain 0
filter protocol all pref 49152 bpf chain 0 handle 0x1 tc-example.o:[ingress] direct-action not_in_hw id 40 tag c5f7825e5dac396f jited
# tc filter show dev enp3s0 egress
filter protocol all pref 49152 bpf chain 0
filter protocol all pref 49152 bpf chain 0 handle 0x1 tc-example.o:[egress] direct-action not_in_hw id 41 tag b2fd5adc0f262714 jited

# bpftool prog show
40: sched_cls  tag c5f7825e5dac396f  gpl
        loaded_at 2019-09-18T05:50:00-0400  uid 0
        xlated 152B  jited 129B  memlock 4096B  map_ids 37
41: sched_cls  tag b2fd5adc0f262714  gpl
        loaded_at 2019-09-18T05:50:05-0400  uid 0
        xlated 152B  jited 132B  memlock 4096B  map_ids 37
```

To remove the filters
```
# tc filter del dev enp3s0 ingress
# tc filter del dev enp3s0 egress
```

---
### tc/XDP eBPF feature checking

1. If my NIC driver don't support XDP, how do I test it
```
# uname -r
5.3.0
# ip link add type netdevsim
Error: netdevsim: Please use: echo "[ID] [PORT_COUNT]" > /sys/bus/netdevsim/new_device.
# echo "5 2" > /sys/bus/netdevsim/new_device
# ip link show eth3
5: eth3: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether 6a:c8:12:d8:9e:e3 brd ff:ff:ff:ff:ff:ff
# ethtool -i eth3
driver: netdevsim
version:
firmware-version:
expansion-rom-version:
bus-info: netdevsim5
supports-statistics: no
supports-test: no
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: no
# ethtool -K eth3 hw-tc-offload on
# ip link set eth3 xdp obj xdp_mini.o
# ip link show eth3
5: eth3: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 xdp qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
                                                ^^ xdp generic
    link/ether 6a:c8:12:d8:9e:e3 brd ff:ff:ff:ff:ff:ff
    prog/xdp id 2 tag 57cd311f2e27366b
# ip link set eth3 xdp off
```
1. How to show if a NIC driver support xdpoffload
   - XDP modes of operation
     - xdpdrv: native XDP, need driver's implement XDP support
     - xdpgeneric: generic XDP and is intended as experimental test bed for
       drivers which do not yet support native XDP.
     - xdpoffload: implemented by SmartNICS that allow for offloading the
       entire BPF/XDP program into hardware
```
# ip link set eth3 xdpoffload obj xdp_mini.o
# ip link show eth3
5: eth3: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 xdpoffload qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
                                                  ^^ xdpoffload
    link/ether 6a:c8:12:d8:9e:e3 brd ff:ff:ff:ff:ff:ff
    prog/xdp id 1 tag 57cd311f2e27366b
# bpftool prog show id 1
1: xdp  tag 57cd311f2e27366b  offloaded_to eth3  gpl <- offloaded to eth3
        loaded_at 2019-09-18T23:34:55-0400  uid 0
        xlated 16B  not jited  memlock 4096B

```
1. How to show if a XDP program has loaded on a interface
```
# ip link show dev enp3s0
7: enp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 xdp qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 7c:fe:90:bf:1c:20 brd ff:ff:ff:ff:ff:ff
    prog/xdp id 26 tag 57cd311f2e27366b jited

    ^^^ here
```

1. How to translate the bpf code to c format
```
# clang -g -O2 -Wall -target bpf -c tc-example.c -o tc-example.o
# llvm-objdump -S -no-show-raw-insn tc-example.o

tc-example.o:   file format ELF64-BPF

Disassembly of section ingress:
0000000000000000 tc_ingress:
; {
       0:       r6 = r1
; {
       1:       r1 = 0
       2:       *(u32 *)(r10 - 4) = r1
       3:       r2 = r10
; int tc_egress(struct __sk_buff *skb)
       4:       r2 += -4
; bytes = map_lookup_elem(&acc_map, &dir);
       5:       r1 = 0 ll
       7:       call 1
; if (bytes)
       8:       if r0 == 0 goto +2 <LBB0_2>
; lock_xadd(bytes, skb->len);
       9:       r1 = *(u32 *)(r6 + 0)
      10:       lock *(u32 *)(r0 + 0) += r1

0000000000000058 LBB0_2:
; return account_data(skb, 1);
      11:       r0 = 0
      12:       exit
Disassembly of section egress:
0000000000000000 tc_egress:
; {
       0:       r6 = r1
; {
       1:       r1 = 1
       2:       *(u32 *)(r10 - 4) = r1
       3:       r2 = r10
; int tc_egress(struct __sk_buff *skb)
       4:       r2 += -4
; bytes = map_lookup_elem(&acc_map, &dir);
       5:       r1 = 0 ll
       7:       call 1
; if (bytes)
       8:       if r0 == 0 goto +2 <LBB1_2>
; lock_xadd(bytes, skb->len);
       9:       r1 = *(u32 *)(r6 + 0)
      10:       lock *(u32 *)(r0 + 0) += r1

0000000000000058 LBB1_2:
; return account_data(skb, 1);
      11:       r0 = 0
      12:       exit

```
---
### Kernel building

#### Build based on upsteam kernel tree

```
# git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
# dnf install clang llvm bison flex
# cd linux
# make headers_install
# make menuconfig
# cd samples/bpf
```

If you only want to build a xdp kernel part program and load it with ip. Then
you only need to let it build the .o file
```
# git diff
diff --git a/samples/bpf/Makefile b/samples/bpf/Makefile
index 1d9be26b4edd..0b56cd74d602 100644
--- a/samples/bpf/Makefile
+++ b/samples/bpf/Makefile
@@ -170,6 +171,7 @@ always += xdp_sample_pkts_kern.o
 always += ibumad_kern.o
 always += hbm_out_kern.o
 always += hbm_edt_kern.o
+always += xdp_drop.o

 KBUILD_HOSTCFLAGS += -I$(objtree)/usr/include
 KBUILD_HOSTCFLAGS += -I$(srctree)/tools/lib/bpf/

# make
# ip link set netdevsim0 xdp obj xdp_drop.o
```

If you have both user space and kernel space code, e.g. xdp_drop_user.c,
xdp_drop_kern.c, The kern file will contain the code which will be compiled
in BPF bytecode. The user file will be the entrypoint of our program
(to start it). Then you need to edit the Makefile like
```
# git diff .
diff --git a/samples/bpf/Makefile b/samples/bpf/Makefile
index 1d9be26b4edd..25c78a76673c 100644
--- a/samples/bpf/Makefile
+++ b/samples/bpf/Makefile
@@ -53,6 +53,7 @@ hostprogs-y += task_fd_query
 hostprogs-y += xdp_sample_pkts
 hostprogs-y += ibumad
 hostprogs-y += hbm
+hostprogs-y += xdp_drop

 # Libbpf dependencies
 LIBBPF = $(TOOLS_PATH)/lib/bpf/libbpf.a
@@ -109,6 +110,7 @@ task_fd_query-objs := bpf_load.o task_fd_query_user.o $(TRACE_HELPERS)
 xdp_sample_pkts-objs := xdp_sample_pkts_user.o $(TRACE_HELPERS)
 ibumad-objs := bpf_load.o ibumad_user.o $(TRACE_HELPERS)
 hbm-objs := bpf_load.o hbm.o $(CGROUP_HELPERS)
+xdp_drop-objs := bpf_load.o xdp_drop_user.o

 # Tell kbuild to always build the programs
 always := $(hostprogs-y)
@@ -170,6 +172,7 @@ always += xdp_sample_pkts_kern.o
 always += ibumad_kern.o
 always += hbm_out_kern.o
 always += hbm_edt_kern.o
+always += xdp_drop_kern.o

 KBUILD_HOSTCFLAGS += -I$(objtree)/usr/include
 KBUILD_HOSTCFLAGS += -I$(srctree)/tools/lib/bpf/

```

#### Build out of kernel tree
*Note:*: When compiling eBPF C code out of kernel tree, you need install the kernel-header file first

Here is an [example code](https://www.spinics.net/lists/xdp-newbies/msg00009.html) from David.
I removed the bpf_helpers.h here to make it a little easier to build.
```
#include <uapi/linux/bpf.h>
#include <linux/if_ether.h>
#include <linux/etherdevice.h>

#ifndef SEC
# define SEC(NAME)                  \
	__attribute__((section(NAME), used))
#endif

SEC("prog")
int xdp_example1(struct xdp_md *ctx)
{
	void *data_end = (void *)(long)ctx->data_end;
	void *data = (void *)(long)ctx->data;
	struct ethhdr *eth = data;

	/* Make sure a full ethernet header is there.  */
	if (data + sizeof(*eth) > data_end)
		return XDP_DROP;

	/* Drop packet is destination address is multicast.  */
	if (is_multicast_ether_addr(eth->h_dest))
		return XDP_DROP;

	return XDP_PASS;
}
```
And we need a Makefile like

```
KDIR ?= /lib/modules/$(shell uname -r)/source
CLANG ?= clang
LLC ?= llc
ARCH := $(subst x86_64,x86,$(shell arch))

BIN := xdp_drop_multicast.o
CLANG_FLAGS = -I. \
	-include $(KDIR)/include/linux/kconfig.h \
	-I$(KDIR)/include \
	-I$(KDIR)/include/uapi \
	-I$(KDIR)/include/generated/uapi \
	-I$(KDIR)/arch/$(ARCH)/include \
	-I$(KDIR)/arch/$(ARCH)/include/generated \
	-I$(KDIR)/arch/$(ARCH)/include/uapi \
	-I$(KDIR)/arch/$(ARCH)/include/generated/uapi \
	-I$(KDIR)/tools/testing/selftests/bpf/ \
	-D__KERNEL__ -D__BPF_TRACING__ -Wno-unused-value -Wno-pointer-sign \
	-D__TARGET_ARCH_$(ARCH) -Wno-compare-distinct-pointer-types \
	-Wno-gnu-variable-sized-type-not-at-end \
	-Wno-address-of-packed-member -Wno-tautological-compare \
	-Wno-unknown-warning-option  \
	-g -O2 -emit-llvm

all: $(BIN)

%.o: %.c
	$(CLANG) $(CLANG_FLAGS) -c $< -o - | \
		$(LLC) -march=bpf -mcpu=$(CPU) -filetype=obj -o $@
clean:
	rm -f *.o

```

### Some known issues

1. Got error like "Makefile:27:  missing separator.  Stop".

Then check if the new separate line of Makefile has spaces instead of a tab after copy/paste.

2. Got errors like: error: unknown type name 'atomic64_t'; did you mean 'atomic_t'

You'd better add '-include $(KDIR)/include/linux/kconfig.h' in Makefile. For more details, please see
[this stackoverflow answer](https://stackoverflow.com/a/56990939)

---
Reference:
[xdp intro](https://developers.redhat.com/blog/2018/12/06/achieving-high-performance-low-latency-networking-with-xdp-part-1/)

[xdp_code](https://github.com/pabeni/xdp_walkthrough_examples)

Use [xdp_dummy.c](https://git.kernel.org/pub/scm/linux/kernel/git/davem/net.git/tree/tools/testing/selftests/bpf/progs/xdp_dummy.c)
as example. The first include line, provided by the kernel-heade package of
most distributions. The second header, which still is part of the Linux kernel
but is not usually packaged by most distributions, , contains a list of the
available eBPF helpers and the definition of the SEC() macro.
The current solution is, unfortunately, to download the full Linux sources, unpack them somewhere on your local disc.

[a simple xdp introduction](https://mcorbin.fr/pages/xdp-introduction/)

[Dive into BPF: a list of reading material](https://qmonnet.github.io/whirl-offload/2016/09/01/dive-into-bpf/)
