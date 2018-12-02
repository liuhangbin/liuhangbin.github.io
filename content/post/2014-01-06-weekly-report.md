---
layout: post
Title: weekly report
Date: 2014-01-06
published: false
tags: [Linux, RedHat]
---

==========================================
2013-12-25 week 52
This week:
---------------
1) Verify bug
Bug 894257 -        Kernel panic, Fatal exception in interrupt - nf_nat_cleanup_conntrack+0x54/0xa4
Bug 1034605 -       possible deadlock at packet_set_ring
Bug 860123 -        RFE: Backport SO_REUSEPORT
Bug 887683 -        unable to handle kernel NULL in selinux_socket_unix_may_send
2) Report bug
Bug 1045356 -        kernel doesn't send echo request after set ip xfrm sel
Next week:
---------------
Testing bug https://bugzilla.redhat.com/show_bug.cgi?id=836427
==========================================
2013-12-18 week 51
This week:
---------------
1) verify bug
Bug 957721 -        [RFE] Allow PF_PACKET to dump/inject Netlink frames
2) sanity only
Bug 1004165 -        Use after free in tcp_v6_do_rcv
Bug 733130 -        [7.0 FEAT] IPv6 support for qetharp tool - kernel
3) test IPv6 EFI boot
4) build USGv6 interoperation environment
Next week:
---------------
Test RHEL7 bugs
==========================================
2013-12-11 week 50
This week:
---------------
1) report bug
Bug 1040241 -        shutdown veth immediately after create failed
Bug 1040249 -        ip link change an none exist address succeed
2) update test case
    networking/common
3) finish test case
    networking/netlink/iproute2
Next week:
---------------
1) test RHEL7 bug
2) write tcp performance test case
==========================================
2013-12-04 week 49
This week:
---------------
1) finish RHEL7 testing
IPv6 https://beaker.engineering.redhat.com/jobs/552829
2) report bug 1036534
3) writting iproute2 utils testing
Next week:
---------------
1) config IPv6 only environment
2) write TCP performance test with latency test
==========================================
2013-11-27 week 48
This week:
---------------
1) RHEL7 test result
Tire1 https://beaker.engineering.redhat.com/jobs/547397
socket https://beaker.engineering.redhat.com/jobs/546551
UDP https://beaker.engineering.redhat.com/jobs/547394
sctp https://beaker.engineering.redhat.com/jobs/547396
igmp https://beaker.engineering.redhat.com/jobs/550655
2) Review bug 
Bug 1011214 -        tproxy for ipv6 without conntrack does not handle fragments correctly
3) Report bug
Bug 1034032 -        SCTP crypto_init_shash_ops
Bug 1034049 -        ip -6 addrlabel return incorrect error message
4) config IPv6 pure environment switch and router
5) Update test case
9a1a1c2 combination: rm regression and sctp folder
cebbc54 combination: rm netlink test
2e635c2 combiantion: sync in each runcase
6055625 tcp/user_timeout: use common sync
36975c5 netlink: update ip adrlabel and link test
70e330e common: push to include dir before get_round incase return path info
078c477 ipv6/snmp: update sync method
d43b9d2 common: update sync to make round specific
dfaac2d igmp/ipv6/send_recv: use watchdog for receiver
a68dd44 Regression: Add Fujitsu-net_sched-bz1003781 test
8ab0ef1 combination: correct typo error
5bfa9bc common: add iperf_install
aac2bfb combination: correct udp socket path
0e4d40d ipv6/QoS: remove distro check and iperf_install
ae8c044 trinity: update patch for RHEL7 support
e379a8b common: use e instead of n for echo
Next week:
---------------
1) set up ipv6 env pxe server
2) keep on write netlink test case
==========================================
2013-11-20 week 47
This week:
---------------
1) Verify bug
Bug 990968 -        [TAHI][SPEC] Test v6LC.1.2.6: Options Processing, Hop-by-Hop Options Header - End Node
2) update test cases
    /kernel/networking/common
    /kernel/networking/socket_fuzz
    /kernel/networking/combination_test_tmp
3) write test case
    /kernel/networking/netlink/iproute
4) design IPv6 only environment
Next week:
---------------
IPv6 only environment
==========================================
2013-11-13 week 46
This week:
---------------
1) Report bug
Bug 1029268 -        kernel BUG at security/selinux/ss/services.c:625!
Bug 1029302 -        Linux kernel doesn't forward IPv6 packets from network namespace
2) Verify bug
Bug 963843 -        CVE-2013-2206 kernel: sctp: duplicate cookie handling NULL pointer dereference 
3) Finish tcms runs on 6.5
socket https://tcms.engineering.redhat.com/run/90849/
udp https://tcms.engineering.redhat.com/run/90851/
tcp https://tcms.engineering.redhat.com/run/94349/
igmp https://tcms.engineering.redhat.com/run/90854/
sctp https://tcms.engineering.redhat.com/run/90853/
ipv6 https://tcms.engineering.redhat.com/run/94350/
tunnel https://tcms.engineering.redhat.com/run/90855/
regression https://tcms.engineering.redhat.com/run/94347/
Next week:
---------------
Issues:
---------------
It looks like veth doesn't send RS packet after set up, please read the code and find out the reason
==========================================
2013-11-06 week 45
This week:
---------------
1) Verify bug
Bug 1019343 -        socket credentials are freed too early and sends garbage to userland
Bug 1011930 -        CVE-2013-4387 Kernel: net: IPv6: panic when UFO=On for an interface [rhel-6.5]
2) Sanity only
[Bug 923659] CVE-2013-2634 CVE-2013-2635 Kernel: information leaks via netlink interface [rhel-6.5]
3) run RHEL6.5 all protocol tests
4) build IPVS NAT env
Next week:
---------------
Verify RHEL6.5 bugs
Run 6.5 regression test
test bug Bug 963843 -        CVE-2013-2206 kernel: sctp: duplicate cookie handling NULL pointer dereference [rhel-6.5]
develop NAT test case
 
