---
title: libguestfs
date: 2020-07-07
---

[libguestfs](http://libguestfs.org/) is a tools for accessing and modifying virtual machine disk images.
and [virt-builder](http://libguestfs.org/virt-builder.1.html) is a very useful tool to build a virtual image.

### Installation and Usage
```
sudo dnf install libguestfs-tools
virt-builder -l
virt-builder fedora-31 -o /home/fedora-31.qcow2 --size 32G --format qcow2 --arch x86_64 --root-password password:redhat --ssh-inject root --install dnf,wget,vim,git,openssl,iproute
```


### Issues
Q: When run `virt-builder -l`, got failure
virt-builder: error: external command ‘'curl' -q --config ...  exited with error 56
A: some link was baned by China, comment archive.libguestfs.org in
/etc/virt-builder/repos.d/libguestfs.conf should fix this issue
