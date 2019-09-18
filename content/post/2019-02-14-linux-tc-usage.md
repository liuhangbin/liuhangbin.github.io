---
title: linux tc usage
date: 2019-02-14
---

TC, means traffic control, can be achieved by using queuing disciplines(qdisc).
Here is an example picture ![tc_tree](/tc_qdisc_tree.png)

## Classes

There are two kinds of qdiscs, classless and classfull.

  - Classless qciscs can only have a single scheduler assigned, can't contain more classes
    - Pfifo: limits the size of a queue by packtets
    - Bfifo: limits the size of a queue by bytes
    - Pfifo_fast: contains 3 fifo queues, from 0 to 2. 0 get the packets with
      hightest priority.
    - SFQ: Stochastic Fairness Queueing
      - `tc qdisc add dev eth0 root handle 1: sfq`
    - TBF: Token Bucket Filter. looks like fifo with tokens.
      - `tc qdisc add dev eth1 root handle 1: tbf rate 256kbit burst 10000 latency 50ms`
    - Netem: network emulator. Could add delay for in/out packets. simulate packet loss, corruption, duplication, reordering.
```
      # tc qdisc add dev eth0 root netem delay 100ms
        - add delay both incoming and outgoing packets by 100ms
      # tc qdisc change dev eth0 root netem delay 100ms 10ms loss 0.1\%
```
    - Others
      - fq, fq_codel, gred, hhf, ingress, mqprio, multiq, pie, red, rr, sfb
  - Classful qdiscs can be used on a network interface as the primary qdisc
    or inside a leaf class of a classfull qdisc. It can contain multiple
    classes and each class can again contain multiple classes
    - PRIO: This class will create class 1:1, 1:2, 1:3 by default, and handle 1:1 first
```
      # tc qdisc add dev eth0 root handle 1: prio
      # tc qdisc add dev eth0 parent 1:1 handle 10: sfq
      # tc qdisc add dev eth0 parent 1:2 handle 20: tbf rate 20kbit buffer 1600 limit 3000
      # tc qdisc add dev eth0 parent 1:3 handle 30: sfq
      # tc filter add dev eth0 parent 1:1 prio 1003 protocol ip u32 match ip src 192.168.1.0/24 flowid 1:10
      # tc filter add dev eth0 parent 1:2 prio 1001 protocol ip u32 match ip src 10.0.0.0/8 flowid 1:20
      # tc filter add dev eth0 parent 1:3 protocol ip prio 1001 u32 match ip tos 0x10 0xff flowid 1:30
```
    - CBQ: Class based Queuing, simple shaping algorithm, takes less CPU, but inaccurate
```
      # tc qdisc add dev eth0 root handle 1:0 cbq bandwidth 100Mbit avpkt 1000 cell 8
      # tc class add dev eth0 parent 1:0 classid 1:1 cbq bandwidth 100Mbit rate 6Mbit weight 0.6Mbit prio 8 allot 1514 cell 8 maxburst 20 avpkt 1000 bounded
      # tc class add dev eth0 parent 1:1 classid 1:3 cbq bandwidth 100Mbit rate 5Mbit weight 0.5Mbit prio 5 allot 1514 cell 8 maxburst 20 avpkt 1000
      # tc class add dev eth0 parent 1:1 classid 1:4 cbq bandwidth 100Mbit rate 3Mbit weight 0.3Mbit prio 5 allot 1514 cell 8 maxburst 20 avpkt 1000
      # tc qdisc add dev eth0 parent 1:3 handle 30: sfq
      # tc qdisc add dev eth0 parent 1:4 handle 40: sfq
        - There will have a fifo queue by default in each class, but we change it to sfq
      # tc filter add dev eth0 parent 1:0 protocol ip prio 1 u32 match ip sport 80 0xffff flowid 1:3
        - web server goto 1:3
      # tc filter add dev eth0 parent 1:0 protocol ip prio 1 u32 match ip sport 25 0xffff flowid 1:4
        - smtp msg goto 1:4
```
    - HTB: Hierarchy token bucket, more accurate shaping, you need to put all
      the filters in root when use htb.
