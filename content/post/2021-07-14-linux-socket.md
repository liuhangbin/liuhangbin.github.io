---
title: linux socket
date: 2021-07-14
---

The code in this article is based on Linux kernel 5.13.
This article is with reference to [socket-interface-and-network-protocol](https://lishiwen4.github.io/network/socket-interface-and-network-protocol) and [Send/Recv UDP](https://flylib.com/books/en/3.475.1.128/1/).

## 1. Socket

From man 2 socket
```
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>

int socket(int domain, int type, int protocol);
```

  * domain: like AF_IENT, AF_INET6, AF_NETLINK, AF_PACKET, see more with `man 7 address_families`, or see all domains defined in kernel file `include/linux/socket.h`
  * type: like SOCK_STREAM, SOCK_DGRAM, SOCK_RAW, see all sock_type in `include/linux/net.h`
  * protocol: usually 0, but may also have many protocols for one domain, e.g. AF_NETLINK(man 7 netlink)

From the syscall you will see socket is not bind a specific protocol, it only create an endpoint for communication.

Note: for every AF_XXX, there is an PF_XXX, and the values are same. AF_XXX is address family, which PF_XXX is protocol family. So please try use AF_XXX in your `struct sockaddr_in` and PF_XXX in your call to socket().

## 2. struct socket

From include/linux/net.h
```
/**
 *  struct socket - general BSD socket
 *  @state: socket state (%SS_CONNECTED, etc)
 *  @type: socket type (%SOCK_STREAM, etc)
 *  @flags: socket flags (%SOCK_NOSPACE, etc)
 *  @ops: protocol specific socket operations
 *  @file: File back pointer for gc
 *  @sk: internal networking protocol agnostic socket representation
 *  @wq: wait queue for several uses
 */
struct socket {
        socket_state            state;

        short                   type;

        unsigned long           flags;

        struct file             *file;
        struct sock             *sk;
        const struct proto_ops  *ops;

        struct socket_wq        wq;
};
```

  * file: fd used in userspace

### 2.1 struct net_proto_family

```
struct net_proto_family {
        int             family;
        int             (*create)(struct net *net, struct socket *sock,
                                  int protocol, int kern);
        struct module   *owner;
};
```

  * family: PF_XXX
  * create: create() syscall in kernel

e.g.
```
static const struct net_proto_family inet_family_ops = {
        .family = PF_INET,
        .create = inet_create,
        .owner  = THIS_MODULE,
};
```

Every protocol family need `sock_register()` to register its own `net_proto_family`.

```
  * net/ipv4/af_inet.c <<inet_init>>
    - sock_register(&inet_family_ops);
  * net/ipv6/af_inet6.c <<inet6_init>>
    - err = sock_register(&inet6_family_ops);
  * net/packet/af_packet.c <<packet_init>>
    - rc = sock_register(&packet_family_ops);
```

`sock_register()` add each protocol in here.
```
static const struct net_proto_family __rcu *net_families[NPROTO] __read_mostly;
```


### 2.2 struct proto_ops

Defines all protocol handlers

```
From include/linux/net.h

struct proto_ops {
        int             family;
        struct module   *owner;
        int             (*release)   (struct socket *sock);
        int             (*bind)      (struct socket *sock,
                                      struct sockaddr *myaddr,
                                      int sockaddr_len);
        int             (*connect)   (struct socket *sock,
                                      struct sockaddr *vaddr,
                                      int sockaddr_len, int flags);
        [...]
        int             (*sendmsg)   (struct socket *sock, struct msghdr *m,
                              size_t total_len);
        int             (*recvmsg)   (struct socket *sock, struct msghdr *m,
                              size_t total_len, int flags);
        [...]
```

### 2.4 struct proto

Defines all protocol specific handlers
```
From include/net/sock.h

struct proto {
        void                    (*close)(struct sock *sk,
                                        long timeout);
        int                     (*pre_connect)(struct sock *sk,
                                        struct sockaddr *uaddr,
                                        int addr_len);
        int                     (*connect)(struct sock *sk,
                                        struct sockaddr *uaddr,
                                        int addr_len);
        [...]

        int                     (*sendmsg)(struct sock *sk, struct msghdr *msg,
                                           size_t len);
        int                     (*recvmsg)(struct sock *sk, struct msghdr *msg,
                                           size_t len, int noblock, int flags,
                                           int *addr_len);
        [...]
```
Even in the same address family, there may have many protocols. So linux used `struct proto` to define specific handler. If not defined, then use the common handler in `struct proto_ops`. If the handler is same for different protocols, they can share the same `struct proto`.

Most address families only declare `struct proto`, but not define details.
But AF_INET and AF_IENT6 has many proto structs. e.g.

```
net/ipv4/af_inet.c:

static struct inet_protosw inetsw_array[] =
{
        {
                .type =       SOCK_STREAM,
                .protocol =   IPPROTO_TCP,
                .prot =       &tcp_prot,
                .ops =        &inet_stream_ops,
                .flags =      INET_PROTOSW_PERMANENT |
                              INET_PROTOSW_ICSK,
        },
        ...
};
```

In net_proto_family.create(), we need alloc sk by `sk_alloc()`. Here the sk_prot was assign suitable `prot` struct.e.g. `inet_create() -> sk_alloc()`, which `prot`s are from the `inetsw_array`.

```
/**
 *      sk_alloc - All socket objects are allocated here
 *      @net: the applicable net namespace
 *      @family: protocol family
 *      @priority: for allocation (%GFP_KERNEL, %GFP_ATOMIC, etc)
 *      @prot: struct proto associated with this new sock instance
 *      @kern: is this to be a kernel socket?
 */
struct sock *sk_alloc(struct net *net, int family, gfp_t priority,
                      struct proto *prot, int kern)
{
        struct sock *sk;
        [...]
        sk->sk_prot = sk->sk_prot_creator = prot;
        [...]
```

If sk->sk_prot implemented, then it could be called in `proto_ops`. e.g.
`IPPROTO_ICMP` has `prot = &ping_prot` and `ops = &inet_sockraw_ops`. When call recvmsg, it calls `inet_recvmsg()` which defined in `inet_sockraw_ops`, then `sk->sk_prot->recvmsg` was called later.

For every `struct prot`, we need to call `proto_register()` to register it
in `proto_list`. You can read `/proc/net/protocols` to see them. e.g.

```
static int __init inet_init(void)
{
  [...]
  rc = proto_register(&tcp_prot, 1);
  rc = proto_register(&udp_prot, 1);
  rc = proto_register(&raw_prot, 1);
  rc = proto_register(&ping_prot, 1);
  [...]
}
```

## 3. Create socket

```
/* userspace */
socket()

/* kernel */ net/socket.c
SYSCALL_DEFINE3(socket, int, family, int, type, int, protocol)
  - __sys_socket()
    - sock_create()
      - __sock_create()
        - pf->create()  /* net_proto_family->create() for AF_INET */
    - sock_map_fd()

int __sock_create(struct net *net, int family, int type, int protocol,
                  struct socket **res, int kern)
{
       [...]
       sock = sock_alloc()
       [...]
       sock->type = type;
       [...]
       /* get correspond net_proto_family*/
       pf = rcu_dereference(net_families[family]);
       [...]
       /* Call net_proto_family create() function. e.g. inet_family_ops -> inet_create() */
       err = pf->create(net, sock, protocol, kern);
       [...]
}
```

As in userspace we use fd to describe the socket, we should bind the socket and file describer in kernel.
```
- sock_map_fd()
  - sock_alloc_file()
    - file = alloc_file_pseudo(SOCK_INODE(sock), sock_mnt, dname,
                              O_RDWR | (flags & O_NONBLOCK),
                              &socket_file_ops)
      - file = alloc_file(&path, flags, fops);
        - file->f_op = fop;
```
So, socket_file_ops will handle the operations from user space socket fd.

## 4. Socket handlers

```
- read()
  - SYSCALL_DEFINE3(read, ...)  /* fs/read_write.c */
    - ksys_read()
      - vfs_read()
        - file->f_op->read_iter(file, buf, count, pos) /* For socket, it's socket_file_ops->read_iter() */
          - sock_read_iter()
            - sock_recvmsg()
              - sock_recvmsg_nosec()
                - sock->ops->recvmsg()
                  - tcp_prot: tcp_recvmsg()
                    udp_prot: udp_recvmsg()
- write()
  - SYSCALL_DEFINE3(write, ...) /* fs/read_write.c */
    - ksys_write()
      - vfs_write()
        - file->f_op->write_iter()
          - sock_write_iter()
            - sock_sendmsg()
- ioctl()
  - SYSCALL_DEFINE3(ioctl, ...) /* fs/ioctl.c */
    - do_vfs_ioctl()
      - vfs_ioctl()
        - filp->f_op->unlocked_ioctl()
          - sock_ioctl()
            - dev_ioctl()
```

Note: some of the ioctl in socket fd are related to network interface, and some are related to network protocol. Therefore, starting from vfs_ioctl(), the ioctl cmds is handled in each layer function.

## 5. AF_INET

For AF_IENT, `struct net_proto_family inet_family_ops` is declared.

### 5.1 protocols in AF_IENT

For different socket type, there are different proto_ops. e.g.
```
const struct proto_ops inet_stream_ops = {
        .family            = PF_INET,
        .owner             = THIS_MODULE,
        .release           = inet_release,
        .bind              = inet_bind,
        .connect           = inet_stream_connect,
        .socketpair        = sock_no_socketpair,
        .accept            = inet_accept,
        ...
}
const struct proto_ops inet_dgram_ops = {
        .family            = PF_INET,
        .owner             = THIS_MODULE,
        .release           = inet_release,
        .bind              = inet_bind,
        ...
}
static const struct proto_ops inet_sockraw_ops = {
        .family            = PF_INET,
        .owner             = THIS_MODULE,
        .release           = inet_release,
        .bind              = inet_bind,
        ...
}
```
As you can see these proto_ops are defined for SOCK_STREAM, SOCK_DGRAM and SOCK_RAW respectively.

For an address family, multiple network protocols can be supported, the common method can be stored in struct proto_ops, and the method of the network protocol itself can be stored in proto. i.e.

soket.ops: store `struct proto_ops`
socket.sk.sk_prot: store `struct proto`.

There are some proto structs defined in AF_INET. e.g. tcp, udp, ping, raw.
```
net/ipv4/af_inet.c:

static struct inet_protosw inetsw_array[] =
{
        {
                .type =       SOCK_STREAM,
                .protocol =   IPPROTO_TCP,
                .prot =       &tcp_prot,
                .ops =        &inet_stream_ops,
                .flags =      INET_PROTOSW_PERMANENT |
                              INET_PROTOSW_ICSK,
        },

        {
                .type =       SOCK_DGRAM,
                .protocol =   IPPROTO_UDP,
                .prot =       &udp_prot,
                .ops =        &inet_dgram_ops,
                .flags =      INET_PROTOSW_PERMANENT,
       },

       {
                .type =       SOCK_DGRAM,
                .protocol =   IPPROTO_ICMP,
                .prot =       &ping_prot,
                .ops =        &inet_sockraw_ops,
                .flags =      INET_PROTOSW_REUSE,
       },

       {
               .type =       SOCK_RAW,
               .protocol =   IPPROTO_IP,        /* wild card */
               .prot =       &raw_prot,
               .ops =        &inet_sockraw_ops,
               .flags =      INET_PROTOSW_REUSE,
       }
};
```

### 5.2 struct inet_protosw

In AF_INET, `struct inet_protosw` is used to show the protocol related info.

```
/* This is used to register socket interfaces for IP protocols.  */
struct inet_protosw {
        struct list_head list;

        /* These two fields form the lookup key.  */
        unsigned short   type;     /* This is the 2nd argument to socket(2). */
        unsigned short   protocol; /* This is the L4 protocol number.  */

        struct proto     *prot;
        const struct proto_ops *ops;

        unsigned char    flags;      /* See INET_PROTOSW_* below.  */
};
```
All the protocols infos are defined in `static struct inet_protosw inetsw_array[]`, with `struct inet_protosw`, the socket type, protocol and related `struct proto`, `struct proto_ops` are combined together.

`inet_register_protosw()` is called to register these inet_protosw structs.
All of them are registerd in static list `static struct list_head inetsw[SOCK_MAX]`.

`inetsw is an array that stores a linked list header for each socket type, that is, the inet_protosw structure is classified according to the socket type and placed in different linked lists`

### 5.3 AF_IENT socket create.

This is followed with `3. Create socket`.

In net_proto_family.create(), it will assign proto_ops to socket.ops. e.g.
```
static int inet_create(struct net *net, struct socket *sock, int protocol,
                       int kern)
{
  ...
  list_for_each_entry_rcu(answer, &inetsw[sock->type], list) {
    ...
    /* Check the non-wild match. */
    if (protocol == answer->protocol) {
            if (protocol != IPPROTO_IP)
                    break;
    } else {
            /* Check for the two wild cases. */
            if (IPPROTO_IP == protocol) {
                    protocol = answer->protocol;
                    break;
            }
            if (IPPROTO_IP == answer->protocol)
                    break;
    }
    ...
  }
  [...]
  sock->ops = answer->ops;
  answer_prot = answer->prot;
  [...]
  sk = sk_alloc(net, PF_INET, GFP_KERNEL, answer_prot, kern);
  [...]
  sock_init_data(sock, sk);
  [...]
  if (sk->sk_prot->init)
    err = sk->sk_prot->init(sk);
  ...
}
```

  1. Find correspond `struct inet_protosw` based on protocol in `inetsw[sock->type]`.
  1. Assign the inet_protosw.ops and inet_protosw.prot to sock->ops, socket->sk.sk_prot.
  1. Call `socket->sk->sk_prot->init()`

### 5.4 socket handlers in AF_INET

In `4. Socket handler`, we know for each read/write, it will calls
sock->ops->xxx at last. And this will calls socket->sk->sk_prot->xxx. e.g.
```
/* userspace */
connect()

/* Kernel */
- connect()
  - SYSCALL_DEFINE3(connect, ...)
    ...
    - socket->ops->connect()  /* It's inet_stream_ops.connect() for tcp */
      - inet_stream_connect()
        - __inet_stream_connect()
          - sk->sk_prot->connect()
            - tcp_v4_connect()
```

## 6. Kernel UDP send/recvmsg

The sending of UDP packets, starting from the system call at the socket interface and running all the way until the completed packet is added to the output queue of the network interface, is handled in just one pass.

But the receiving of UDP packets requires two separate steps: Once a packet has been received, `udp_rcv()` first allocates it to a socket in bottom-half context and places it into that socket's receive queue. From there, the packet is fetched via system call of a user process, which is mapped to `udp_recvmsg()`.

### 6.1 UDP sendmsg

```
/* user space */
- sendmsg(int sockfd, const struct msghdr *msg, int flags)

/* kernel part */
- SYSCALL_DEFINE3(sendmsg, ...)
  - __sys_sendmsg()
    - sock_sendmsg(sock, msg_sys)
      - sock_sendmsg_nosec()
        - sock->ops->sendmsg()  /* For UDP, it's inet_dgram_ops->sendmsg() */
          - inet_sendmsg()
            - sk->sk_prot->sendmsg()  /* udp_prot->sendmsg() */
              - udp_sendmsg()
                - if (msg->msg_controllen): ip_cmsg_send()
                - if (connected): rt = (struct rtable *)sk_dst_check();
                  else: rt = ip_route_output_flow(net, fl4, sk);
                - udp_send_skb()
                  - ip_send_skb()
                    - ip_local_out()
                      - skb_dst(skb)->output()  /* rt_dst_alloc(): rt->dst.output = ip_output; */
                        - ip_output()
                          - ip_finish_output()
                            - __ip_finish_output()
                              - ip_finish_output2()
                                - neigh_hh_output()
                                  - dev_queue_xmit()
```

### 6.2 UDP recvmsg

As we said, The recvmsg has 2 steps, one is kernel part, after `udp_rcv` all the messages are queued in the buffer. Another step is from user space, which calls recvmsg() to get the data in kernel buffer.

```
/* Kernel part */
- NET_RX_SOFTIRQ
  - net_rx_action()
    - napi_poll()
      - n->poll() /* sd->backlog.poll = process_backlog; */
        - process_backlog()
          - __netif_receive_skb()
            - __netif_receive_skb_one_core()
              - pt_prev->func() /* see following ip_packet_type */
                - ip_rcv()
                  - ip_rcv_finish()
                    - dst_input()
                      - skb_dst(skb)->input /* rt->dst.input = ip_local_deliver; */
   ------------------------^
   |
- ip_local_deliver()
  - ip_local_deliver_finish()
    - ip_protocol_deliver_rcu()
      - ipprot = rcu_dereference(inet_protos[protocol]);
        - ipprot->handler() /* see the following udp_protocol struct */
          - udp_rcv()
            - __udp4_lib_rcv()
              - sk = __udp4_lib_lookup_skb()
              - udp_unicast_rcv_skb(sk, skb, uh)
                - udp_queue_rcv_skb()
                  - udp_queue_rcv_one_skb()


/* User part */
- recvmsg()
  - SYSCALL_DEFINE3(recvmsg, ...)
    - __sys_recvmsg()
      ...
      - socket->ops->recvmsg()  /* inet_dgram_ops->recvmsg() */
        - inet_recvmsg()
          - sk->sk_prot->recvmsg()  /* udp_prot->recvmsg() */
            - udp_recvmsg()
              - skb_copy_datagram_msg()
```

Call backs for `truct packet_type` and `struct net_protocol`.
```
static struct packet_type ip_packet_type __read_mostly = {
        .type = cpu_to_be16(ETH_P_IP),
        .func = ip_rcv,
        .list_func = ip_list_rcv,
};

static struct net_protocol udp_protocol = {
        .early_demux =  udp_v4_early_demux,
        .early_demux_handler =  udp_v4_early_demux,
        .handler =      udp_rcv,
        .err_handler =  udp_err,
        .no_policy =    1,
};

```
