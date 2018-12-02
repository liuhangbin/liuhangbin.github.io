---
layout: post
Title: Linux Kernel Networking Learning
Date: 2013-05-21
tags: [Linux, Kernel, Networking]
---

1. How to create Proc
---
proc_net_fops_create()  /* create and init the file*/
 |- create_proc_entry()

proc_net_remove()
 |- remove_proc_entry()


2. struct socket
---
struct socket {
sk
ops
}

3. socket buffer
---
include/linux/skbuff.h
net/core/skbuff.c

4. Network module init
---

start_kernel()
 |-> ...
 |-> trap_init()
 |-> rcu_init()
 |-> ...
 |-> rest_init
      |-> kernel_thread(init, ...)
      |-> ...       |
                    |
  |------------------
init()
 |-> ...
 |-> do_basic_setup()
 |-> ...       |
               |
   |------------
do_basic_setup()
 |-> init_workqueues()
 |-> usermodehelper_init()
 |-> driver_init()
 |-> sysctl_init()
 \-> do_initcalls()

driver_init() : drivers/net/ethernet/intel/e100.c
---

static int __init e100_init_module(void)
{
        if (((1 << debug) - 1) & NETIF_MSG_DRV) {
                pr_info("%s, %s\n", DRV_DESCRIPTION, DRV_VERSION);
                pr_info("%s\n", DRV_COPYRIGHT);
        }
        return pci_register_driver(&e100_driver);
}

static void __exit e100_cleanup_module(void)
{
        pci_unregister_driver(&e100_driver);
}

module_init(e100_init_module);
module_exit(e100_cleanup_module);

静态编译下的module_init() : include/linux/init.h

/**
 * module_init() - driver initialization entry point
 * @x: function to be run at kernel boot time or module insertion
 * 
 * module_init() will either be called during do_initcalls() (if
 * builtin) or at module insertion time (if a module).  There can only
 * be one per module.
 */

...

