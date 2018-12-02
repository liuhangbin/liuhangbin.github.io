---
layout: post
Title: ESXi Create Vlan
Date: 2013-04-12
tags: [Vmware, ESXi]
---


Steps to add vm network for ESXi server:

1) Left click you host (not virtual machine), choose "configuration" on the
right panel.
2) Choose Hardware -> networking, you can see there is only one vm network by
default.
3) click Add Networking -> Virtual Machine -> Create a vSphere standed switch,
there is no need to add a vlan id, just click next and finish adding it.
4) Edit you new vm network Properites and make promicuous mode accpeted.
5) Now you have a specific vm network, choose TN and NUT, and edit virtual
machine setting, add a testing NIC which network lab is your new vm network.
6) Modify you tn.def and nut.def, rerun you test.

Reference:
http://www.rustyhann.com/
http://www.rustyhann.com/ESXi-06-Create-vLAN.pdf
http://blog.chinaunix.net/uid-1730385-id-3087725.html
