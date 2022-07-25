---
title: bpf skeleton
date: 2022-06-01
---

This blog's content is copied from: [BPF skeleton and BPF app lifecycle](https://nakryiko.com/posts/bcc-to-libbpf-howto-guide/#bpf-skeleton-and-bpf-app-lifecycle)
 and [libbpf -bootstrap](https://nakryiko.com/posts/libbpf-bootstrap/)

## bpf skeleton theory

Here is the main libbpf concepts and phases that each BPF application goes through.
BPF application consists of a set of BPF programs, either cooperating or
completely independent, and BPF maps and global variables, shared between all
BPF programs (allowing them to cooperate on a common set of data). BPF maps
and global variables are also accessible from user-space. BPF application
typically goes through the following phases:

  * `Open phase`: BPF object file is parsed: BPF maps, BPF programs, and
    global variables are discovered, but not yet created. After a BPF app is
    opened, it’s possible to make any additional adjustments (setting BPF
    program types, if necessary; pre-setting initial values for global
    variables, etc), before all the entities are created and loaded.

  * `Load phase`: BPF maps are created, various relocations are resolved, BPF
    programs are loaded into the kernel and verified. At this point, all the
    parts of a BPF application are validated and exist in kernel, but no BPF
    program is yet executed. After the load phase, it’s possible to set up
    initial BPF map state without racing with the BPF program code execution.

  * `Attachment phase`: This is the phase at which BPF programs get attached
    to various BPF hook points (e.g., tracepoints, kprobes, cgroup hooks,
    network packet processing pipeline, etc). This is the phase at which BPF
    starts performing useful work and read/update BPF maps and global
    variables.

  * `Tear down phase`: BPF programs are detached and unloaded from the kernel.
    BPF maps are destroyed and all the resources used by the BPF app are
    freed.

Generated BPF skeleton has corresponding functions to trigger each phase:

  * `<name>__open()` – creates and opens BPF application;
  * `<name>__load()` – instantiates, loads, and verifies BPF application parts;
  * `<name>__attach()` – attaches all auto-attachable BPF programs (it’s
    optional, you can have more control by using libbpf APIs directly);
  * `<name>__destroy()` – detaches all BPF programs and frees up all used
    resources.


## BPF code example

Now let's use the [bpf bootstrap](https://github.com/libbpf/libbpf-bootstrap) as example：

The bpf code `minimal.bpf.c`
```c
// SPDX-License-Identifier: GPL-2.0 OR BSD-3-Clause
/* Copyright (c) 2020 Facebook */
#include <linux/bpf.h>
#include <bpf/bpf_helpers.h>

char LICENSE[] SEC("license") = "Dual BSD/GPL";

int my_pid = 0;

SEC("tp/syscalls/sys_enter_write")
int handle_tp(void *ctx)
{
	int pid = bpf_get_current_pid_tgid() >> 32;

	if (pid != my_pid)
		return 0;

	bpf_printk("BPF triggered from PID %d.\n", pid);

	return 0;
}
```

Note there is a global variable `my_pid`. Let's build it to `.o` file and
generae the skeleton header file.
```
$ clang -O2 -g -Wall -target bpf -c minimal.bpf.c -o minimal.bpf.o

$ llvm-objdump -h minimal.bpf.o

minimal.bpf.o:  file format elf64-bpf

Sections:
Idx Name                            Size     VMA              Type
  0                                 00000000 0000000000000000
  1 .strtab                         000000fa 0000000000000000
  2 .text                           00000000 0000000000000000 TEXT
  3 tp/syscalls/sys_enter_write     000000c0 0000000000000000 TEXT
  4 .reltp/syscalls/sys_enter_write 00000010 0000000000000000
  5 license                         0000000d 0000000000000000 DATA
  6 .bss                            00000004 0000000000000000 BSS
  7 .rodata.str1.1                  0000001c 0000000000000000 DATA
  8 .debug_loc                      00000023 0000000000000000 DEBUG
  9 .debug_abbrev                   000000f1 0000000000000000 DEBUG
 10 .debug_info                     0000013f 0000000000000000 DEBUG
 11 .rel.debug_info                 000001b0 0000000000000000
 12 .debug_str                      000000e9 0000000000000000 DEBUG
 13 .BTF                            000001c9 0000000000000000
 14 .rel.BTF                        00000020 0000000000000000
 15 .BTF.ext                        000000a0 0000000000000000
 16 .rel.BTF.ext                    00000070 0000000000000000
 17 .debug_frame                    00000028 0000000000000000 DEBUG
 18 .rel.debug_frame                00000020 0000000000000000
 19 .debug_line                     000000ab 0000000000000000 DEBUG
 20 .rel.debug_line                 00000010 0000000000000000
 21 .llvm_addrsig                   00000002 0000000000000000
 22 .symtab                         00000120 0000000000000000

# strip useless DWARF info
$ llvm-strip -g minimal.bpf.o
$ llvm-objdump -h minimal.bpf.o

minimal.bpf.o:  file format elf64-bpf

Sections:
Idx Name                            Size     VMA              Type
  0                                 00000000 0000000000000000
  1 .text                           00000000 0000000000000000 TEXT
  2 tp/syscalls/sys_enter_write     000000c0 0000000000000000 TEXT
  3 license                         0000000d 0000000000000000 DATA
  4 .bss                            00000004 0000000000000000 BSS
  5 .rodata.str1.1                  0000001c 0000000000000000 DATA
  6 .BTF                            000001c9 0000000000000000
  7 .BTF.ext                        000000a0 0000000000000000
  8 .symtab                         00000090 0000000000000000
  9 .reltp/syscalls/sys_enter_write 00000010 0000000000000000
 10 .rel.BTF                        00000020 0000000000000000
 11 .rel.BTF.ext                    00000070 0000000000000000
 12 .llvm_addrsig                   00000002 0000000000000000
 13 .strtab                         00000097 0000000000000000

$ bpftool gen skeleton minimal.bpf.o > minimal.skel.h
$ cat minimal.skel.h
/* SPDX-License-Identifier: (LGPL-2.1 OR BSD-2-Clause) */

/* THIS FILE IS AUTOGENERATED! */
#ifndef __MINIMAL_BPF_SKEL_H__
#define __MINIMAL_BPF_SKEL_H__

#include <errno.h>
#include <stdlib.h>
#include <bpf/libbpf.h>

struct minimal_bpf {
        struct bpf_object_skeleton *skeleton;
        struct bpf_object *obj;
        struct {
                struct bpf_map *bss;
        } maps;
        struct {
                struct bpf_program *handle_tp;
        } progs;
        struct {
                struct bpf_link *handle_tp;
        } links;
        struct minimal_bpf__bss {
                int my_pid;
        } *bss;
};

static inline void minimal_bpf__destroy(struct minimal_bpf *obj) { ... }
static inline struct minimal_bpf *minimal_bpf__open_opts(const struct bpf_object_open_opts *opts) { ... }
static inline struct minimal_bpf *minimal_bpf__open(void) { ... }
static inline int minimal_bpf__load(struct minimal_bpf *obj) { ... }
static inline struct minimal_bpf *minimal_bpf__open_and_load(void) { ... }
static inline int minimal_bpf__attach(struct minimal_bpf *obj) { ... }
static inline void minimal_bpf__detach(struct minimal_bpf *obj) { ... }

#endif /* __MINIMAL_BPF_SKEL_H__ */
```

We can find the `struct minimal_bpf` and related functions are generated. The
variable `my_pid` is in `bss` section. The BPF maps can be accessed via libbpf
API.

Skeleton can also optionally have `bss`, `data`, and `rodata` sections that
allow _direct_ (no extra syscalls needed) access to BPF global variables from
user-space. In this case, our `my_pid` BPF variable corresponds to the
`bss->my_pid` field.


## User C code example

After generate the `skel.h` file. We can write the user C code like:

```c
$ cat minimal.c

// SPDX-License-Identifier: (LGPL-2.1 OR BSD-2-Clause)
/* Copyright (c) 2020 Facebook */
#include <stdio.h>
#include <unistd.h>
#include <sys/resource.h>
#include <bpf/libbpf.h>
#include "minimal.skel.h"

static int libbpf_print_fn(enum libbpf_print_level level, const char *format, va_list args)
{
	return vfprintf(stderr, format, args);
}

int main(int argc, char **argv)
{
	struct minimal_bpf *skel;
	int err;

	libbpf_set_strict_mode(LIBBPF_STRICT_ALL);
	/* Set up libbpf errors and debug info callback */
	libbpf_set_print(libbpf_print_fn);

	/* Open BPF application */
	skel = minimal_bpf__open();
	if (!skel) {
		fprintf(stderr, "Failed to open BPF skeleton\n");
		return 1;
	}

	/* ensure BPF program only handles write() syscalls from our process */
	skel->bss->my_pid = getpid();

	/* Load & verify BPF programs */
	err = minimal_bpf__load(skel);
	if (err) {
		fprintf(stderr, "Failed to load and verify BPF skeleton\n");
		goto cleanup;
	}

	/* Attach tracepoint handler */
	err = minimal_bpf__attach(skel);
	if (err) {
		fprintf(stderr, "Failed to attach BPF skeleton\n");
		goto cleanup;
	}

	printf("Successfully started! Please run `sudo cat /sys/kernel/debug/tracing/trace_pipe` "
	       "to see output of the BPF programs.\n");

	for (;;) {
		/* trigger our BPF program */
		fprintf(stderr, ".");
		sleep(1);
	}

cleanup:
	minimal_bpf__destroy(skel);
	return -err;
}
```

So basically just

```
struct minimal_bpf *skel;

skel = minimal_bpf__open();
err = minimal_bpf__load(skel);
err = minimal_bpf__attach(skel);

minimal_bpf__destroy(skel);

$ gcc -c minimal.c -o minimal
```

## More tools

### vmlinux.h
When developing complex tools, we need to use `vmlinux.h` header file, which
includes all the types from the Linux kernel in one file. We can generate it
via `bpftool btf dump file /sys/kernel/btf/vmlinux format c > vmlinux.h`

> All the types in `vmlinux.h` come with extra __attribute__((preserve_access_index)) applied, which makes Clang generate [BPF CO-RE](https://nakryiko.com/posts/bpf-portability-and-co-re/#reading-kernel-structure-s-fields) relocations, allowing libbpf to adapt your BPF code to the specific memory layout of the host kernel, even if it differs from the one that `vmlinux.h` was originally generated from. This is a crucial aspect of building portable pre-compiled BPF application that doesn't require entire Clang/LLVM toolchain to be deployed alongside it to the target system.

Keep in mind that `vmlinux.h` can't be combined with other system-wide kernel headers, as you'll inevitably run into type redefinitions and conflicts. So please stick with using just vmlinux.h, libbpf-provided headers, and your application's custom headers to avoid unnecessary headaches.

### Makefile

Here is an example Makefile

```
# SPDX-License-Identifier: (LGPL-2.1 OR BSD-2-Clause)
OUTPUT ?= ./output
CLANG ?= clang
LLVM_STRIP ?= llvm-strip
BPFTOOL ?= bpftool

INCLUDES := -I$(OUTPUT)
CFLAGS := -g -Wall -lbpf

APPS = minimal

.PHONY: all
all: $(APPS)

.PHONY: clean
clean:
	$(call msg,CLEAN)
	rm -rf $(OUTPUT) $(APPS)

$(OUTPUT):
	$(call msg,MKDIR,$@)
	mkdir -p $@

# Build BPF code
$(OUTPUT)/%.bpf.o: %.bpf.c $(wildcard %.h) | $(OUTPUT)
	$(call msg,BPF,$@)
	$(BPFTOOL) btf dump file /sys/kernel/btf/vmlinux format c > $(OUTPUT)/vmlinux.h
	$(CLANG) -g -O2 -target bpf $(INCLUDES) $(CLANG_BPF_SYS_INCLUDES) -c $(filter %.c,$^) -o $@
	$(LLVM_STRIP) -g $@ # strip useless DWARF info

# Generate BPF skeletons
$(OUTPUT)/%.skel.h: $(OUTPUT)/%.bpf.o | $(OUTPUT)
	$(call msg,GEN-SKEL,$@)
	$(BPFTOOL) gen skeleton $< > $@

# Build user-space code
$(patsubst %,$(OUTPUT)/%.o,$(APPS)): %.o: %.skel.h

$(OUTPUT)/%.o: %.c $(wildcard %.h) | $(OUTPUT)
	$(call msg,CC,$@)
	$(CC) $(CFLAGS) $(INCLUDES) -c $(filter %.c,$^) -o $@

# Build application binary
$(APPS): %: $(OUTPUT)/%.o | $(OUTPUT)
	$(call msg,BINARY,$@)
	$(CC) $(CFLAGS) $^ -lelf -lz -o $@

# delete failed targets
.DELETE_ON_ERROR:

# keep intermediate (.skel.h, .bpf.o, etc) targets
.SECONDARY:
```