==========================================
2013-10-30 week 44
This week:
---------------
1) Verify bug
Bug 981559 - CVE-2013-2232 Kernel: ipv6: using ipv4 vs ipv6 structure during routing lookup in sendmsg
Bug 997103 -        Regression with ipv6 and local routes
2) Report bug
Bug 1022555 -        Wrong  SCTP COOKIE ECHO checksum with IPVS NAT mode
3) Sanity only
Bug 864597 -        tcp stack defaults unsuitable for high bandwith apps
4) lksctp-tools errata regression test
Next week:
---------------
1) Run TAHI test suite on RHEL6.5
2) Run all jobs on RHEL6.5
5) test bug Bug 963843 -        CVE-2013-2206 kernel: sctp: duplicate cookie handling NULL pointer dereference [rhel-6.5]
5) develop NAT test case
==========================================
2013-10-23 week 43
This week:
---------------
1) Report bug
Bug 1020426 -        ip tunnel add gre0 mode gre return failed
Bug 1020434 -        ip tunnel add gre1 show 2 entries
2) Verify bug
Bug 953383 -        [TAHI] SCTP: RecvChunkECNEcho.seq - The cwnd is not update correctly
Bug 855379 -        [TAHI] SCTP:  InitCwndAfterIdle.seq - The cwnd of the transport address should be  adjusted to max(cwnd/2, 4*MTU) when does not transmit dat
Bug 855383 -        [TAHI] SCTP: InvalidParamInHeartbeat.seq - HEARTBEAT chunk is received with invalid parameter length
Bug 755486 -        [RFE] Include M3AU measurement counters for SCTP in RHEL
Bug 856262 -        [RFE] add multicast igmp snooping data to brctl
[Bug 1020630] [TestOnly] mlx4: loosing multicast packets when leaving promisc mod
[Bug 1020627] [TestOnly] setsockopt() incorrectly allows IP_TTL to be 0
Bug 947950 -        Backport "tcp:sk_add_backlog() is too agressive for TCP" patch from upstream 3.5 kernel to RHEL6
Bug 994393 -        [FJ6.4 Bug] netfilter: ctnetlink: don't permit ct creation with random tuple
3) Sanity Only bug
Bug 896233 - RHEL 6 Update 4 Kernel -348 panic in network stack when running AMG at 384 or 512 process count
Bug 982108 -        function netlink_dump() forget to release netlink callback mutex on error path
Next week:
---------------
lksctp-tools errata regression test
==========================================
2013-10-16 week 42
This week:
---------------
1) Report bug
Bug 1017581 -        echo 1073741824 to rps_flow_cnt invoked oom-killer on x86_64
2) Verify bug
Bug 889048 -        [FJ6.3 Bug] netfilter: ipset: fix crash if IPSET_CMD_NONE command is sent
Bug 915552 -        Improve the accuracy of dropwatch and tcpdump
Bug 801856 -        virtio IPv6 very slow (1000x slower than IPv4)
Bug 958215 -        iproute fails during ipv6 route change
Bug 908390 -        Backport SCTP_GET_ASSOC_STATS support
Bug 999365 -        CVE-2013-0343 kernel: handling of IPv6 temporary addresses
Bug 782886 -        Setting the TCP buffer size to something smaller than 280 causes 3 second delay in TCP handshake, plus an extra ACK.
3) SanityOnly
Bug 916291 -        sctp: set association state to established in dupcook_a handler
Next week:
---------------
1) complete Training https://redhat-lcec.lrn.com/
Test bug:
Bug 892256 -        SCTP init fail on IPVS NAT mode
2) RHEL7 IPv6 feature test
https://bugzilla.redhat.com/show_bug.cgi?id=872571
https://bugzilla.redhat.com/show_bug.cgi?id=731504
==========================================
2013-10-09 week 41
---------------
1) Verify
Bug 1001479 -        tcp_ioctl FIONREAD/SIOCINQ sets output variable to 1 when it should be 0
Next week:
---------------
1) vpnc performance on RHEL6/7
2) TP-LINK wireless
3) complete Training https://redhat-lcec.lrn.com/
==========================================
2013-09-18 week 38
This week:
---------------
1) Verify
Bug 912557 -        Change the MIN_RCVBUF from 256 to a larger number for test_1_to_1_sockopt.c
Bug 912029 -        Kernel shows wrong expires value for expired routes
Bug 887868 -        SCTP NULL pointer dereference in _read_lock_bh
Bug 876634 -        RFE : IPv6: Tokenised identifier support
Bug 918316 -        SCTP protocol to change from MD5 to SHA1/2 for FIPS compliance in RHEL6
2) Report bug
Bug 1008267 -        SCTP page allocation failure
Bug 1008268 -        kernel BUG at net/sctp/sm_make_chunk.c:1396
3) Tested [Bug 856262] [RFE] add multicast igmp snooping data to brctl , but failed QA, Have ased developer 
==========================================
2013-09-11 week 37
This week:
---------------
1) Report bug
Bug 1004645 -        ip page allocation failure with bnx2x
Bug 1005084 -        swapper/0 Not tainted 3.10.0-15.el7.x86_64
2) verify bug
Bug 795367 -        Allow autoconfigure of IPv6 when server is a router
Bug 629857 -        tcp conntrack: fix the retransmissions of tcp syn packet when netfilter state is out-of-sync
Bug 863512 -        CVE-2013-2128 Kernel: net: oops from tcp_collapse() when using splice(2) 
Bug 896020 -        ipv6: do not create neighbor entries for local delivery
3) Sanity only
Bug 970609 -        tcp: fix tcp_trim_head() to adjust segment count with skb MSS
Next week:
---------------
1) see if we should ack Bug 963317 -        ICMPv6 ND: ndisc_build_skb() failed to allocate an skb, err=-11 , could get an ack
2) IPv6 UEFI testing
3) run/update RHEL6 test case
==========================================
2013-09-04 week 36
This week:
---------------
1) Build Openstack testing environment
http://hp-dl388g8-05.rhts.eng.nay.redhat.com
2) Setup USGv6 MLD interopterablity environment and help developer test a MLD bug
3) track with RHEL7 TAHI bugs
4) Testing RHEL6.5 bugs
Next week:
---------------
1) Testing RHEL6.5 bugs
2) Write RHEL6.5 test case
==========================================
2013-08-28 week 35
This week:
---------------
1) Report Bug:
Bug 999381 -        Neighbour table overflow
2) Verify Bug:
Bug 980142 -        CVE-2013-2224 kernel: net: IP_REPOPTS invalid free
Bug 987648 -        CVE-2013-4162 Kernel: net: panic while pushing pending data out of a IPv6 socket with UDP_CORK enabled [rhel-5.10]
3) setup IPv6 interoperability environemnt
Next week:
---------------
1) test IPv6 interoperability MLD testing
2) test RHEL6.5 bugs
3) write RHEL6.5 test case
==========================================
2013-08-21 week 33
This week:
---------------
1) Report Bug:
Bug 998130 -        sctp_packet_transmit kernel NULL pointer
2) Try build up flat OVS on openstack, but failed and report bug
Bug 998388 -        Instance stay on Spawning state forever
3) write and update testcase
    update combiantion test
    write netfilter regression test
    update openstack install test
