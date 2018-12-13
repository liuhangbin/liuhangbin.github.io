---
layout: post
title: linux kernel ipv6
date: 2014-08-30
description:
category:
Tags: [linux, kernel, ipv6]
---

### Code flow chart

sendto     recvfrom

+----------------------------------------------------------------------+
|                                                                      |
|       +--------+                         +-------------+             |
|       | sendto |       Application       |  recvfrom   |             |
|       +----+---+                         +------+------+             |
|            |                                    |                    |
+------------+------------------------------------+--------------------+
             |                                    |
-------------+------------------------------------+----------------------
             |                                    |
+------------v------------------------------------v--------------------+
|                       Socket Interface                               |
+-----+---+------------------------------------------------------------+
      |   +------------------------------------+              ^  ^
      |                      +-----------------+--------------+  |
+-----+----------------------+-------+ +-------+-----------------+---------+
|     |                      |       | |       |                 |         |
|     |  UDP         +-------+-----+ | |       |  TCP      +-----+-------+ |
|     |              | udp_recvmsg | | |       |           | tcp_recvmsg | |
|   +-v-----------+  +-------------+ | |+------v------+    +-------------+ |
|   | udp_sendmsg |  +-------------+ | || tcp_sendmsg |    +-------------+ |
|   +------+------+  |    udp_rcv  | | |+------+------|    |  tcp_v4_rcv | |
|          |         +-------------+ | |       |           +-------------+ |
|          |                    ^    | |       |                 ^         |
+----------+--------------------+----+ +-------+-----------------+---------+
+----------+--------------------+--------------+-----------------+---------+
|          v                    |              v                 |         |
| +------------------------+    |        +---------------+       |         |
| | ip_push_pending_frames |    |        | ip_queue_xmit |       |         |
| +----+-------------------+    |        +-----+---------+       |         |
|      | +----------------------+--------------+                 |         |
|      | | +----------------+   +-----------------------+        |         |
|      v v v                |                           |        |         |
|  +-----------+      +-----+-------------+     +-------+--------+--------+|
|  | ip_output |      | ip_forward_finish |     | ip_local_deliver_finish ||
|  +---+-------+      +-------------------+     +-------------------------+|
|      v                           ^                        ^              |
|+-------------------+       +-----+------+         +-------+----------+   |
|| ip_finish_output2 |       | ip_forward |         | ip_local_deliver |   |
|+-----+-------------+       +------------+         +------------------+   |
|      |                           ^                        ^              |
|      v                           +-------------------+    |              |
| +---------------------+                           +--+----+-------+      |
| | Neighbor sub system |                           | ip_rcv_finish |      |
| +--------------+------+                           +---------------+      |
|                |                                       ^                 |
|                |                                  +----+----+            |
|     IP         |                                  | ip_rcv  |            |
|                |                                  +---------+            |
|                |                                       ^                 |
+----------------+---------------------------------------+-----------------+
                 |                                       |
+----------------v---------------------------------------+-----------------+
|        +----------------+                         +----+-----+           |
|        | dev_queue_xmit |    Driver Interface     | netif_rx |           |
|        +-------+--------+                         +----------+           |
|                |                                       ^                 |
+----------------+---------------------------------------+-----------------+
                 |                                       |
+----------------v---------------------------------------+-----------------+
|        +------------------                         +---+----+            |
|        | net_send_packet |       Driver            | net_rx |            |
|        +-----------------+                         +--------+            |
+--------------------------------------------------------------------------+





#### struct sk_buff   // include/linux/skbuff.h