#define __define_initcall(fn, id) \
        static initcall_t __initcall_##fn##id __used \
        __attribute__((__section__(".initcall" #id ".init"))) = fn
...
#define device_initcall(fn)             __define_initcall(fn, 6)
...
#define __initcall(fn) device_initcall(fn)
...
#define module_init(x)  __initcall(x);



net/socket.c		core_initcall(sock_init)
net/core/sock.c		subsys_initcall(proto_init)
net/ipv4/af_inet.c	fs_initcall(inet_init)
net/core/dev		subsys_initcall(net_dev_init)
driver/net/e100.c	module_init(e100_init_module)

Lets see net_dev_init first:
----

$ sed -n '6186, 6272p' net/core/dev.c
/*
 *      Initialize the DEV module. At boot time this walks the device list and
 *      unhooks any devices that fail to initialise (normally hardware not
 *      present) and leaves us with a valid list of present and active devices.
 *
 */

/*
 *       This is called single threaded during boot, so no need
 *       to take the rtnl semaphore.
 */
static int __init net_dev_init(void)
{
        int i, rc = -ENOMEM;

        BUG_ON(!dev_boot_phase);

        if (dev_proc_init())	// ** init /proc/net/dev/ and proc/net/softnet_stat
                goto out;

        if (netdev_kobject_init())	// ** don't know what's this used for
                goto out;

        INIT_LIST_HEAD(&ptype_all);	// ** init hash table ptype_base
        for (i = 0; i < PTYPE_HASH_SIZE; i++)
                INIT_LIST_HEAD(&ptype_base[i]);

        INIT_LIST_HEAD(&offload_base);

        if (register_pernet_subsys(&netdev_net_ops))
                goto out;

        /*
         *      Initialise the packet receive queues.
         */

        for_each_possible_cpu(i) {	// ** init cpu related quenes
                struct softnet_data *sd = &per_cpu(softnet_data, i);

                memset(sd, 0, sizeof(*sd));
                skb_queue_head_init(&sd->input_pkt_queue);
                skb_queue_head_init(&sd->process_queue);
                sd->completion_queue = NULL;
                INIT_LIST_HEAD(&sd->poll_list);
                sd->output_queue = NULL;
                sd->output_queue_tailp = &sd->output_queue;
#ifdef CONFIG_RPS
                sd->csd.func = rps_trigger_softirq;
                sd->csd.info = sd;
                sd->csd.flags = 0;
                sd->cpu = i;
#endif

                sd->backlog.poll = process_backlog;
                sd->backlog.weight = weight_p;
                sd->backlog.gro_list = NULL;
                sd->backlog.gro_count = 0;
        }

        dev_boot_phase = 0;	// ** means net dev init over

        /* The loopback device is special if any other network devices
         * is present in a network namespace the loopback device must
         * be present. Since we now dynamically allocate and free the
         * loopback device ensure this invariant is maintained by
         * keeping the loopback device as the first device on the
         * list of network devices.  Ensuring the loopback devices
         * is the first device that appears and the last network device
         * that disappears.
         */
        if (register_pernet_device(&loopback_net_ops))
                goto out;

        if (register_pernet_device(&default_device_ops))
                goto out;

        open_softirq(NET_TX_SOFTIRQ, net_tx_action);
        open_softirq(NET_RX_SOFTIRQ, net_rx_action);

        hotcpu_notifier(dev_cpu_callback, 0);
        dst_init();	// ** init dest route cache
        rc = 0;
out:
        return rc;
}

subsys_initcall(net_dev_init);


IP INIT
---

net/ipv4/devinet.c : about inetdev_init, netlink and ioctl

ASSERT_RTNL();	// ** What it used for??

static struct in_device *inetdev_init(struct net_device *dev)
{
	struct in_device *in_dev;

	ASSERT_RTNL();

	in_dev = kzalloc(sizeof(*in_dev), GFP_KERNEL);
	if (!in_dev)
		goto out;
	memcpy(&in_dev->cnf, dev_net(dev)->ipv4.devconf_dflt,
			sizeof(in_dev->cnf));
	in_dev->cnf.sysctl = NULL;
	in_dev->dev = dev;
	in_dev->arp_parms = neigh_parms_alloc(dev, &arp_tbl);
	if (!in_dev->arp_parms)
		goto out_kfree;
	if (IPV4_DEVCONF(in_dev->cnf, FORWARDING))
		dev_disable_lro(dev);
	/* Reference in_dev->dev */
	dev_hold(dev);
	/* Account for reference dev->ip_ptr (below) */
	in_dev_hold(in_dev);

	devinet_sysctl_register(in_dev);
	ip_mc_init_dev(in_dev);
	if (dev->flags & IFF_UP)
		ip_mc_up(in_dev);

	/* we can receive as soon as ip_ptr is set -- do this last */
	rcu_assign_pointer(dev->ip_ptr, in_dev);
out:
	return in_dev;
out_kfree:
	kfree(in_dev);
	in_dev = NULL;
	goto out;
}


static void inetdev_destroy(struct in_device *in_dev)
{
	struct in_ifaddr *ifa;
	struct net_device *dev;

	ASSERT_RTNL();

	dev = in_dev->dev;

	in_dev->dead = 1;

	ip_mc_destroy_dev(in_dev);

	while ((ifa = in_dev->ifa_list) != NULL) {
		inet_del_ifa(in_dev, &in_dev->ifa_list, 0);
		inet_free_ifa(ifa);
	}

	RCU_INIT_POINTER(dev->ip_ptr, NULL);

	devinet_sysctl_unregister(in_dev);
	neigh_parms_release(&arp_tbl, in_dev->arp_parms);
	arp_ifdown(dev);

	call_rcu(&in_dev->rcu_head, in_dev_rcu_put);
}

// ** Dont know why use __in_dev_get_rcu() two times
__be32 inet_select_addr(const struct net_device *dev, __be32 dst, int scope)
{
	__be32 addr = 0;
	struct in_device *in_dev;
	struct net *net = dev_net(dev);

	rcu_read_lock();
	in_dev = __in_dev_get_rcu(dev);
	if (!in_dev)
		goto no_in_dev;

	for_primary_ifa(in_dev) {
		if (ifa->ifa_scope > scope)
			continue;
		if (!dst || inet_ifa_match(dst, ifa)) {
			addr = ifa->ifa_local;
			break;
		}
		if (!addr)
			addr = ifa->ifa_local;
	} endfor_ifa(in_dev);

	if (addr)
		goto out_unlock;
no_in_dev:

	/* Not loopback addresses on loopback should be preferred
	   in this case. It is importnat that lo is the first interface
	   in dev_base list.
	 */
	for_each_netdev_rcu(net, dev) {	// ** seems try other dev
		in_dev = __in_dev_get_rcu(dev);
		if (!in_dev)
			continue;

		for_primary_ifa(in_dev) {
			if (ifa->ifa_scope != RT_SCOPE_LINK &&
			    ifa->ifa_scope <= scope) {
				addr = ifa->ifa_local;
				goto out_unlock;
			}
		} endfor_ifa(in_dev);
	}
out_unlock:
	rcu_read_unlock();
	return addr;
}
EXPORT_SYMBOL(inet_select_addr);

/*
 * Confirm that local IP address exists using wildcards:
 * - in_dev: only on this interface, 0=any interface
 * - dst: only in the same subnet as dst, 0=any dst
 * - local: address, 0=autoselect the local address
 * - scope: maximum allowed scope value for the local address
 */
__be32 inet_confirm_addr(struct in_device *in_dev,
			 __be32 dst, __be32 local, int scope)
{
	__be32 addr = 0;
	struct net_device *dev;
	struct net *net;

	if (scope != RT_SCOPE_LINK)	// ** Why use scope != RT_SCOPE_LINK ?
		return confirm_addr_indev(in_dev, dst, local, scope);

	net = dev_net(in_dev->dev);
	rcu_read_lock();
	for_each_netdev_rcu(net, dev) {
		in_dev = __in_dev_get_rcu(dev);
		if (in_dev) {
			addr = confirm_addr_indev(in_dev, dst, local, scope);
			if (addr)
				break;
		}
	}
	rcu_read_unlock();

	return addr;
}
EXPORT_SYMBOL(inet_confirm_addr);

static int __inet_insert_ifa(struct in_ifaddr *ifa, struct nlmsghdr *nlh,
			     u32 portid)
{
	struct in_device *in_dev = ifa->ifa_dev;
	struct in_ifaddr *ifa1, **ifap, **last_primary;

	ASSERT_RTNL();

	if (!ifa->ifa_local) {
		inet_free_ifa(ifa);
		return 0;
	}

	ifa->ifa_flags &= ~IFA_F_SECONDARY;
	last_primary = &in_dev->ifa_list;

	for (ifap = &in_dev->ifa_list; (ifa1 = *ifap) != NULL;
	     ifap = &ifa1->ifa_next) {
		if (!(ifa1->ifa_flags & IFA_F_SECONDARY) &&
		    ifa->ifa_scope <= ifa1->ifa_scope)
			last_primary = &ifa1->ifa_next;
		if (ifa1->ifa_mask == ifa->ifa_mask &&
		    inet_ifa_match(ifa1->ifa_address, ifa)) {
			if (ifa1->ifa_local == ifa->ifa_local) {
				inet_free_ifa(ifa);
				return -EEXIST;
			}
			if (ifa1->ifa_scope != ifa->ifa_scope) {
				inet_free_ifa(ifa);
				return -EINVAL;
			}
			ifa->ifa_flags |= IFA_F_SECONDARY;
		}
	}

	if (!(ifa->ifa_flags & IFA_F_SECONDARY)) {
		net_srandom(ifa->ifa_local);	// ** Why use net_srandom ?
		ifap = last_primary;
	}

	ifa->ifa_next = *ifap;
	*ifap = ifa;

	inet_hash_insert(dev_net(in_dev->dev), ifa);

	cancel_delayed_work(&check_lifetime_work);
	schedule_delayed_work(&check_lifetime_work, 0);

	/* Send message first, then call notifier.
	   Notifier will trigger FIB update, so that
	   listeners of netlink will know about new ifaddr */
	rtmsg_ifa(RTM_NEWADDR, ifa, nlh, portid);
	blocking_notifier_call_chain(&inetaddr_chain, NETDEV_UP, ifa);

	return 0;
}


