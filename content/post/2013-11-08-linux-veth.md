---
layout: post
Title: Linux veth
Date: 2013-11-08
tags: [Linux, ip, veth]
---


Linux的网络虚拟化是LXC项目中的一个子项目，LXC包括文件系统虚拟化，进程空间虚拟化，用户虚拟化，网络虚拟化，等等
[1]，这里使用LXC的网络虚拟化来模拟多个网络环境。

== 创建虚拟网络环境

使用命令 `$ ip netns add net0`
可以创建一个完全隔离的新网络环境，这个环境包括一个独立的网卡空间，路由表，ARP表，ip地址表，iptables，ebtables，等等。总之，与网络有关的组件都是独立的。

使用命令
```
$ ip netns list
net0
```
可以看到我们刚才创建的网络环境

== 进入虚拟网络环境

使用命令 `$ ip netns exec net0 `command``
我们可以在 net0 虚拟环境中运行任何命令
```
$ ip netns exec net0 bash
$ ip ad
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
```

这样我们可以在新的网络环境中打开一个shell，可以看到，新的网络环境里面只有一个lo设备，并且这个lo设备与外面的lo设备是不同的，之间不能互相通讯。

== 连接两个网络环境

新的网络环境里面没有任何网络设备，并且也无法和外部通讯，就是一个孤岛，通过下面介绍的这个方法可以把两个网络环境连起来，简单的说，就是在两个网络环境之间拉一根网线

`$ ip netns add net1`

先创建另一个网络环境net1，我们的目标是把net0与net1连起来

```
$ ip link add type veth
$ ip ad
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    81: veth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 12:39:09:81:3a:dd brd ff:ff:ff:ff:ff:ff
    82: veth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 32:4f:fd:cc:79:1b brd ff:ff:ff:ff:ff:ff
```
这里创建连一对veth虚拟网卡，类似pipe，发给veth0的数据包veth1那边会收到，发给veth1的数据包veth0会收到。就相当于给机器安装了两个网卡，并且之间用网线连接起来了

```
$ ip link set veth0 netns net0
$ ip link set veth1 netns net1
```

这两条命令的意思就是把veth0移动到net0环境里面，把veth1移动到net1环境里面，我们看看结果

```
$ ip ad
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
$ ip netns exec net0 ip ad
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
81: veth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 12:39:09:81:3a:dd brd ff:ff:ff:ff:ff:ff
$ ip netns exec net1 ip ad
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
82: veth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 32:4f:fd:cc:79:1b brd ff:ff:ff:ff:ff:ff
```
veth0 veth1已经在我们的环境里面消失了，并且分别出现在net0与net1里面。下面我们简单测试一下net0与net1的联通性

```
$ ip netns exec net0 ip link set veth0 up
$ ip netns exec net0 ip address add 10.0.1.1/24 dev veth0
$ ip netns exec net1 ip link set veth1 up
$ ip netns exec net1 ip address add 10.0.1.2/24 dev veth1
```

分别配置好两个设备，然后用ping测试一下联通性：

```
$ ip netns exec net0 ping -c 3 10.0.1.2
PING 10.0.1.2 (10.0.1.2) 56(84) bytes of data.
64 bytes from 10.0.1.2: icmp_req=1 ttl=64 time=0.101 ms
64 bytes from 10.0.1.2: icmp_req=2 ttl=64 time=0.057 ms
64 bytes from 10.0.1.2: icmp_req=3 ttl=64 time=0.048 ms

--- 10.0.1.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.048/0.068/0.101/0.025 ms
```

== 一个稍微复杂的网络环境
```
                    |--- net0 (.1)
bridge(10.0.1.0/24) |
                    |--- net1 (.2)
```
创建虚拟网络环境并且连接网线
```
ip netns add net0
ip netns add net1
ip netns add bridge
ip link add type veth
ip link set dev veth0 name net0-bridge netns net0
ip link set dev veth1 name bridge-net0 netns bridge
ip link add type veth
ip link set dev veth0 name net1-bridge netns net1
ip link set dev veth1 name bridge-net1 netns bridge
```

在bridge中创建并且设置br设备
```
ip netns exec bridge brctl addbr br
ip netns exec bridge ip link set dev br up
ip netns exec bridge ip link set dev bridge-net0 up
ip netns exec bridge ip link set dev bridge-net1 up
ip netns exec bridge brctl addif br bridge-net0
ip netns exec bridge brctl addif br bridge-net1
```

然后配置两个虚拟环境的网卡
```
ip netns exec net0 ip link set dev net0-bridge up
ip netns exec net0 ip address add 10.0.1.1/24 dev net0-bridge
ip netns exec net1 ip link set dev net1-bridge up
ip netns exec net1 ip address add 10.0.1.2/24 dev net1-bridge
```

测试
```
$ ip netns exec net0 ping -c 3 10.0.1.2
PING 10.0.1.2 (10.0.1.2) 56(84) bytes of data.
64 bytes from 10.0.1.2: icmp_req=1 ttl=64 time=0.121 ms
64 bytes from 10.0.1.2: icmp_req=2 ttl=64 time=0.072 ms
64 bytes from 10.0.1.2: icmp_req=3 ttl=64 time=0.069 ms

--- 10.0.1.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.069/0.087/0.121/0.025 ms
```

== 配置lldpd检查线路链接情况

随着虚拟网络环境增加，环境中网卡数量也在不断增加，经常会忘记环境中哪些网卡连接到哪里，通过 lldp [2] 协议，我们可以清楚看到每个网卡连接到了哪些环境中的哪个网卡。

github 上有一个 lldp 在 linux 下的开源实现 [3]，通过在每个环境中起一个 lldp daemon，我们就可以实时查看每个网卡的连接情况

Bridge 上 lldp 的数据
```
$ lldpcli show neighbors

LLDP neighbors:

Interface:    bridge-net0, via: LLDP, RID: 2, Time: 0 day, 00:06:53
  Chassis:
    ChassisID:    mac 82:be:2a:ec:70:69
    SysName:      localhost
    SysDescr:     net0
    Capability:   Bridge, off
    Capability:   Router, off
    Capability:   Wlan, off
  Port:
    PortID:       mac 82:be:2a:ec:70:69
    PortDescr:    net0-bridge

Interface:    bridge-net1, via: LLDP, RID: 1, Time: 0 day, 00:06:53
  Chassis:
    ChassisID:    mac b2:34:28:b1:be:49
    SysName:      localhost
    SysDescr:     net1
    Capability:   Bridge, off
    Capability:   Router, off
    Capability:   Wlan, off
  Port:
    PortID:       mac b2:34:28:b1:be:49
    PortDescr:    net1-bridge
```

[1] http://linuxcontainers.org/
[2] http://en.wikipedia.org/wiki/Link_Layer_Discovery_Protocol
[3] https://github.com/vincentbernat/lldpd
Ref:
http://blog.kghost.info/2013/03/01/linux-network-emulator/
