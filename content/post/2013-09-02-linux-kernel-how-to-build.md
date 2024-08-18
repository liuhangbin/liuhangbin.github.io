---
layout: post
Title: linux kernel how to build
Date: 2013-09-02
tags: [linux, kernel]
---


=== How to build a kernel with src.rpm

1. install kernel-$version.src.rpm; cd rpmbuild/
1. dnf install -y rpm-build
2. rpmbuild -bp SPECS/kernel.spec
3. If you want to add a patch, cp your.patch SOURCES/linux-kernel-test.patch
4. uncomment # %define buildid .local and change local to you summary
5. dnf install -y flex bison bc gcc elfutils-libelf-devel elfutils-devel openssl-devel dwarves vim clang llvm git ldd-devel
6. dnf install -y libpcap-devel python3-docutils
5. rpmbuild -bb --target=`uname -m` --without debug --without doc --without \
headers --without perf --without debuginfo --without tools SPECS/kernel.spec


=== How to build a RHEL kernel
1. make rh-configs
2. cp redhat/configs/kernel-3.10.0-x86_64.config .config
3. make oldconfig
4. make -j28
5. make rh-check-kabi Or redhat/kabi/check-kabi -k redhat/kabi/Module.kabi_x86_64 -s Module.symvers

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

Or build an upstream kernel driver with clang

```
make -j28 scripts prepare modules_prepare CC=clang
make -C . M=drivers/net/wireguard/ CC=clang
```

Or only build a driver for testing:

```
mkdir build_dir && cp config build_dir/.config
make ARCH=x86_64 mrproper
make W=1 O=build_dir ARCH=x86_64 SHELL=/bin/bash drivers/net/bonding/
```

[1] https://www.kernel.org/doc/html/latest/kbuild/modules.html
[2] https://www.kernel.org/doc/html/latest/kbuild/llvm.html
