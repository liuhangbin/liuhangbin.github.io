---
layout: post
Title: How to config router and switch
Date: 2013-08-28
tags: [Router, Switch]
---

Reference:
http://www.juniper.net/techpubs/en_US/junos11.4/topics/topic-map/mcast-mld.html

login: root
passwd: root123

root@% cli
root> configure
root#
Entering configuration mode

[edit]
lab# edit protocols

[edit protocols]
lab# set mld ?
Possible completions:
  <[Enter]>            Execute this command
  accounting           Enable join and leave event notification
+ apply-groups         Groups from which to inherit configuration data
+ apply-groups-except  Dont inherit configuration data from these groups
> interface            Interface options for MLD
  query-interval       When to send host query messages (1..1024 seconds)
  query-last-member-interval  When to send group query messages (seconds)
  query-response-interval  How long to wait for a host query response (seconds)
  robust-count         Expected packet loss on a subnet (2..10)
> traceoptions         Trace options for MLD
  |                    Pipe through a command
[edit protocols]

lab# set mld query-interval 150
lab# delete mld accounting
lab# activate protocols router-advertisement
lab# deactivate protocols router-advertisement

lab# set mld interface ge-0/0/0.0 ?
Possible completions:
  <[Enter]>            Execute this command
  accounting           Enable join and leave event notification
+ apply-groups         Groups from which to inherit configuration data
+ apply-groups-except  Dont inherit configuration data from these groups
  disable              Disable MLD on this interface
+ group-policy         Group filter applied to incoming MLD report messages
  immediate-leave      Group is removed immediately without sending query for last membership
  no-accounting        Dont enable join and leave event notification
  ssm-map              Map for SSM translation of MLDv1 messages
> static               Static group or source membership
  version              Set MLD version number on this interface (1..2)
  |                    Pipe through a command
[edit protocols]
lab# set mld interface ge-0/0/0.0 version 2

lab# show mld
query-interval 125;
query-response-interval 10;
query-last-member-interval 1;
robust-count 2;
accounting;
interface ge-0/0/0.0 {
    version 1;
}

[edit protocols]

lab# quit

[edit]
lab# show interfaces
ge-0/0/0 {
    link-mode full-duplex;
    unit 0 {
        family inet {
            address 192.168.3.13/24;
        }
        family inet6 {
            mtu 1500;
            address 3ffe:501:ffff:104::13/64;
        }
    }
}
ge-0/0/1 {
    link-mode full-duplex;
    unit 0 {
        family inet {
            address 192.168.4.13/24;
        }
        family inet6 {
            mtu 1280;
            address 3ffe:501:ffff:103::13/64;
        }
    }
}

[edit]

lab# commit
commit complete

[edit]

lab# commit
commit complete

[edit]

### How to monitor interface

## How to set multi interface

root@switch# wildcard range set interfaces xe-0/0/[1,3,5] apply-groups vlan-ports

### How to config lacp
{master:0}[edit]
root@switch# set chassis aggregated-devices ethernet device-count 2
{master:0}[edit interfaces]
root@switch# set ae0 aggregated-ether-options lacp passive
{master:0}[edit interfaces]
root@switch# set xe-0/0/20 ether-options 802.3ad ae0
{master:0}[edit interfaces]
root@switch# set xe-0/0/21 ether-options 802.3ad ae0
{master:0}[edit]
root@switch# show | diff
[edit]
+  chassis {
+      aggregated-devices {
+          ethernet {
+              device-count 2;
+          }
+      }
+  }
[edit interfaces xe-0/0/20]
+    ether-options {
+        802.3ad ae0;
+    }
[edit interfaces xe-0/0/21]
+    ether-options {
+        802.3ad ae0;
+    }
[edit interfaces]
+   ae0 {
+       aggregated-ether-options {
+           lacp {
+               passive;
+           }
+       }
+   }
{master:0}[edit]
root@switch# delete interfaces xe-0/0/20 unit 0
{master:0}[edit]
root@switch# delete interfaces xe-0/0/21 unit 0
{master:0}[edit]
root@switch# set interfaces ae0 unit 0 family ethernet-switching vlan member default
{master:0}[edit]
root@switch# commit

Ref: http://www.juniper.net/documentation/en_US/junos14.1/topics/task/configuration/link-aggregation-cli.html
