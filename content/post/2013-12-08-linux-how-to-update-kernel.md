---
layout: post
Title: linux how to update kernel
Date: 2013-12-18
tags: [linux, kernel]
---

### How to update s390x kernel

# grubby --set-default=/boot/vmlinuz-3.10.0-54.el7.s390x.debug
# grubby --default-kernel
/boot/vmlinuz-3.10.0-54.el7.s390x.debug
# zipl
Using config file '/etc/zipl.conf'
Run /lib/s390-tools/zipl_helper.device-mapper /boot
Building bootmap in '/boot'
Building menu 'zipl-automatic-menu'
Adding #1: IPL section '3.10.0-54.el7.s390x.debug' (default)
Adding #2: IPL section 'linux'
Preparing boot device: dasdb.
Done.

