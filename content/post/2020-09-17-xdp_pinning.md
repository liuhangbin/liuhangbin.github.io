---
title: xdp_pinning
date: 2020-09-17
---

iproute2 support setting pinning in maps. Here is an example about how to use
it. Note that libbpf do not support setting pinning in map defination. So this
way is not recommand.


$ cat xdp_pin.c

#include <uapi/linux/bpf.h>
#include <bpf/bpf_helpers.h>

struct bpf_elf_map {
	__u32 type;
	__u32 size_key;
	__u32 size_value;
	__u32 max_elem;
	__u32 flags;
	__u32 id;
	__u32 pinning;
};

struct bpf_elf_map SEC("maps") xdp_pin_map = {
	.type = BPF_MAP_TYPE_PERCPU_HASH,
	.size_key = sizeof(__u64),
	.size_value = sizeof(__u64),
	.pinning = 2,
	.max_elem = 2,
};

SEC("bpf_pin")
int bpf_prog(struct __sk_buff *skb)
{
	__u64 *value, key = 0, init_val = 1;

	value = bpf_map_lookup_elem(&xdp_pin_map, &key);
	if (value)
		__sync_fetch_and_add(value, 1);
	else
		bpf_map_update_elem(&xdp_pin_map, &key, &init_val, BPF_ANY);

	return BPF_OK;
}

SEC("xdp_pass")
int xdp_prog(struct xdp_md *ctx)
{
	return XDP_PASS;
}

char _license[] SEC("license") = "GPL";


$ clang -O2 -Wall -target bpf -I ~/iproute2/include/ -c xdp_pin.c -o xdp_pin.o
$ ip link set veth0 xdp obj xdp_pin.o sec bpf_pin
$ ls /sys/fs/bpf/xdp/
xdp_pin_map

Or you can use bpftool to pin a map(must be libbpf supported type, not the map
in this example) directly by:
$  bpftool prog loadall xdp_pin.o /sys/fs/bpf/xdp/progs type xdp pinmaps /sys/fs/bpf/xdp/maps
