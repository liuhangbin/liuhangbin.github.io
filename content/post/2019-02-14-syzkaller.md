---
title: syzkaller
date: 2019-02-14
---

[syzkaller source code](https://github.com/google/syzkaller)

# Some tips about how to use syzkaller
how to unpack corpus
```
bin/syz-db unpack corpus.db corpus_dir
```

# How to setup build env before submit a patch

`Note`: in this part you need to download and install a lot devel packages,
syzkaller, kernel source code. Please leave at least 5G space for it.

When you want to write testcase for syzkaller, taking one of [my patch](https://github.com/google/syzkaller/commit/c2c0d4d6ae3a)
as example, you may feel confused about
how to wirte the .go files. The answer is the .go and .const files are
generated automatically, not by hand. Only the .txt files are your work.

Here I will introduce how to setup a build env (I use fedora as example,
ubuntu would be much easier) to generate this files before submit your patch.

1. install golang and download the source code

```
$ sudo dnf install -y golang
$ mkdir $PWD/src
$ export GOPATH=$PWD/src; export GOROOT=/usr/lib/golang
$ go get -v -u -d github.com/google/syzkaller/...
$ cd src/gopath/src/github.com/google/syzkaller/
```

2. Add your test cases.
In my patch I updated sys/linux/vnet.txt and
sys/linux/socket_netlink_route.txt. You can follow the [syzkaller
doc](https://github.com/google/syzkaller/blob/master/docs/contributing.md),
like sign Google CLA, fork the code to your github, checout a new branch
based on your repo, and write your test cases etc.

3. Install build dependencies.
After you finish your patch. You need to install the build dependencies now.
On ubuntu, it would be very easy, just run `make install_prerequisites`.
With Fedora, you need
```
$ sudo dnf install bc bison flex elfutils-libelf-devel clang glibc-devel \
	glibc-devel.i686 gcc-x86_64-linux-gnu gcc-ppc64le-linux-gnu \
	gcc-aarch64-linux-gnu
$ sudo ln -s /usr/bin/arm-linux-gnu-gcc /usr/bin/arm-linux-gnueabi-gcc
$ sudo ln -s /usr/bin/arm-linux-gnu-ld /usr/bin/arm-linux-gnueabi-ld
$ sudo ln -s /usr/bin/arm-linux-gnu-ld.bfd /usr/bin/arm-linux-gnueabi-ld.bfd
```

4. Download kernel sourcecode. This is used for generate the .const files

5. generate the .const and go files

```
$ make extract TARGETOS=linux SOURCEDIR=$your_kenrel_source_path
$ make generate
$ make presubmit
```

If all good, then you can `$ git push $your_repo $branch [--force]` and create
the PR.
