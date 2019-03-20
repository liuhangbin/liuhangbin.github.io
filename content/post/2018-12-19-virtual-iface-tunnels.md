---
title: Tunnel interfaces introduction
date: 2018-12-19
draft: true
---

# Introduction

"An IP tunnel is an Internet Protocol (IP) network communications channel
between two networks. It is used to transport another network protocol by
encapsulation of its packets." quote from
[Wikipedia](https://en.wikipedia.org/wiki/IP_tunnel).

Linux has supported many kinds of tunnels, but new users often get confused
by their differences and about which one is best suited for a given use case.
In this post, I will give a brief introduction for commonly used tunnel interfaces
in the Linux kernel. There is no code analysis, only a brief introduction to the
interfaces and their usage on Linux. Anyone with a network background might be
interested in this blog post. A list of tunnel interfaces can be obtained by
issuing the iproute2 command `ip link help`. Help on specific tunnel
configuration can also be obtained with the command `ip link help <tunnel_type>`.

# Tunnels
## IPIP Tunnel

IPIP tunnel, just as the name suggests, is an IP over IP tunnel, defined
in [RFC 2003](https://tools.ietf.org/html/rfc2003). The IPIP tunnel
header looks like ![IPIP](/ipip.png)

It's typically employed to connect two internal IPv4 subnets through public
IPv4 internet.

It has the lowest overhead but can only transmit IPv4 unicast traffic. So
you __can not__ send multicast via IPIP tunnel.

IPIP tunnel supports both IP over IP and MPLS over IP.

`Note`: When the `ipip` module is loaded, or an IPIP device is created for the first time,
the Linux kernel will create a `tunl0` default device in each namespace, with
attributes local=any and remote=any. When
receiving IPIP protocol packets, the kernel will forward them to tunl0 as a
fallback device if it can't find another device whose local/remote attributes
match their source or destination address more closely.

### How to create an IPIP tunnel
```
On Server A:
# ip link add name ipip0 type ipip local <SERVER_A_v4_ADDR> remote <SERVER_B_v4_ADDR>
# ip link set ipip0 up
# ip addr add <SERVER_A_INTERNAL_v4_ADDR>/24 dev ipip0
Add a remote internal subnet route if the endpoints don't belong to the same subnet
# ip route add <SERVER_B_INTERNAL_v4_SUBNET>/24 dev ipip0

On Server B:
# ip link add name ipip0 type ipip local <SERVER_B_v4_ADDR> remote <SERVER_A_v4_ADDR>
# ip link set ipip0 up
# ip addr add <SERVER_B_INTERNAL_V4_ADDR>/24 dev ipip0
# ip route add <SERVER_A_INTERNAL_v4_SUBNET>/24 dev ipip0
```

## SIT Tunnel
SIT stands for Simple Internet Transition. The main purpose is to interconnect
isolated IPv6 networks, located in global IPv4 Internet.

Initially, it only had an IPv6 over IPv4 tunneling mode. After years of development,
it acquired support for several different modes, such as ipip (the same with IPIP tunnel), ip6ip, mplsip,
any. Mode __any__ is used to accept both IP and IPv6 traffic, which may prove useful in some deployments.
SIT tunnel also supports [ISATAP](https://www.ietf.org/rfc/rfc4214.txt) and
here comes a [usage example](http://www.litech.org/isatap/).

The SIT tunnel header looks like ![sit](/sit.png)

When the sit module is loaded, the Linux kernel will create a default device, named `sit0`.

### How to create a SIT tunnel
```
On Server A:
# ip link add name sit1 type sit local <LOCAL_IPv4_ADDR> remote <REMOTE_IPv4_ADDR> mode any
# ip link set sit1 up
# ip addr add <INTERNAL_IPV4_ADDR>/24 dev sit1

And perform the same steps on the remote side.
```

## ip6tnl Tunnel
ip6tnl is an IPv4/IPv6 over IPv6 tunnel interface, which looks like an IPv6
version of the SIT tunnel. The tunnel header looks like ![ip6tnl](/ip6tnl.png)

ip6tnl supports modes ip6ip6, ipip6, any. ipip6 is IPv4 over IPv6, ip6ip6 is
IPv6 over IPv6, and mode __any__ supports both IPv4/IPv6 over IPv6.

When the ip6tnl module is loaded, the Linux kernel will create a default device, named `ip6tnl0`.

### How to create an ip6tnl tunnel
```
# ip link add name ipip6 type ip6tnl local <LOCAL_IPv6_ADDR> remote <REMOTE_IPv6_ADDR> mode any
```

## VTI/VTI6

VTI (virtual Tunnel Interface) on Linux is similar to Cisco's VTI and
Juniper's implementation of secure tunnel (st.xx).

This particular tunneling driver implements IP encapsulations, which
can be used with xfrm to give the notion of a secure tunnel and then use
kernel routing on top.

In general, VTI tunnels operate almost in the same way as ipip or sit tunnels,
except that they add a fwmark and IPsec encapsulation/decapsulation.

VTI6 is the IPv6 equivalent of VTI.

### How to create a VTI tunnel
```
# ip link add name vti1 type vti key <VTI_KEY> local <LOCAL_IPv4_ADDR> remote <REMOTE_IPv4_ADDR>
# ip link set vti1 up
# ip addr add <LOCAL_VIRTUAL_ADDR>/24 dev vti1

# ip xfrm state add src <LOCAL_IPv4_ADDR> dst <REMOTE_IPv4_ADDR> spi <SPI> <PROTO> <ALGO> mode tunnel
# ip xfrm state add src <REMOTE_IPv4_ADDR> dst <LOCAL_IPv4_ADDR> spi <SPI> <PROTO> <ALGO> mode tunnel
# ip xfrm policy add dir in  tmpl src <REMOTE_IPv4_ADDR> dst <LOCAL_IPv4_ADDR> <PROTO> mode tunnel mark <VTI_KEY>
# ip xfrm policy add dir out tmpl src <LOCAL_IPv4_ADDR> dst <REMOTE_IPv4_ADDR> <PROTO> mode tunnel mark <VTI_KEY>
```

You can also config IPsec via [libreswan](https://libreswan.org/wiki/Route-based_VPN_using_VTI)
or [strongswan](https://wiki.strongswan.org/projects/strongswan/wiki/RouteBasedVPN).

## GRE/GRETAP

Generic Routing Encapsulation, also known as GRE, is defined in [RFC 2784](https://tools.ietf.org/html/rfc2784)

GRE tunnel adding an additional GRE header between the inside and outside IP headers.
In theory, GRE could encapsulate any layer three protocol with a valid
Ether type, unlike IPIP, which can only encapsulate IP. The GRE header looks
like ![gre](/gre.png)

Note that you can transport multicast traffic and IPv6 through a GRE tunnel.

When the gre module is loaded, the Linux kernel will create a default device, named `gre0`.

### How to create a GRE tunnel
```
# ip link add name gre1 type gre local <LOCAL_IPv4_ADDR> remote <REMOTE_IPv4_ADDR> [seq] key <KEY>
```

While GRE tunnels operate at OSI layer three. GRETAP works at OSI layer two,
which mean there is an ether header in inner header.
![gretap](/gretap.png)

### How to create a GRETAP tunnel
```
# ip link add name gretap1 type gretap local <LOCAL_IPv4_ADDR> remote <REMOTE_IPv4_ADDR>
```


## GRE6/GRE6TAP

GRE6 is the IPv6 equivalent of GRE, that allows us to encapsulate any layer three protocol
over IPv6. The tunnel header looks like ![gre6](/gre6.png)

GRE6TAP, just list GRETAP, has an Ethernet header in the inner header
![gre6tap](/gre6tap.png)

### How to create a GRE tunnel
```
# ip link add name gre1 type gre6 local <LOCAL_IPv6_ADDR> remote <REMOTE_IPv6_ADDR>
# ip link add name gretap1 type gretap6 local <LOCAL_IPv6_ADDR> remote <REMOTE_IPv6_ADDR>
```

## FOU

Tunneling can happen at multiple levels in the networking stack.
IPIP, SIT, GRE are at the IP level, while the FOU (foo over UDP) is a
UDP-level tunneling.

There are some advantages of using UDP tunneling as UDP works with existing HW
infrastructure, like [RSS](https://en.wikipedia.org/wiki/Network_interface_controller#RSS) in NICs,
[ECMP](https://en.wikipedia.org/wiki/Equal-cost_multi-path_routing) in switches, and checksum offload.
The developer's [patch set](https://lwn.net/Articles/614433/) shows significant performance increases
for the SIT and IPIP protocols.

Currently, the FOU tunnel supports encapsulation protocol based on IPIP, SIT, GRE.
An example FOU header looks like
![fou](/fou.png)

### How to create a FOU tunnel
```
# ip fou add port 5555 ipproto 4
# ip link add name tun1 type ipip remote 192.168.1.1 local 192.168.1.2 ttl 225 encap fou encap-sport auto encap-dport 5555
```
The first command configured a FOU receive port for IPIP bound to 5555, for
GRE you need set `ipproto 47`. The second command set up a new IPIP virtual
interface (tun1) configured for FOU encapsulation, with dest port 5555.

`NOTE`: FOU is not supported in RHEL

## GUE

GUE ([Generic UDP Encapsulation](https://tools.ietf.org/html/draft-ietf-intarea-gue)) is another kind of UDP tunneling. The difference between
FOU and GUE is GUE has its own encapsulation header (GUE), which contains the protocol info and other data.

The GUE tunnel header looks very similar to [VXLAN](https://developers.redhat.com/blog/2018/10/22/introduction-to-linux-interfaces-for-virtual-networking/#vxlan), but GUE tunnel supports inner IPIP, SIT, GRE encapsulation.

An example GUE header looks like
![gue](/gue.png)

### How to create a GUE tunnel
```
# ip fou add port 5555 gue
# ip link add name tun1 type ipip remote 192.168.1.1 local 192.168.1.2 ttl 225 encap gue encap-sport auto encap-dport 5555
```
This will set up a GUE receive port for IPIP bound to 5555, and an IPIP tunnel
configured for GUE encapsulation.

`NOTE`: GUE is not supported in RHEL

## GENEVE

GENEVE (Generic Network Virtualization Encapsulation) supports all of the
capabilities of VXLAN, NVGRE and STT, and was designed to overcome their
perceived limitations. Many believe GENEVE could eventually
replace these earlier formats entirely. The tunnel header looks like
![geneve](/geneve.png) which looks very similar to
[VXLAN](https://developers.redhat.com/blog/2018/10/22/introduction-to-linux-interfaces-for-virtual-networking/#vxlan).
The main difference is that the GENEVE header is flexible. It's very easy to add new
features by extending the header with a new TLV (Type-Length-Value) field. Here is the
latest [geneve ietf draft](https://tools.ietf.org/html/draft-ietf-nvo3-geneve-08).
For more details, you can refer to a blog post specifically focused on [what's geneve](https://www.redhat.com/en/blog/what-geneve).

OVN use GENEVE as default encapsulation.

### How to create a GENEVE tunnel
```
# ip link add name geneve0 type geneve id <VNI> remote <REMOTE_IPv4_ADDR>
```

## ERSPAN/IP6ERSPAN

ERSPAN (Encapsulated Remote Switched Port Analyzer) uses GRE encapsulation to
extend the basic port mirroring capability from Layer 2 to Layer 3,
which allows the mirrored traffic to be sent through a routable IP network.
The ERSPAN header looks like ![erspan](/erspan.png)

The ERSPAN tunnel allows a Linux host to act as an ERSPAN traffic source and send the
ERSPAN mirrored traffic to a remote host, or to an ERSPAN destination which
receives and parses the ERSPAN packets generated from Cisco or other
ERSPAN-capable switches. This could be used to analyze, diagnose, detect malicious traffic.

Linux currently supports most features of two ERSPAN versions, v1 (type II) and v2 (type III).

### How to create an ERSPAN tunnel
```
# ip link add dev erspan1 type erspan local <LOCAL_IPv4_ADDR> remote <REMOTE_IPv4_ADDR> seq key <KEY> erspan_ver 1 erspan <IDX>
or
# ip link add dev erspan1 type erspan local <LOCAL_IPv4_ADDR> remote <REMOTE_IPv4_ADDR> seq key <KEY> erspan_ver 2 erspan_dir <DIRECTION> erspan_hwid <HWID>

Add tc filter to monitor traffic
# tc qdisc add dev <MONITOR_DEV> handle ffff: ingress
# tc filter add dev <MONITOR_DEV> parent ffff: matchall skip_hw action mirred egress mirror dev erspan1
```

# Summary

Here is a summary of all the tunnels we introduced.

| Tunnel/Link Type | Outer Header | Encapsulate Header | Inner Header      |
| ------           | ------------ | ------------       | ------------      |
| ipip             | IPv4         | None               | IPv4              |
| sit              | IPv4         | None               | IPv4/IPv6         |
| ip6tnl           | IPv6         | None               | IPv4/IPv6         |
| vit              | IPv4         | IPsec              | IPv4              |
| vit6             | IPv6         | IPsec              | IPv6              |
| gre              | IPv4         | GRE                | IPv4/IPv6         |
| gretap           | IPv4         | GRE                | Ether + IPv4/IPv6 |
| gre6             | IPv6         | GRE                | IPv4/IPv6         |
| gre6tap          | IPv6         | GRE                | Ether + IPv4/IPv6 |
| fou              | IPv4/IPv6    | UDP                | IPv4/IPv6/GRE     |
| gue              | IPv4/IPv6    | UDP + GUE          | IPv4/IPv6/GRE     |
| geneve           | IPv4/IPv6    | UDP + Geneve       | Ether + IPv4/IPv6 |
| erspan           | IPv4         | GRE + ERSPAN       | IPv4/IPv6         |
| ip6erspan        | IPv6         | GRE + ERSPAN       | IPv4/IPv6         |

`Note`: All configurations in this tutorial are volatile and won’t survive to a
server reboot. If you want to make the configuration persistent across reboots,
please consider using a networking configuration daemon, such as
[NetworkManager](https://developer.gnome.org/NetworkManager/stable/), or
distribution-specific mechanisms.