Next week:
---------------
1)Write RHEL6.5 test case
2) Test Some bugs
==========================================
2013-08-14 week 32
This week:
---------------
1) update socket fuzz test for IPv6 and SCTP support
2) Add trinity test
3) Update combination test
4) Reivew RHEL6.5 bugs and write IPv6/protocol/IPVS test plan
Next week:
---------------
1) Write RHEL6.5 test case
1) Build openstack network!!! (delayed too much time)
==========================================
2013-08-07 week 31
This week:
---------------
1) Verify bug 957604
1) Report bug
Bug 990968 -        [TAHI][SPEC] Test v6LC.1.2.6: Options Processing, Hop-by-Hop Options Header - End Node
Bug 990981 -        [TAHI][ICMPv6] Test v6LC.5.1.7: Unrecognized Next Header (Parameter Problem Generation)
1) Run TAHI test on RHEL7
Next week:
---------------
1) RHEL6.5 test plan
1) socket fuzz test case update
==========================================
2013-07-31 week 30
This week:
---------------
1) Verify bug 980141, 980811
1) Report bug
Bug 990386 -        [TAHI][PMTU] Test v6LC.4.1.4: Reduce PMTU On-link failed
Next week:
---------------
1) RHEL7 feature & smoke testing
2) RHEL7 test plan
==========================================
2013-07-24 week 29
This week:
---------------
1) Report bug 987344 netlink: fix for too early rmmod and add reference of module in netlink_dump_start
   Verify bug Bug 728922 - RFC 4291 section 2.4 states that all uncategorized addresses should be considered as Global Unicast
2) Build Openstack environment, now we can automatically setup two computes which using openvswitch
3) Run RHEL5.10 Snapshot 1 regression test
4) Interview Interns
Next week:
---------------
1) RHEL7 feature & smoke testing
2) Update combination test
==========================================
2013-07-17 week 28
This week:
---------------
1) Try to setup openstack environment
2) run combination test on RHEL7
3) Update combination test, add 8 commits
4) Update ipv6/tahi test
Next week:
---------------
1) RHEL7 feature & smoke testing
2) test RHEL7 bug and TAHI MLD bug
==========================================
2013-07-10 week 27
This week:
---------------
1) Try to setup openstack environment
2) run combination test on RHEL7
3) Update combination test, add 12 commits
4) interview Ruifeng Bian
Next week:
---------------
1) RHEL7 feature & smoke testing
2) test RHEL7 bug and TAHI MLD bug
==========================================
2013-07-03 week 26
This week:
---------------
1) report bug
Bug 978670 -        "Example 4.1. Single Node Deployment" use the wrong IP address
Bug 978674 -        It is easy misunderstanding to use 192.168.1.0 for CONTROLLER_ADDRESS
Bug 978678 -        packstack only try found eth1 during single node deployment
Bug 979646 -        Linux router doesn't send ICMP fragment needed message when decrease mtu on the first recived interface
2) verify bug
Bug 905190 -        RHEL 5 does not change MTU when received ICMP NEED FRAGMENT msg
Bug 866743 -        check optlen for IP_MULTICAST_IF option
Bug 745321 -        Can not ping ipv6 address after restart loopback interface
Bug 847613 -        IP_MULTICAST_IF sockopt confusion
Bug 868622 -        error "SIOCSIFADDR: Network is down"seen while assigning IPv6 address to an interface on  2.6.18-308.el5PAE kernel
Bug 871787 -        initcwnd does not work properly in RHEL 5.8
Bug 947038 -        [RHEL5] TCP connection is unexpectedly reset by connect(2) race with enabling tcp_rw_reuse
3) close RHEL7 bug
Bug 801617 -        BUG kmalloc-2048: Poison overwritten
Bug 837519 -        Rename NIC name disorder on RHEL7
Bug 801241 -        BUG blkdev_queue: Poison overwritten
Bug 803110 -        RCU : suspicious rcu_dereference_check() usage
Bug 835326 -        inconsistent lock state
Bug 837516 -        WARNING: at block/genhd.c:1570 disk_clear_events+0x119/0x130()
Bug 835330 -        udevd[295]: error changing net interface name
Bug 917515 -        Call Trace during disabling non-boot cpus on RHEL7
4) interview xiongkai, sunshouqu
Next week:
---------------
1) RHEL7 feature & smoke testing
2) review RHEL7 bugs https://url.corp.redhat.com/0169cc7
==========================================
2013-06-26 week 25
This week:
---------------
1) Verify bug 973485 , Sanity only 955222, 956096, 956148
2) update test cases
    combination_test_tmp
    promiscous/bkrjob
    multicast/conformance
    common
Next week:
---------------
1) verify 6.4.z bugs 963564 968871 969306
2) review RHEL7 bugs https://url.corp.redhat.com/0169cc7
==========================================
2013-06-19 week 24
This week:
---------------
1) Update test case
  combintion_test_tmp
  promiscous/protocol_on_virtual_interface
  udp/dont_sendmsg_to_lo
  sctp/lksctp-tools
  networking/common
2) Running 5.10 protocol tire1 testing
Next week:
---------------
1) Update combination protocol part test
2) Run 5.10 protocol testing
3) Write 5.10 regression test case
==========================================
2013-06-12 week 23
This week:
---------------
1) Report bug 
Bug 972332 - IPV6/UDP: Kernel sends a message from specified interface(such as eth0) to loopback address successfully
Bug 973952 - RHEL7 does not init /proc/sys/net/ipv6/conf/$nic/accept_ra by defult
Next week:
---------------
==========================================
2013-06-05 week 22
This week:
---------------
1) Add IPv6 SocketAPI to combination test
2) Update socket_all to C/S mode and disable avc check
3) Help debug 889992
4) Review RHEL6.5 bugs
Next week:
---------------
2) DEBUG IKEv2 Test case
==========================================
2013-05-29 week 21
This week:
---------------
1) Review RHEL6.5 bugs
2) Review and discuss TCP test case
3) clean up all Protocol test cases
    http://leo.etherpad.corp.redhat.com/test-case
