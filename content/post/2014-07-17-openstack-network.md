---
layout: post
title: openstack network
date: 2014-07-17
description:
category:
Tags: [Linux, openstack, network]
---

1. Local

2. Vlan
  1. Vlan Compute node
![vlan_compute](/media/pic/openstack/vlan_compute.png)
    1. qbrXXX: linux bridge, q-quantum, br-bridge
    1. qvbXXX/qvoXXX: a veth pair devices, connect bridge and OVS
    	q-quantum, v-veth, b-bridge, o-open vswitch
    1. int-br-eth1 and phy-br-eth1
        a veth pair devices, connect br-int and br-eth1
  2. Vlan network node
![vlan_network](/media/pic/openstack/vlan_network.png)
    1. qr-YYY and qg-VVV: q-quantum, r-router, g-gateway
        qr-YYY have a internal ip, and qg-VVV have an external ip, and they
	are connected by iptables NAT rules.
    1. phy-br-ex and int-br-ex
       All packets must go through a physnet, even though qr and qg have nat,
       they still need to connected by a veth pair.




# [OpenStack Networking ("Neutron")](https://wiki.openstack.org/wiki/Neutron)

## OpenStack Networking Overview

### OpenStack Networking Architecture
![Example network diagram](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/5/html/Installation_and_Configuration_Guide/images/5113.png)

### OpenStack Networking Plug-ins
* Open vSwitch (openstack-neutron-openvswitch)
* Linux Bridge (openstack-neutron-linuxbridge) 
* Cisco (openstack-neutron-cisco)
* NEC OpenFlow (openstack-neutron-nec)
* VMware (openstack-neutron-vmware)
* Ryu (openstack-neutron-ryu) 

### Modular Layer 2 (ML2) Overview
* The requirement behind ML2
* ML2 network types
    * flat
    * GRE
    * local
    * VLAN
    * VXLAN
* ML2 Mechanisms
    * Arista
    * Cisco
    * Nexus
    * Hyper-V Agent
    * L2 Population
    * Linuxbridge Agent
    * Open vSwitch Agent
    * Tail-f NCS 



Reference:

[Neutron Administrator Guide](http://docs.openstack.org/admin-guide-cloud/content/ch_networking.html)

[Nova](http://www.ibm.com/developerworks/cn/cloud/library/1402_chenhy_openstacknetwork/)

[OpenStack OVS GRE/VXLAN网络](http://blog.sina.com.cn/s/blog_6de3aa8a0101pfgz.html)

[ openstack 网络架构](http://blog.csdn.net/beginning1126/article/details/41172365)

[RedHat Doc](https://access.redhat.com/documentation/zh-CN/Red_Hat_Enterprise_Linux_OpenStack_Platform/)

[OVS scenario](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/5/html/Cloud_Administrator_Guide/section_networking-scenarios.html#under_the_hood_openvswitch)

[Install Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/5/html/Installation_and_Configuration_Guide/chap-OpenStack_Networking_Service_Installation.html)

