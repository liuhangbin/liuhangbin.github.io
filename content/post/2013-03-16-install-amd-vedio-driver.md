---
layout: post
Title: How to install AMD vedio driver glxinfo in linux
Date: 2013-03-16
tags: [Linux, AMD]
---

make sure you have installed Xorg, if not , # yum install -y Xorg

sh# lspci -v  // or just glxinfo | grep render ??

Download http://support.amd.com/cn/gpudownload/linux/Pages/radeon_linux.aspx
or 
sh# wget http://www2.ati.com/drivers/linux/amd-driver-installer-12-1-x86.x86_64.run

sh# chmod +x amd-driver-installer-12-1-x86.x86_64.run
sh# ./amd-driver-installer-12-1-x86.x86_64.run --listpkg // check which system supported
sh# ./amd-driver-installer-12-1-x86.x86_64.run --buildpkg RedHat/RHEL6
sh# yum localinstall -y fglrx_p_i_c-8.93-1.i386.rpm
sh# aticonfig --initial -f
sh# reboot

After reboot

# lspci -k  // make sure the adaptor have used your driver