4) Report IKEv2 bug...
    Bug 968127 - [TAHI] openswan should send IKE_SA_INIT Trequest again after ikev2_auth timeout
Next week:
---------------
1) Update RHEL7 USGv6 and IPVS test plan
2) DEBUG IKEv2 Test case
==========================================
2013-05-22 week 20
This week:
---------------
1) Update 5.10 NIC test plan
    1. update 7.4, add 7.5, 7.6
2) Update 5.10 IPv6 test plan
    1. update 6.5.2 stress test
    1. update 6.5.1 robustness test
3) Update 5.10 Protocol test plan
    1. Add SCTP 2.4 SCTP socket fuzz testing
    1. Add SCTP 7.3 SCTP Robustness test by tc
    1. Add SCTP 8 SCTP Netfilter tracking test
4) Update Fujitsu socketet test case
5) install fedora in beaker and test IPv6 IKEv2 : Done, figure out how to setup IPv6 IKEv2 transport connection
6) Update socket/socket_all test case
7) report IKEv2 bug 965014
8) Write RHEL7 feature test plan
    1. USGv6 http://leo.etherpad.corp.redhat.com/RHEL7-USGv6
    1. IPVS http://leo.etherpad.corp.redhat.com/RHEL7-IPVS
Next week:
---------------
1) Update RHEL7 USGv6 and IPVS test plan
2) DEBUG IKEv2 Test case
==========================================
2013-05-15 week 19
This week:
---------------
1) Transfer Multicast with xiaodong
2) Update 5.10 protocol test plan
3) rerun cisco TAHI test manually
4) find out how to config IKEv2
    
Next week:
---------------
1) go on IKEv2 test
2) RHEL7 USGv6 test plan
3) RHEL7 IPVS test plan
==========================================
2013-05-08 week 18
This week:
---------------
1) Update test case Purpose
    /kernel/networking/socket
2) Write socket test
3) Blocked by IKEv2 test, developer said he is looking on this
    
Next week:
---------------
1) write socket test
2) write all test case list
==========================================
2013-05-01 week 17
This week:
---------------
1) Labour day
2) Update test case
    /kernel/networking/ipv6/Fujitsu-socketapi-test
3) Still struggling with IKEv2 test, but find some openshit qe have been running it, will ask them how to config
4) Help cisco debug there TAHI testing...Not finished yet
    
Next week:
---------------
1) Update all test case purpose
2) Write/Update TAHI IKEv2 test
3) Help Cisco Guys
==========================================
2013-04-24 week 16
This week:
---------------
1) Report Bug:
Bug 953756 - [TAHI] SCTP: InitWithParamMissing.seq - Linux doesn't send ABORT with Missing Mandatory Parameter
Bug 953831 - [TAHI] SCTP: BundCookieAckAndData.seq - NUT send abort instead of shutdown
2) Still struggling with IKEv2 test, but find some openshit qe have been running it, will ask them how to config
3) Help cisco debug there TAHI testing...Not finished yet
4) Finish debugging TAHI SCTPv4 test, report and closed some bugs. But the test need update.
    
Next week:
--------test failers should be fixed with separated switch
4) Debug/Update TAHI SCTP test
5) One day for IDF
    
Next week:
---------------
1) DEBUG TAHI SCTP test
2) Write/Update TAHI IKEv2 test
3) Update all protocol test purpose and TCMS details
==========================================
2013-04-10 week 14
This week:
---------------
1) Update TAHI beaker test
2) Debug/write TAHI IKEv2 test
3) Write/Update test case
  Common
    /kernel/networking/virt/add_iface
4) Help Cisco debug TAHI test failers
5) Choose Router and Switch for FY144
    
Next week:
---------------
1) DEBUG TAHI SCTP test
2) Write/Update TAHI IKEv2 test
==========================================
2013-04-03 week 13
This week:
---------------
1) Verify bug 923353, 
2) Report RHEL6.5 Bug 928636 - Linux router doesn't send ICMP fragment needed message when decrease mtu on the first recived interface
3) Update test case
  Common
    /kernel/networking/common
  SCTP
    /kernel/networking/sctp/Conformance
    
Next week:
---------------
1) Update test networking/virt/addr_nic
2) Write/Update TAHI test
2013-03-27 week 12
This week:
---------------
1) Test bug 905190, but didn't reproduced...
2) Update test case
  Common
    /kernel/networking/common
  Ethtool
    /kernel/networking/ethtool/functional
  IPv6
    /kernel/networking/ipv6/tahi
    /kernel/networking/ipv6/Fujitsu-socketapi-test
3) Try to update TAHI IKEv2 test, modify a part, still need help from developer.
    
Next week:
---------------
1) Write/Update TAHI
2) Write SCTP test case
3) Test bug 905190
2013-03-20 week 11
This week:
---------------
1) Update test case for RHEL7
  Ethtool
    /kernel/networking/ethtool/functional
2) Download and build Cisco TAHI evironment
3) Learn IPsec setup
4) Review bug 905190
    
Next week:
---------------
1) Write/Update TAHI and SCTP test case
2) Debug Cisco TAHI testing environment
2013-03-13 week 10
This week:
---------------
1) Verify 6.3.z bug 909158
2) Update test case for RHEL7
  Ethtool
    /kernel/networking/ethtool/functional
  IPv6
    /kernel/networking/ipv6/tahi
3) Metting with Cisco and debug TAHI testing.
Set up TAHI environment on ESXi guest.
    
Next week:
---------------
1) Write/Update TAHI and SCTP test case
2) Debug Cisco TAHI testing environment
2013-03-06 week 9
This week:
---------------
1) Report RHEL7 bug 917515
2) Verify bug 909159
3) Update test case for RHEL7
  Common
    /kernel/networking/common
  SCTP
    /kernel/networking/sctp/lksctp-tools
    /kernel/networking/sctp/multi-homing
    /kernel/networking/sctp/sctp_ssl
    /kernel/networking/sctp/sctp_ns
