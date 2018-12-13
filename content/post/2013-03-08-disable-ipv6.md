---
layout: post
Title: How do I disable or enable the IPv6 protocol in RHEL
Date: 2013-03-08
tags: [Linux, IPv6]
---

Issue:
How do I disable or enable the IPv6 protocol in Red Hat Enterprise Linux?

When I attempt to disable IPv6 in RHEL5, IPv4 does not connect. Had to
re-enable IPv6 via console commands to connect to network.


Environmenta:
Red Hat Enterprise Linux 6
Red Hat Enterprise Linux 5
Red Hat Enterprise Linux 4

Resolution:

## Disabling IPv6 support in Red Hat Enterprise Linux 7
1. Create a new file named /etc/sysctl.d/ipv6.conf and add the following options:
```
# To disable for all interfaces
net.ipv6.conf.all.disable_ipv6 = 1
# the protocol can be disabled for specific interfaces as well.
net.ipv6.conf.<interface>.disable_ipv6 = 1
```
1. The new settings would then need to be reloaded with: `# sysctl -p`

## Disabling IPv6 support in Red Hat Enterprise Linux 6
1. Create a file /etc/modprobe.d/ipv6.conf with the following contents:
`options ipv6 disable=1`
1. For completeness, it is a good idea to configure the ip6tables service not
to start at boot by issuing the following command: `chkconfig ip6tables off`
1. Edit /etc/sysconfig/network and add the following line `NETWORKING_IPV6=no`
1. Disable IPv6 support in the kernel through /etc/sysctl.conf :
```
# IPv6 support in the kernel, set to 0 by default
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
```
1. Comment out any IPv6 addresses found in /etc/hosts, including ::1 localhost
address
```
# cp -p /etc/hosts /etc/hosts.disableipv6
# sed -i 's/^[[:space:]]*::/#::/' /etc/hosts
```
1. Optionally to prevent rpc.nfsd setting up IPv6 sockets while NFS server is
running, edit /etc/netconfig for the lines starting with udp6 and tcp6; change
the "v" in the third column to "-"(hyphen/dash)
1. Reboot the system to disable IPv6 support.

Note: The solution above only disables(unhooks all of the calls) the ipv6
module, but doesn't prevent it from loading. Unloading the module isn't
recommended due to the dependency factors it has with other modules(for
example: bonding) and services. To prevent the module from loading, modify
/etc/modprobe.d/ipv6.conf with the following only:
```
install ipv6 /bin/false
```

1. Disabling ipv6 in the sysctl.conf will ensure ipv6 isnt used even if the
ipv6 module is loaded and can work as a short term solution (until a full
reboot)
1. There is a special case where this might not work, please see The "ipv6
disable=1" option does not seem to work on Red Hat Enterprise Linux 6 system.
(https://access.redhat.com/knowledge/solutions/72733)

## Re-enabling IPv6 support in Red Hat Enterprise Linux 6
1. Review the files under /etc/modprobe.d/ and remove (or comment out) any of
the following lines:
```
options ipv6 disable=1
install ipv6 /bin/true
blacklist ipv6
alias ipv6 off
```
1. Configure the ip6tables service to start at boot by issuing the following
command: `chkconfig ip6tables on`
1. Edit /etc/sysconfig/network and add the line `NETWORKING_IPV6=yes`
1. Make sure the following options to your /etc/sysctl.conf are
```
# ipv6 support in the kernel, set to 0 by default
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
```
1. Make sure the following line exists in /etc/hosts, and is not commented
out.
```
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```
1. Edit /etc/netconfig for the lines starting with udp6 and tcp6; change the
"-"(hyphen/dash) in the third column to "v"
1. Reboot the system to activate IPv6 support.


Reference :
https://access.redhat.com/knowledge/solutions/8709
https://access.redhat.com/knowledge/solutions/72733