+-----------------------------------------------------------------------------+
|                                                                             |
|  +-----------------------------------------------------------------------+  |
|  |  +------+               +------+          +------+          +------+  |  |
+--+->| next |-------------->| next |--------->| next |--------->| next |--+--+
   |  +------+               +------+          +------+          +------+  |
   +--| prev |<--------------| prev |<---------| prev |<---------| prev |<-+
      +------+               +------+          +------+          +------+
      | qlen |               | list |          | list |          | list |
      +------+               +------+          +------+          +------+
      | lock |               | sk   |          | sk   |          | sk   |
      +------+               +------+          +------+          +------+
                             | ...  |          | ...  |          | ...  |
                             +------+          +------+          +------+
                             | tail |          | tail |          | tail |
                             +------+          +------+          +------+
                             | end  |          | end  |          | end  |
                             +------+          +------+          +------+
                             | head |          | head |          | head |
                             +------+          +------+          +------+
                             | data |          | data |          | data |
                             +------+          +------+          +------+


   struct sk_buff_head             struct sk_buff





                            +------+      +----------+
                            | head |      | headroom |
                            +------+      +----------+
                            | data |      |          |
                            +------+      |  data    |
                            | tail |      |          |
                            +------+      +----------+
                            | end  |      | tailroom |
                            +------+      +----------+


#### struct net_device  // include/linux/netdevice.h






            name: eth0

            name_hlist

              state

              next ?

              priv ?

              ....

            netdev_ops

            ethtool_ops

             fwd_ops

              ....




#### network device create/rigister

alloc_etherdev  // include/linux/etherdevice.h
alloc_netdev_mqs        // net/core/dev.c

e1000_init_module      // drivers/net/ethernet/intel/e1000/e1000_main.c
   |
   +--> pic_register_driver(&e1000_driver)
           |
           +--> e1000_prob
                   |
                   +--> alloc_etherdev

                           +------------------+
                           |   init_module    |
                           +------------------+
                                    |
                                    v
+--------------------+     +------------------+       +-----------------+
| alloc_etherdev     |<----| do_netcard_probe |------>| rigister_netdev |
+---------+----------+     +------------------+       +-----------------+
          v
+--------------------+
| alloc_etherdev_mqs |
+---------+----------+
          v
+--------------------+
| alloc_netdev_mqs   |
+---------+----------+
          v
+--------------------+
|    setup           |
+--------------------+


### Reference

struct sk_buff_head	# include/linux/skbuff.h


### network socket

net/ipv4/af_inet.c
static const struct net_proto_family inet_family_ops = {
        .family = PF_INET,
        .create = inet_create,
        .owner  = THIS_MODULE,
};

/* Upon startup we insert all the elements in inetsw_array[] into
 * the linked list inetsw.
 */
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
                .ops =        &inet_dgram_ops,
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


const struct proto_ops inet_stream_ops = {
        .family            = PF_INET,
        .owner             = THIS_MODULE,
        .release           = inet_release,
        .bind              = inet_bind,
        .connect           = inet_stream_connect,
        .socketpair        = sock_no_socketpair,
        .accept            = inet_accept,
        .getname           = inet_getname,
        .poll              = tcp_poll,
        .ioctl             = inet_ioctl,
        .listen            = inet_listen,
        .shutdown          = inet_shutdown,
        .setsockopt        = sock_common_setsockopt,
        .getsockopt        = sock_common_getsockopt,
        .sendmsg           = inet_sendmsg,
        .recvmsg           = inet_recvmsg,
        .mmap              = sock_no_mmap,
        .sendpage          = inet_sendpage,
        .splice_read       = tcp_splice_read,
#ifdef CONFIG_COMPAT
        .compat_setsockopt = compat_sock_common_setsockopt,
        .compat_getsockopt = compat_sock_common_getsockopt,
        .compat_ioctl      = inet_compat_ioctl,
#endif
};
EXPORT_SYMBOL(inet_stream_ops);


net/ipv4/tcp_ipv4.c
struct proto tcp_prot = {
        .name                   = "TCP",
        .owner                  = THIS_MODULE,
        .close                  = tcp_close,
        .connect                = tcp_v4_connect,
        .disconnect             = tcp_disconnect,
        .accept                 = inet_csk_accept,
        .ioctl                  = tcp_ioctl,
        .init                   = tcp_v4_init_sock,
        .destroy                = tcp_v4_destroy_sock,
        .shutdown               = tcp_shutdown,
        .setsockopt             = tcp_setsockopt,
        .getsockopt             = tcp_getsockopt,
        .recvmsg                = tcp_recvmsg,
        .sendmsg                = tcp_sendmsg,
...
        .h.hashinfo             = &tcp_hashinfo,
        .no_autobind            = true,
#ifdef CONFIG_COMPAT
        .compat_setsockopt      = compat_tcp_setsockopt,
        .compat_getsockopt      = compat_tcp_getsockopt,
#endif
#ifdef CONFIG_MEMCG_KMEM
        .init_cgroup            = tcp_init_cgroup,
        .destroy_cgroup         = tcp_destroy_cgroup,
        .proto_cgroup           = tcp_proto_cgroup,
#endif
};
EXPORT_SYMBOL(tcp_prot);