4) Run RHEL7 Aplha 3 test
https://tcms.engineering.redhat.com/run/57988/
https://tcms.engineering.redhat.com/run/57722/
https://tcms.engineering.redhat.com/run/57892/
    
Next week:
---------------
1) Test 6.3.z bug 909158
2) Update TAHI test and try to test it through cisco switch
2013-02-27 week 8
This week:
---------------
1) Learn to use squid and try to test bug 896020.
2) Write/Update test case
  Common
    /kernel/networking/common
  SCTP
    /kernel/networking/sctp/lksctp-tools
  Stress
    /kernel/networking/stress/simple-netperf
  Promisc
    /kernel/networking/promiscuous/bkrjob
3) Learn QA-tools usage
Next week:
---------------
1) Test RHEL6.4.z bug
2) RHEL7 alpha 3 testing
3) UPdate TAHI tests
2013-02-20 week 7
This week:
---------------
1) Report bug 912029, 912557
1) Import test case
  Regression
    /kernel/networking/Regression/Fujitsu-bz810073
    /kernel/networking/Regression/Fujitsu-bz853344
    /kernel/networking/Regression/Fujitsu-bz872067
3) Debug TAHI tests
Next week:
---------------
1) Test RHEL6.4.z bug
2) Write SCTP lksctp-tools regression test
3) learn how to use qa-tools
4) RHEL7 alpha 3 testing
2013-02-06 week 5
This week:
---------------
1) Report bug 907323, verify bug 726807
2) Update test case
  IPv6
    /kernel/networking/ipv6/proc
  Common
    /kernel/networking/common
  SCTP
    /kernel/networking/sctp/bug/sctp_dos_bz714867
3) TCMS
  SCTP
    https://tcms.engineering.redhat.com/run/56120/
  IPv6
    https://tcms.engineering.redhat.com/run/56543/y bug 706038
3) Try to verify bug 726807, failed QA, change back to asign
4) Run TAHI test on RHEL6.4 kernel 355.el6 , all passed except some known issue
    Core Host : http://10.66.13.54/RHEL6.4/Core_Host/
    core Router : http://10.66.13.54/RHEL6.4/Core_Router/
    DHCP Server : http://10.66.13.54/RHEL6.4/DHCPv6_Server_Host
    IPsec : http://10.66.13.54/RHEL6.4/IPsec/
    SNMP : http://10.66.13.54/RHEL6.4/SNMP/
Next week:
---------------
1) Finish RHEL6.4 bugs
2) Write TAHI test to beaker
3) Write USGv6 addr test case
2013-01-16 week 2
This week:
---------------
1) Report RHEL6.4 bug 895855(blocker?), RHEL6.5 bug 893900
2) Sanity only bug 877327 850736 835535
3) Verify bug 830716
4) Write/Update test case
  IPVS
    /kernel/networking/IPVS/Schedule
  SCTP
    /kernel/networking/sctp/multi-homing
Next week:
---------------
1) Verify remained RHEL6.4 bugs
2) Develope IPv6 tahi auto test
3) Run 6.4 tests in beaker
2013-01-09 week 1
This week:
---------------
1) Report RHEL6.5 bug 892256
2) Verify RHEL6.4 bug 787705
3) Write/Update test case
  IPVS
    /kernel/networking/IPVS/algorithm_test
    /kernel/networking/IPVS/DR
    /kernel/networking/IPVS/NAT
    /kernel/networking/IPVS/TUN
    /kernel/networking/IPVS/Schedule
  SCTP
    /kernel/networking/sctp/multi-homing
  Ltp
    /kernel/networking/ltp/network_stress/
Next week:
---------------
1) Verify RHEL6.4 bugs
2) Update IPVS test cases
3) Run combination_test
2012-12-26 week 51
This week:
---------------
1) Verify 5.9.z bug 874837
2) Report RHEL7 bug 889455, RHEL6.4 bug 889992
3) Write/Update test case
  Common
    /kernel/networking/common
  SCTP
    /kernel/networking/SCTP/sctp_stress
    /kernel/networking/SCTP/multihoming
  Stress
    /kernel/networking/stress/simple_netperf
  Promisc
    /kernel/networking/promiscuous/bkrjob
  IPVS
    /kernel/networking/IPVS/NAT
4) Run RHEL6.4 Snapshot 3 tests
https://tcms.engineering.redhat.com/run/53732/
https://tcms.engineering.redhat.com/run/53731/
https://tcms.engineering.redhat.com/run/53730/
https://tcms.engineering.redhat.com/run/53729/
Next week:
---------------
1) PTO and New Year holiday
2012-12-19 week 50
This week:
---------------
1) Verify 6.4 bugs 876249
2) Report 6.4 bug 887868 , RHEL7 bug 888121 , beaker bug 888611
3) update dell 740 bios for a day but failed
4) Write/Update test case
  SCTP
    /kernel/networking/SCTP/multihoming
    /kernel/networking/SCTP/sctp_stress
5) Try to reproduce bug 706038, but failed
Next week:
---------------
1) Verify 5.8.z Bug 874837
2) Verify RHEL6.4 bugs
3) Write RHEL6 test case
4) update tcms
Misc : beaker still can't run multi-host test between guests on same host(can't get system name sometimes), but not between guest and host
TAHI RHEL7 test blocked by NUT install
2012-12-12 week 49
This week:
---------------
1) Verify 6.4 bugs 855759
2) Try to test bug 787705, big patch , ask developer what else we can do. 
3) review bug 706038, can't reproduce, will ask developer.
4) Write/Update test case
  SCTP
    /kernel/networking/SCTP/multihoming
Next week:
---------------
1) Write USGv6 Addr_arch test case
2) Verify RHEL6.4 bugs
3) Write RHEL6 test case
4) install nics
5) update tcms
6) up IT ticket
2012-12-05 week 48
This week:
---------------
1) Report RHEL7 bug 882782, 883672 (SCTP Regression)
2) Verify bug 6.3.z 874550
    6.4 bugs 819952, 829109, 867688, 851118, 850756.