```
      # tc qdisc add dev eth0 root handle 1: htb default 30
      # tc class add dev eth0 parent 1: classid 1:1 htb rate 6mbit burst 15k
      # tc class add dev eth0 parent 1:1 classid 1:10 htb rate 5mbit burst 15k
      # tc class add dev eth0 parent 1:1 classid 1:20 htb rate 3mbit ceil 6mbit burst 15k
      # tc class add dev eth0 parent 1:1 classid 1:30 htb rate 1kbit ceil 6mbit burst 15k
      # tc qdisc add dev eth0 parent 1:10 handle 10: sfq perturb 10
      # tc qdisc add dev eth0 parent 1:20 handle 20: sfq perturb 10
      # tc qdisc add dev eth0 parent 1:30 handle 30: sfq perturb 10
      # U32="tc filter add dev eth0 protocol ip parent 1:0 prio 1 u32"
      # $U32 match ip dport 80 0xffff flowid 1:10
      # $U32 match ip sport 25 0xffff flowid 1:20
```
  - Ingress qdisc: All qdiscs discussed so far are egress qdiscs. The ingress
    qdisc itself does not require any parameters. It differs from other qdiscs
    in that it does not occupy the root of a device.
    - Flower
```
      # tc qdisc add dev eth0 ingress
      # tc filter add dev eth0 protocol ip parent ffff: flower ip_proto sctp dst_port 80 action drop
```
      - Hardware offload
```
        # ethtool -K eth0 hw-tc-offload on
        # tc qdisc add dev eth0 ingress
        # tc filter add dev eth0 protocol ip parent ffff: flower skip_sw ip_proto sctp dst_port 80 action drop
        # tc filter show dev eth0 ingress
```
    - Others
      - ATM, DRR, DSMARK, HFSC, QFQ
  - Example
    - Classless
```
      # tc qdisc show dev eth1
      # tc qdisc change dev eth1 root bfifo
      # tc qdisc del dev eth0 root
```

## Filter
Filters are rules that can be attached to each classfull qdisc node
and specify how should a packet be processed based on specific criteria.

  - u32: the commonly used filter, very powerfull. Could filter packets based
    on several criteria, including source, dest addr, port, protocol.
    0xffff means all the packets
```
    # tc filter add dev eth0 protocol ip parent 10: prio 1 u32 match ip dport 22 0xffff flowid 10:1
    # tc filter add dev eth0 protocol ip parent 10: prio 2 flowid 10:2
    # tc filter add dev eth0 parent 10:0 protocol ip prio 1 u32 match ip src 4.3.2.1/32 match ip sport 80 0xffff flowid 10:1
```
    - matches
```
      - match ip src 1.2.3.0/24
      - match ip dst 4.3.2.0/24
      - match ip 1.2.3.4/32
      - match ip sport 80 0xffff
      - match ip dport 80 0xffff
      - match ip protocol 1 0xff
        - match icmp, the proto number is from /etc/protocols
```
    - fwmark
```
      # iptables -A PREROUTING -t mangle -i eth0 -j MARK --set-mark 6
      # tc filter add dev eth1 protocol ip parent 1:0 prio 1 handle 6 fw flowid 1:1
```
  - Examples
    - `tc filter show dev eth1`


## Examples
1. Assigning a classfull HTB qdisc to the default network interface (eth2 in
this case) with a single leaf node and then assigning a filter leading all
traffic to this node. (ceil: maximum transfer rate, see man tc-htb)

```
# tc qdisc add dev eth2 root handle 1: htb
# tc class add dev eth2 parent 1: classid 1:1 htb rate 20kbit ceil 20kbit
# tc filter add dev eth2 protocol ip parent 1: prio 1 u32 match ip dst 192.168.77.24 flowid 1:1
# tc class change dev eth2 parent 1: classid 1:1 htb rate 20kbit ceil 2000kbit
```

## Reference:

[Linux流量控制工具TC](http://codeshold.me/2017/01/tc_inro.html)

[流量控制工具TC详细说明](http://codeshold.me/2017/01/tc_detail_inro.html)

[Linux 高级流控](https://www.ibm.com/developerworks/cn/linux/1412_xiehy_tc/)

[networking:netem](https://wiki.linuxfoundation.org/networking/netem)