net/ipv4/af_inet6.c
static const struct net_proto_family inet6_family_ops = {
        .family = PF_INET6,
        .create = inet6_create,
        .owner  = THIS_MODULE,
};

net/ipv6/tcp_ipv6.c

tcpv6_init() -> int6_add_protocol(tcpv6_protocol)
             -> inet6_register_protosw(tcpv6_protosw)

static const struct inet6_protocol tcpv6_protocol = {
        .early_demux    =       tcp_v6_early_demux,
        .handler        =       tcp_v6_rcv,
        .err_handler    =       tcp_v6_err,
        .flags          =       INET6_PROTO_NOPOLICY|INET6_PROTO_FINAL,
};

static struct inet_protosw tcpv6_protosw = {
        .type           =       SOCK_STREAM,
        .protocol       =       IPPROTO_TCP,
        .prot           =       &tcpv6_prot,
        .ops            =       &inet6_stream_ops,
        .flags          =       INET_PROTOSW_PERMANENT |
                                INET_PROTOSW_ICSK,
};


struct proto tcpv6_prot = {
        .name                   = "TCPv6",
        .owner                  = THIS_MODULE,
        .close                  = tcp_close,
        .connect                = tcp_v6_connect,
        .disconnect             = tcp_disconnect,
        .accept                 = inet_csk_accept,
        .ioctl                  = tcp_ioctl,
        .init                   = tcp_v6_init_sock,
        .destroy                = tcp_v6_destroy_sock,
        .shutdown               = tcp_shutdown,
        .setsockopt             = tcp_setsockopt,
        .getsockopt             = tcp_getsockopt,
        .recvmsg                = tcp_recvmsg,
        .sendmsg                = tcp_sendmsg,
        .sendpage               = tcp_sendpage,
        .backlog_rcv            = tcp_v6_do_rcv,
...
        .h.hashinfo             = &tcp_hashinfo,
        .no_autobind            = true,
#ifdef CONFIG_COMPAT
        .compat_setsockopt      = compat_tcp_setsockopt,
        .compat_getsockopt      = compat_tcp_getsockopt,
#endif
#ifdef CONFIG_MEMCG_KMEM
        .proto_cgroup           = tcp_proto_cgroup,
#endif
        .clear_sk               = tcp_v6_clear_sk,
};

net/ipv6/af_inet6.c
const struct proto_ops inet6_stream_ops = {
        .family            = PF_INET6,
        .owner             = THIS_MODULE,
        .release           = inet6_release,
        .bind              = inet6_bind,
        .connect           = inet_stream_connect,       /* ok           */
        .socketpair        = sock_no_socketpair,        /* a do nothing */
        .accept            = inet_accept,               /* ok           */
        .getname           = inet6_getname,
        .poll              = tcp_poll,                  /* ok           */
        .ioctl             = inet6_ioctl,               /* must change  */
        .listen            = inet_listen,               /* ok           */
        .shutdown          = inet_shutdown,             /* ok           */
        .setsockopt        = sock_common_setsockopt,    /* ok           */
        .getsockopt        = sock_common_getsockopt,    /* ok           */
        .sendmsg           = inet_sendmsg,              /* ok           */
        .recvmsg           = inet_recvmsg,              /* ok           */
        .mmap              = sock_no_mmap,
        .sendpage          = inet_sendpage,
        .splice_read       = tcp_splice_read,
#ifdef CONFIG_COMPAT
        .compat_setsockopt = compat_sock_common_setsockopt,
        .compat_getsockopt = compat_sock_common_getsockopt,
#endif
};