3) Sanity only 6.4 bug 854376
4) Write/Update test case
  Common
    /kernel/networking/common
  IPv6
    /kernel/networking/ipv6/DNS
  IGMP
    /kernel/networking/multicast/conformance/
  Promisc
    /kernel/networking/promiscuous/protocol_on_local_host/
  Route
    /kernel/networking/route/source_routing
5) Finish RHEL7 based on F18 Beta testing
    https://tcms.engineering.redhat.com/run/52119/
Next week:
---------------
1) Write USGv6 Addr_arch test case
2) Verify RHEL6.4 bugs
3) Write RHEL6 test case
2012-11-28 week 47
This week:
---------------
1) Sanity Only 5.8.z bug 874973, 5.9 bug 798705, 6.3.z bug 877950, 6.4 bug 872799
2) Verify bug 6.3.z 866795 6.4 859533
3) Write/Update test case
  Common
    /kelan
2) Write USGv6 Addr_arch test case
3) Finish RHEL5 regression testing
4) Finish RHEL6.4 beta regression testing
5) Verify RHEL5.9 bug 798705
6) Verify RHEL6.4 bugs
Misc :
We can run multihost jobs on guests in nay lab now
https://beaker.engineering.redhat.com/jobs/335685
2012-11-14 week 45
This week:
---------------
1) Sanity Only 5.9 bug 872612
2) Write/Update test case
  Common
    /kernel/networking/common
  IPv6
    /kernel/networking/ipv6/tahi
  Stress
    /kernel/networking/ltp/network_stress
  Promisc
    /kernel/networking/promiscuous/add_nic_for_guest
3) Write USGv6 Test Plan
https://wiki.test.redhat.com/Leo/usgv6
Next week:
---------------
1) Update USGv6 test plan
2) Update TAHI test case
3) Verify RHEL6.4 bugs
2012-11-06 week 44
This week:
---------------
1) Verify 5.9 bug 715159
2) Report Bug 872065, 872023
3) Write/Update test case
  Common
    /kernel/networking/common
  IPv6
    /kernel/networking/ipv6/tahi
  Stress
    /kernel/networking/stress/simple_netperf
    /kernel/networking/ltp/network_stress
  Promisc
    /kernel/networking/promiscuous/add_nic_for_guest
4) Write TAHI module for beaker
Next week:
---------------
1) Write USGv6 test plan
2) Update TAHI test case
2012-10-30 week 43
This week:
---------------
1) Verify 5.8.z bug 857966, 6.3.z bug 861953
2) Write/Update test case
  Common
    /kernel/networking/common
  IPv6
    /kernel/networking/ipv6/bugs/ipv6_tcp_syn_process_bz803920
  LTP
    /kernel/networking/ltp/network_stress/
3) Write TAHI module for beaker
Next week:
---------------
1) Verify RHEL5.9 bug 715159
2) Update TAHI test case
2012-10-23 week 42
This week:
---------------
1) Test 5.9 bug 715159(bridge multicast), meet some problems, developer's response is slow...
2) Write/Update test case
  Common
    /kernel/networking/common
  TAHI
    /kernel/networking/ipv6/tahi
  Socket
    /kernel/networking/socket/socket_basic_test
  UDP
    /kernel/networking/udp/dont_sendmsg_to_lo
    /kernel/networking/udp/udp_order
    /kernel/networking/udp/udp_proc
    /kernel/networking/udp/udp_socket
3) Update RHEL6.4 test plan
    https://wiki.test.redhat.com/Kernel/Testplans/RHEL64IPVS
Next week:
---------------
1) Verify RHEL5.9 bug 715159
2) Verify RHEL5.8.z bug 857966
3) Verify RHEL6.3.z bug 861953 858956
4) Update TAHI test case
5) Update RHEL7 IPv6 feature status
https://home.corp.redhat.com/wiki/rhel-7-feature-page-usgv6-sdoc
https://home.corp.redhat.com/wiki/rhel-7-feature-page-ipv6
2012-10-16 week 41
This week:
---------------
1) Verify 5.9 bug 854067 819830
2) Report bug 866743
3) Write/Update test case
  Common
    /kernel/networking/common
  TAHI
    /kernel/networking/ipv6/tahi
  LTP
    /kernel/networking/ltp/network_stress
4) Update RHEL6.4 test plan
    https://wiki.test.redhat.com/Kernel/Testplans/RHEL64IPVS
Next week:
---------------
1) Verify RHEL5.9 bugs
2) Update RHEL6.4 test paln
3) Update TAHI beaker test case
2012-10-09 week 39
This week:
---------------
1) Verify 6.3.z bug 858284 858285
2) Write/Update test case
  Common
    /kernel/networking/common
  TAHI
    /kernel/networking/ipv6/tahi
  Regression
    /kernel/networking/vlan/vlan-on-loopback-bz703709
    /kernel//networking/Regression/Fujitsu-bz603662
3) Update RHEL6.4 test plan
    http://wiki.test.redhat.com/Kernel/Testplans/RHEL64Protocol
    http://wiki.test.redhat.com/Kernel/Testplans/RHEL64IPv6
    https://wiki.test.redhat.com/Kernel/Testplans/RHEL64IPVS
Next week:
---------------
1) Verify RHEL5.9 bugs
2) Update RHEL6.4 test paln
3) Update TAHI beaker test case
2012-09-25 week 38
This week:
---------------
1) Write/Update test case
  Common
    /kernel/networking/common
  Stress
    /kernel/networking/ipv6/tahi
2) Update RHEL6.4 test plan
    http://wiki.test.redhat.com/Kernel/Testplans/RHEL64Protocol
    http://wiki.test.redhat.com/Kernel/Testplans/RHEL64IPv6
3) Review and test bug 854376, Test Bug 715159, wait for developer's feedback
Next week:
---------------
1) Verify RHEL5.9 bugs
2) Update RHEL6.4 test paln
3) Update TAHI beaker test case
2012-09-18 week 37
This week:
---------------
1) Sanity only RHErnel/networking/promiscuous/protocol_on_virtual_interface/
3) Try to verify bug 715159 failed, wait for developer's refix
4) Write single nic test for bonding/combination_test, waitting for kaka and zhchen's review
Next week:
---------------
1) Verify RHEL5.9 bugs
2) Write ltp network stress test
3) Develop Protocol regression test case
4) Verify bug 715159
2012-09-04 week 35
This week:
---------------
1) Report bug 852921 : netxen_nic report bad irq
2) Verify bug 748332, sanity only bug 846831
3) Write/Update test case
  Common
    /kernel/networking/common
  Promisc
    /kernel/networking/promiscuous/protocol_on_virtual_interface
