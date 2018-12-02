---
layout: post
Title: How to config router and switch
Date: 2013-08-28
tags: [Router, Switch]
---

Reference:
http://www.juniper.net/techpubs/en_US/junos11.4/topics/topic-map/mcast-mld.html

login: lab
passwd: lab123

lab> configure
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


