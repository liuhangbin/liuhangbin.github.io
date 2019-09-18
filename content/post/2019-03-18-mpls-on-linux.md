---
title: mpls routing on linux
date: 2019-03-18
---

[MPLS](https://en.wikipedia.org/wiki/Multiprotocol_Label_Switching)
Multiprotocol Label Switching is a routing protocol, which works in layer 2.5.
It insert a lable between IP layer and protocol layer. This looks like VLAN,
which works in layer 1.5 and in subnet. But MPLS works in global internet.

## Enable MPLS Routing

Some distribution may build mpls route as a module, so before start, please
check if you have `/proc/sys/net/mpls/`, if not, then load the module first:
```
# modprobe mpls_router
# modprobe mpls_iptunnel
# lsmod | grep mpls
mpls_iptunnel          16384  0
mpls_router            36864  1 mpls_iptunnel
ip_tunnel              28672  1 mpls_router

```

If failed, then we need to enable CONFIG_MPLS, CONFIG_MPLS_ROUTING,
CONFIG_MPLS_IPTUNNEL when compile the kernel. If you got error after build
like
```
# ip -f mpls route add 101 dev lo
RTNETLINK answers: Operation not supported
```
then you need to run `sysctl -w net.mpls.platform_labels=65535`. See
[mpls-sysctl](https://www.kernel.org/doc/Documentation/networking/mpls-sysctl.txt)

## MPLS Routing

### Config examples

Routing 10.10.10.10/32 to 192.168.1.2 with label 100:
`ip route add 10.10.10.10/32 encap mpls 100 via inet 192.168.1.2`

Label swapping 100 for 200 and sent to 192.168.2.2:
`ip -f mpls route add 100 as 200 via inet 192.168.2.2`

Decapsulating label 300 and delivering locally:
`ip -f mpls route add 300 dev lo`

### MPLS Routing example

Base Machine: veth0(peer veth1), veth2(peer veth3)
Host1: veth1
Host2: veth3

```
ip netns add host1
ip netns add host2
ip link add veth0 type veth peer netns host1 name veth1
ip link add veth2 type veth peer netns host2  name veth3

sysctl -w net.mpls.platform_labels=65535
sysctl -w net.mpls.conf.veth0.input=1
sysctl -w net.mpls.conf.veth2.input=1

ip link set veth0 up
ip link set veth2 up
ip netns exec host1 ip link set lo up
ip netns exec host2 ip link set lo up
ip netns exec host1 ip link set veth1 up
ip netns exec host2 ip link set veth3 up

ip addr add 10.3.3.1/24 dev veth0
ip netns exec host1 ip addr add 10.3.3.2/24 dev veth1
ip addr add 10.4.4.1/24 dev veth2
ip netns exec host2 ip addr add 10.4.4.2/24 dev veth3

ip netns exec host1 ip addr add 10.10.10.1/32 dev lo
ip netns exec host2 ip addr add 10.10.10.2/32 dev lo

ip netns exec host1 ip route add 10.10.10.2/32 encap mpls 112 via inet 10.3.3.1
ip netns exec host2 ip route add 10.10.10.1/32 encap mpls 111 via inet 10.4.4.1

ip -f mpls route add 111 via inet 10.3.3.2
ip -f mpls route add 112 via inet 10.4.4.2
```

Run `ip netns exec host2 ping 10.10.10.1 -I 10.10.10.2` and we can got
```
# tcpdump -i veth0 -nn -l
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on veth0, link-type EN10MB (Ethernet), capture size 262144 bytes
23:48:14.829921 IP 10.10.10.2 > 10.10.10.1: ICMP echo request, id 8637, seq 1, length 64
23:48:14.830016 MPLS (label 112, exp 0, [S], ttl 64) IP 10.10.10.1 > 10.10.10.2: ICMP echo reply, id 8637, seq 1, length 64
23:48:15.849543 IP 10.10.10.2 > 10.10.10.1: ICMP echo request, id 8637, seq 2, length 64
23:48:15.849748 MPLS (label 112, exp 0, [S], ttl 64) IP 10.10.10.1 > 10.10.10.2: ICMP echo reply, id 8637, seq 2, length 64
```

`Note`: Label 111 was decapsulated on base machine.

## MPLS Tunnel Routing
1. Setup two machines or netns: ha and hb

```
ip netns add ha
ip netns add hb

ip link add veth1 type veth peer netns hb name veth1
ip link set veth1 netns ha
ip netns exec ha ip link set veth1 up
ip netns exec hb ip link set veth1 up
ip netns exec ha ip addr add 192.168.1.1/24 dev veth1
ip netns exec hb ip addr add 192.168.1.2/24 dev veth1
```

2. Add gre tunnel and setup address

```
ip netns exec ha ip link add foo type gre remote 192.168.1.2 local 192.168.1.1
ip netns exec hb ip link add foo type gre remote 192.168.1.1 local 192.168.1.2

ip netns exec ha ip addr add 192.168.100.1/24 dev foo
ip netns exec hb ip addr add 192.168.100.2/24 dev foo
ip netns exec ha ip link set foo up
ip netns exec hb ip link set foo up
```

3. Add mpls route

```
ip netns exec ha sysctl -w net.mpls.platform_labels=65535
ip netns exec ha sysctl -w net.mpls.conf.foo.input=1
ip netns exec ha ip route change 192.168.100.0/24 encap mpls 100 dev foo
ip netns exec ha ip -f mpls route add 100 dev lo

ip netns exec hb sysctl -w net.mpls.platform_labels=65535
ip netns exec hb sysctl -w net.mpls.conf.foo.input=1
ip netns exec hb ip route change 192.168.100.0/24 encap mpls 100 dev foo
ip netns exec hb ip -f mpls route add 100 dev lo
```

4. Set `rp_filter` to relaxed mode or completely disable it for packets to be accepted correctly

```
ip netns exec ha sysctl -w net.ipv4.conf.all.rp_filter=2
ip netns exec hb sysctl -w net.ipv4.conf.all.rp_filter=2
```

5. For IPv6

```
ip netns exec ha ip addr add 2100::1/64 dev foo
ip netns exec hb ip addr add 2100::2/64 dev foo
ip netns exec ha ip -6 route del 2100::/64 dev foo
ip netns exec ha ip -6 route add 2100::/64 encap mpls 100 dev foo
ip netns exec hb ip -6 route del 2100::/64 dev foo
ip netns exec hb ip -6 route add 2100::/64 encap mpls 100 dev foo
```

## MPLS RFCs

1. [RFC3032](https://tools.ietf.org/html/rfc3032) MPLS Label Stack Encoding

1. [RFC4023](https://tools.ietf.org/html/rfc4203) Encapsulating MPLS in IP or Generic Routing Encapsulation (GRE)
| Ether Hdr | IP Hdr | MPLS Hdr | Message |
| Ether Hdr | IP Hdr | GRE Hdr | MPLS Hdr | Inner IP | Message |

1. [RFC7510](https://tools.ietf.org/html/rfc7510) Encapsulating MPLS in UDP

| Ether Hdr | IP Hdr | UDP Hdr | MPLS Hdr | Message |

Linux kernel has [supported](https://www.spinics.net/lists/netdev/msg385392.html) this feature, but it need FOU enabled first.


References:

1. [MPLS testbed with kernel 4.3](http://www.samrussell.nz/2015/12/mpls-testbed-on-ubuntu-linux-with.html)
1. [MPLS in GRE tunnel](https://jsteward.moe/mpls-in-gre-tunnel-linux.html)
1. [mpls tutorial](https://www.netdevconf.org/1.1/proceedings/slides/prabhu-mpls-tutorial.pdf)