4) Try to develop SCTP conformance test on KVM guest, blocked on console config.
Next week:
---------------
1) Verify RHEL5.9 bugs
2) Update netperf test case for kaka
3) Develop Protocol regression test case
2012-08-28 week 34
This week:
---------------
1) Report bug 850736 Delete unnecessary call of sk_alloc_size()
2) Try to test bug 748332, need info reporter, wait for feedback
3) Write/Update test case
  Common
    /kernel/networking/common
  Promisc
    /kernel/networking/promiscuous/virt_multihost
  Socket : Add pthread and IP Options test
    /kernel/networking/socket/socket_basic_test
4) Develop TAHI MLDv2 test environment, test failed because of bug 514794, update test result
    https://wiki.test.redhat.com/Leo/ipv6-certification
5) Try to develop SCTP IPv4/v6 certification test environment
6) One day Carnegie traning
Next week:
---------------
1) Verify RHEL5.9 bugs 748332 and 816106
2) Try to run SCTP certification test
3) Develop Protocol regression test case
Misc: 
Please let jkastner return dell580/585 after use
Virt_multihost usage :
1) Run you task as a parameter
<task name="/kernel/networking/promiscuous/virt_multihost" role="STANDALONE">
    <params>
        <param name="CONTROLLER_IP" value="10.66.12.114"/>
        <param name="TASK" value="/kernel/networking/promiscuous/virt_multihost"/>
        <param name="TASK_ROLE" value="SERVERS"/>
    </params>
</task>
2) Add an if in you runtest.sh
{{{
. /mnt/tests/kernel/networking/common/include.sh
if [ $VM_ROLE ];then
        virt_multihost $VM_ROLE
fi
...
if [ $VM_ROLE ];then
        clean_up
fi
}}}
<task name="/kernel/networking/promiscuous/virt_multihost" role="STANDALONE">
    <params>
        <param name="VM_ROLE" value="SERVERS"/>
    </params>
</task>
2012-08-21 week 33
This week:
---------------
1) Verify bug 819608 842765
2) Write/Update test case
  Common
    /kernel/networking/common
  Promisc
    /kernel/networking/promiscuous/virt_multihost
  IPv6
    /ipv6/bugs/ipv6_hop_jumbo_remote_crash_bz548641
  TCP
    /kernel/networking/tcp/kernel_oops_from_user_MSS_bz652508
  Socket
    /kernel/networking/socket/socket_basic_test
  Misc
    /kernel/networking/tcp/Performance
    /kernel/networking/udp/Performance
3) Create lcov environment and do code coverage test. There are too many files(about 160 files, 47K lines), it is hard to cover all(I have covered about 20K lines).
http://10.66.12.114/kcov/net/index.html > 50%
http://10.66.12.114/kcov/net/ipv4/index.html > 40%
http://10.66.12.114/kcov/net/ipv6/index.html > 40%
http://10.66.12.114/kcov/net/sctp/index.html > 50%
http://10.66.12.114/kcov/net/unix/index.html > 60%
Next week:
---------------
1) Update all Test cases for RHEL5.9 support
2) Test RHEL5.9 bugs
3) Update socket test with IP options
2012-08-14 week 32
This week:
---------------
1) Report bug
  RHEL5.9 :
    847613 - IP_MULTICAST_IF sockopt confusion
    846876 - SCTP sctp_recvmsg() got SCTP_ASSOC_CHANGE SCTP_COMM_UP twice
    846939 - kernel softlockup when loading thousands of qdiscs
  RHEL6.3 : 846877
    846877 - SCTP sctp_recvmsg() got SCTP_ASSOC_CHANGE SCTP_COMM_UP twice
2) Write/UPdate test case
  Update test case for 5.9 testing
  Common
    /kernel/networking/common
  SCTP
    /kernel/networking/sctp/sctp_ltp
    /kernel/networking/sctp/bugs/sctp_remote_dos_bz555658
    /kernel/networking/sctp/bugs/malformed_SCTPChunkInit_bz584645
  IPv6
    /kernel/networking/ipv6/common
    /kernel/networking/ipv6/roo run MLDv2 tests, But all failed , will check the reason.
    http://10.66.13.54/RHEL5.8/MLDv2-Listener/mldv2-listener/index.html
5) Create IPv6 certification link and add RHEL6.2 RHEL5.8 test out
    https://wiki.test.redhat.com/Leo/ipv6-certification
Next week:
---------------
1) Update all Test cases for RHEL5.9 support
2) Update all multihost test for virtual-interface support
3) Update IPv6 Socket test for RHEL7
4) Check MLDv2 tests fail reason
2012-07-31 week 30
This week:
---------------
1) Reprot RHEL5.9 bug
    842765 - Add negative value check for TCP_USER_TIMEOUT option
2) Write/UPdate test case
  Write basic socket test case
  /kernel//networking/socket/socket_basic_test
  Add Time control support
  /kernel/networking/tcp/Performance
  /kernel/networking/tdp/Performance
  /kernel/networking/sctp/Performance
3) Debug TAHI IPv6 Invalid Next Header error, failed on RHEL5.8, finally found we forgot turn off ip6tables
4) Review RHEL6.4 bugs
Next week:
---------------
1) Write RHEL5.9 bug regression test cases
2) Write socket test case based on test plan
3) Review RHEL6.4 bugs
4) Run TAHI test on RHEL5.8 and try to run IKEv2 tests
2012-07-24 week 29
This week:
---------------
1) Reprot RHEL5.9 bug
    Bug 842486 - NULL pointer dereference when mlx4 driver add vlan
2) Write/UPdate test case
  Add interface choose support
  /kernel/networking/tcp/Performance
  /kernel/networking/tdp/Performance
  /kernel/networking/sctp/Performance
  Add tcp regression test for RHEL5.9
  /kernel/networking/tcp/tcp_user_timeout_bz819608
3) Update IPv6 test plan based on USGv6
  https://wiki.test.redhat.com/Kernel/Testplans/RHEL59IPv6
4) TAHI SNMP test debug
  http://10.66.13.54/SNMP_Debug/MIB/index.html
