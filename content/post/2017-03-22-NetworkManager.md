---
layout: post
title: NetworkManager
date: 2017-03-22
description:
category:
Tags: []
---

# nmcli connection show
# nmcli connection show eth0
# nmcli device status

# nmcli connection add con-name eth1 type ethernet ifname eth1
# nmcli connection modify eth1 ip4 192.168.1.1/24 gw4 192.168.1.254

# nmcli con modify eth1 connection.lldp enable
# nmcli -f all device lldp
# nmcli device lldp list ifname eth1
