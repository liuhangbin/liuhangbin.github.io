---
title: How to compile llvm for BPF
date: 2021-04-22
---

My steps:

```
dnf install -y cmake ninja-build gcc gcc-c++
git clone https://github.com/llvm/llvm-project.git
cd llvm-project/
git checkout -b llvmorg-13 llvmorg-13-init
cd llvm/
mkdir build_13
cd $!
cmake -G "Ninja" -DLLVM_TARGETS_TO_BUILD="BPF;X86" \
   -DLLVM_ENABLE_PROJECTS="clang" \
   -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr ..
ninja -j20
ninja install
clang -v
```

---
Here is the message from Jesper

(The commands I use listed below are indented, and not prefix with $
because it makes it easier to copy-paste into your cmdline. You likely
need to install packages ninja and stow).

Getting git-code and create branch:

 cd ~/git
 git clone https://github.com/llvm/llvm-project.git
 cd ~/git/llvm-project
 git checkout -b llvm-12-git02-jackman-6f4ee6f87060 6f4ee6f87060

In this case, I use git commit recommended[1] by Brendan Jackman
<jackmanb@google.com> as once again upstream broke BPF selftests.
And the reason was he added some atomic stuff to compiler.

Next trick, I want to handle multiple version of LLVM on my system.
Thus, I change the install path when I generate the cmake file.

 export INSTDIR=/usr/local/stow/llvm-12-git02-jackman-6f4ee6f87060
 mkdir $INSTDIR

It was Eelco that show me 'ninja' make system.  Notice that I limit the
compiler targets to be BPF focused.

 cd ~/git/llvm-project
 cd llvm
 mkdir build-12-git02
 cd !$
 cmake -G "Ninja" -DLLVM_TARGETS_TO_BUILD="BPF;X86" \
    -DLLVM_ENABLE_PROJECTS="clang" \
    -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$INSTDIR ..

Above created the make file, and we are ready to compile.  I'm using
'ninja' cmdline, but this time I had to limit it to 6 parallel threads,
because my machine doesn't have enough memory.

 ninja -j6
 ninja install

Due to above INSTDIR the install target install the files under:
 /usr/local/stow/llvm-12-git02-jackman-6f4ee6f87060

I use the old UNIX tool[2] 'stow' to handle and choose which LLVM version
I want to have active (via symlinks in /usr/local).

 cd /usr/local/stow/
 ll /usr/local/bin/llc
 lrwxrwxrwx. 1 root root 48 Jan 18 13:46 /usr/local/bin/llc -> ../stow/llvm-11.1.0-rc1-git-9bbcb554cdbf/bin/llc

I see that current version is llvm-11.1.0-rc1-git-9bbcb554cdbf, so I
first remove (symlinks) to that version and enable the new recently
installed version instead:

 sudo stow -D llvm-11.1.0-rc1-git-9bbcb554cdbf
 sudo stow    llvm-12-git02-jackman-6f4ee6f87060