5) Test bug 819608, add a patch for developer, but don't know whether to add max int value control, will ask developer.
https://bugzilla.redhat.com/show_bug.cgi?id=819608#c9
Next week:
---------------
1) Write RHEL5.9 bug regression test cases
2) Updte stress test with time control support
3) TAHI test dubug and try to run other test cases that haven't run
4) Review RHEL6.4 bugs
2012-07-17 week 28
This week:
---------------
1) Reprot bug 839116
2) UPdate test case
  Add TCP support for testcase
  /kernel/networking/promiscuous/protocol_on_virtual_interface
3) Update Protocol test plan
  Add socket test plan and update other test cases
  https://wiki.test.redhat.com/Kernel/Testplans/RHEL59Protocol
4) TAHI test and update tahi test wiki, but not find why SNMP test failed, icmpStatsInMsgs counter(2) UNMATCHED (recv: 110 exp: 111)
  http://10.66.13.54/SNMP_Debug/MIB/index.html
5) RHCAS training and some interviews
Next week:
---------------
1) Write RHEL5.9 bug regression test cases
2) Try to update IPv6 test plan based on USGv6
3) TAHI test dubug and try to run other test cases that haven't run
4) Write stress test case for virtual interface
2012-07-10 week 27
This week:
---------------
1) Reprot RHEL7 bugs :
    Bug 837499 - e1000e NIC Reset adapter in loop
    Bug 837516 - WARNING: at block/genhd.c:1570  disk_clear_events+0x119/0x130()
    Bug 837519 - Rename NIC name disorder on RHEL7
2) Write protocol test case module which contains IPv4/IPv6 TCP/UDP/SCTP/IGMP tests
    /kernel/networking/promiscuous/protocol_on_virtual_interface
3) debug RHEL7 Alpha-2 pre-rc tire2 test, gre test passed now
4) Write TAHI test config wiki
  https://wiki.test.redhat.com/Leo/tahi
5) Review and ack RHEL6.4 bugs
Next week:
---------------
1) Update protocol test modules
2) Update IGMP test plan, add test cases status and remove depulated items.
3) TAHI test dubug and try to run other test cases that haven't run
2012--7-03 week 26
This week:
---------------
1) Write and update RHEL5.9 Test Plans
  Tunnel test plan
    Update ip utility test
    Add IGMP/SCTP test over tunnel
  Protocol test plan
    Add TCP new feature test
    Add TCP state and flag test
  IPv6 test plan
    Add IPv6 TCP/UDP/SCTP over virtio_net device test
2) Run RHEL7 Alpha-2 pre-rc tire2 test: gre test failed
    All IGMP jobs blocked by bug 835003
    Part of SCTP job failed for bug 8ll and debug:
    http://leo.etherpad.corp.redhat.com/tahi-freebsd
4) RHEL7 jobs sync error, run some multihost jobs manually.
Next week:
---------------
1) Write and update RHEL5.9 Test Plans
2) Run RHEL7 Alpha-2 pre-rc tire2 test
3) IPv6 conformance test transfer
4) investigate 1x 2ports PCI-e NIC
2012-06-19 week 24
This week:
---------------
1) Update test cases
  TCP Performance
    /kernel/networking/tcp/Performance
  UDP Performance
    /kernel/networking/udp/Performance
  SCTP
    /kernel/networking/sctp/bugs/sctp_netfilter_dos_bz197387
  IPv6
    /kernel/networking/ipv6/bugs/raw_sockets_panic_bz743375
2) RHEL6.3 Performance test compared with rhel6.2
  http://10.66.12.114/performance/RHEL6
3) RHEL7 Alpha-2 pre-rc tire 1 testing
  https://beaker.engineering.redhat.com/jobs/249718
4) Learn TAHI test tools config
5) TAHI and USGv6 comparsion
  https://wiki.test.redhat.com/Leo/ipv6#A34TAHIandUSGv6comparison
Next week:
---------------
1) Finish bug 707901, how to set bug status?
3) RHEL5.9 Test Plans
4) RHEL7 Alpha-2 pre-rc test, analyse tire1 result and run tire 2 testing
5) IPv6 conformance test transfer
2012-06-12 week 23
This week:
---------------
1) Fill bug 831024
2) Write test case
  Multihost jobs on guests
    /kernel/networking/promiscuous/virt_multihost
    e.g. https://beaker.engineering.redhat.com/jobs/246423
  Vlan on tunnel
    /kernel/networking/vlan/vlan-on-tunnel-bz692013
3) Update test cases
  Protocol tests on virtual interface
    /kernel/networking/promiscuous/protocol_on_virtual_interface
  UDP Performance
    /kernel/networking/udp/Performance
4) Create TCMS test plan and TCMS runs for RHEL7
  Openvswitch test plan:
    https://tcms.engineering.redhat.com/plan/6457
  Tunnel Run:
    https://tcms.engineering.redhat.com/run/41005/?from_plan=5578
  SCTP Run:
    https://tcms.engineering.redhat.com/run/41009/?from_plan=5202
  IGMP Run:
    https://tcms.engineering.redhat.com/run/41013/?from_plan=5204
  IPv6 Run:
    https://tcms.engineering.redhat.com/run/41014/?from_plan=5192
Next week:
---------------
1) Finish bug 707901
2) Update test cases
3) RHEL5.9 Test Plans
4) RHEL6.2 to RHEL6.3 upgrading performance test
5) IPv6 conformance test transfer
6) check TAHI for address arch tests
2012-06-05 week 22
This week:
---------------
1) Verify RHEL6.3 bugs 814504 814288
    6.2.z bugs 816292 814286
2) All RHEL6.3 network protocol regression test passed based on distro RHEL6.3-20120531.0
    https://wiki.test.redhat.com/Leo/jobs
3) Update test case
  IPVS
    /kernel/networking/IPVS/NAT/
    /kernel/networking/IPVS/DR/
    /kernel/networking/IPVS/Schedule/
  IPv6
    /kernel/networking/ipv6/general/
4) Test bug IPVS bug 707901, Tunnel mode test failed, seem we doesn't backport a patch
5) RHCE exam and interview training (1.5 days)
Next week:
---------------
1) Finish bug 707901
2) Update other test cases
3) RHEL5.9 Test Plans

