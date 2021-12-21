---
title: XDP TC eBPF Programing
date: 2019-10-08
---

=== XDP Programing

Reference: [another example code](https://mcorbin.fr/pages/xdp-introduction/)

``` count_packets.c
// SPDX-License-Identifier: GPL-2.0
#include "bpf.h"
#include "bpf_helpers.h"
#include <linux/in.h>
#include <linux/if_ether.h>
#include <linux/ip.h>
//#include <linux/ipv6.h>
#include <linux/etherdevice.h>
#include <linux/if_vlan.h>

/* Count for ether, vlan, QinQ, IPv4, IPv6, tcp, udp, sctp, igmp */
enum packet_counts {
	ETHER = 0,
	VLAN,
	QINQ,
	IPV4,
	IPV6,
	TCP,
	UDP,
	SCTP,
	IGMP,
	COUNT_MAX,
};

/* array to store all types package counts */
struct bpf_map_def SEC("maps") count_map = {
	.type = BPF_MAP_TYPE_ARRAY,
	.key_size = sizeof(u32),
	.value_size = sizeof(u32),
	.max_entries = COUNT_MAX,
};

SEC("prog")
int xdp_count(struct xdp_md *ctx)
{
	void *data = (void *)(long)ctx->data;
	void *data_end = (void *)(long)ctx->data_end;
	struct ethhdr *eth = data;
	u16 h_proto;
	u64 nh_off;
	u32 *count;
	u32 index = 0;

	nh_off = sizeof(struct ethhdr);
	if (data + nh_off > data_end)
		return XDP_DROP;	/* Or just return XDP_PASS to not filter any packet */

	/* find correspond index data and do puls one */
	index = ETHER;
	count = bpf_map_lookup_elem(&count_map, &index);
	if (count)
		__sync_fetch_and_add(count, 1);

	h_proto = ntohs(eth->h_proto);
	if (is_multicast_ether_addr(eth->h_dest)) {
		index = IGMP;
		count = bpf_map_lookup_elem(&count_map, &index);
		if (count)
			__sync_fetch_and_add(count, 1);
	}

	if (h_proto == ETH_P_8021Q) {
		struct vlan_hdr *vhdr;

		vhdr = data + nh_off;
		nh_off += sizeof(struct vlan_hdr);
		if (data + nh_off > data_end)
			return XDP_PASS;
		h_proto = ntohs(vhdr->h_vlan_encapsulated_proto);

		index = VLAN;
		count = bpf_map_lookup_elem(&count_map, &index);
		if (count)
			__sync_fetch_and_add(count, 1);
	} else if (h_proto == ETH_P_8021AD) {
		struct vlan_hdr *vhdr;

		vhdr = data + nh_off + sizeof(struct vlan_hdr);
		nh_off += 2 * sizeof(struct vlan_hdr);
		if (data + nh_off > data_end)
			return XDP_PASS;
		h_proto = ntohs(vhdr->h_vlan_encapsulated_proto);

		index = QINQ;
		count = bpf_map_lookup_elem(&count_map, &index);
		if (count)
			__sync_fetch_and_add(count, 1);
	}

	if (h_proto == ETH_P_IP) {
		struct iphdr *iph;

		iph = data + nh_off;
		nh_off += sizeof(struct iphdr);
		if (data + nh_off > data_end)
			return XDP_PASS;
		h_proto = ntohs(iph->protocol);

		index = IPV4;
		count = bpf_map_lookup_elem(&count_map, &index);
		if (count)
			__sync_fetch_and_add(count, 1);

/* commit IPv6 code as there is a build error
	} else if (h_proto == ETH_P_IPV6) {
		struct ipv6hdr *ip6h;

		ip6h = data + nh_off;
		nh_off += sizeof(struct ipv6hdr);
		if (data + nh_off > data_end)
			return XDP_PASS;
		h_proto = ntohs(ip6h->nexthdr);

*/
	} else {
		h_proto = 0;
	}

	if (h_proto == IPPROTO_TCP) {
		index = TCP;
		count = bpf_map_lookup_elem(&count_map, &index);
		if (count)
			__sync_fetch_and_add(count, 1);
	} else if (h_proto == IPPROTO_UDP) {
		index = UDP;
		count = bpf_map_lookup_elem(&count_map, &index);
		if (count)
			__sync_fetch_and_add(count, 1);
	} else if (h_proto == IPPROTO_SCTP) {
		index = SCTP;
		count = bpf_map_lookup_elem(&count_map, &index);
		if (count)
			__sync_fetch_and_add(count, 1);
	}

	return XDP_PASS;
}

char _license[] SEC("license") = "GPL";

```

``` Makefile

KDIR ?= /lib/modules/$(shell uname -r)/source
CLANG ?= clang
LLC ?= llc
ARCH := $(subst x86_64,x86,$(shell arch))

BIN := count_packets.o
CLANG_FLAGS =-I. \
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

=== bpf printk

```
SEC("xdp_printk")
int xdp_printk(struct xdp_md *ctx)
{
       char fmt[] = "devmap redirect: dev %u -> dev %u len %u\n";
       void *data_end = (void *)(long)ctx->data_end;
       void *data = (void *)(long)ctx->data;
       unsigned int len = data_end - data;

       bpf_trace_printk(fmt, sizeof(fmt),
                       ctx->ingress_ifindex, ctx->egress_ifindex, len);

       return XDP_PASS;
}

After load the section, you can get the print info from
cat /sys/kernel/debug/tracing/trace_pipe
```

More examples, please see [the guide to bpf_trace_printk() and bpf_printk()](https://nakryiko.com/posts/bpf-tips-printk/)

=== Reference
[An other Makefile example](https://github.com/dpino/xdp_ipv6_filter/blob/master/Makefile)
[xdp-tutorial](https://github.com/xdp-project/xdp-tutorial)
