---
layout: post
Title: linux kernel how to build
Date: 2013-09-02
tags: [linux, kernel]
---


=== How to build a kernel with src.rpm

1. install kernel-$version.src.rpm; cd rpmbuild/
2. rpmbuild -bp SPECS/kernel.spec
3. If you want to add a patch, cp your.patch SOURCES/linux-kernel-test.patch
4. uncomment # %define buildid .local and change local to you summary
5. rpmbuild -bb --target=`uname -m` --without debug --without doc --without
headers --without perf --without debuginfo --without tools SPECS/kernel.spec


=== How to build a kernel

1. git clone git://git.kernel.org/pub/scm/linux/kernel/git/davem/net-next.git
2. cd net-next
3. git am 0001-net-ipv6.... (attached patch)
4. make menuconfig (just exit and save)
5. make -j4
6. make modules_install -j4
7. make install
8. cd /boot/
9. dracut -f initramfs-3.11.0-rc6+.img 3.11.0-rc6+ (# ls /lib/modules/3.11.0-rc6+/)
10. update your grub to boot the correct kernel
11. reboot

If you want to rename the kernel, add a name in .config CONFIG_LOCALVERSION


=== How to build a driver

1. install kernel devel package
2. cd rpmbuild/BUILD/kernel-3.10.0-64.el7/linux-3.10.0-64.el7.x86_64/drivers/infiniband/ulp/ipoib/
3. make -C /usr/src/kernels/3.10.0-64.el7.x86_64/ M=${PWD} modules
