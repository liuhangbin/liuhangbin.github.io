---
layout: post
title: weekly-report
date: 2015-05-25
description:
category:
Tags: []
---
==========================================
2014-12-31 week 52

    Summary:


    TODO:

    Issues

    ip addr show dynamic

    Wed:

    Verify bug 1119364

    Thu:

    PTO

    Report Bug 1177226 - scheduling while atomic: swapper/0/0x10000500

    Fri:

    Test bug 1076106

    Mon:

    Test bug 1076106, still could not reproduce, sanity only

    Tue:

    Report Bug 1177763 - [RFE] show ip dynamic address for IPv4

    Run Protocol RHEL7.1 S1 testing



==========================================
2014-12-24 week 51

    Summary:


    TODO:

    IOL Addr Arch testing

    Wed:

    OVS tech talk

    Thu:

    Add test restart_lo_bz744655

    Fri:

    DISC training

    Mon:

    Run IOL Addr Arch test and all PASSed

    verify bug 1077053

    Tue:

    Verify bug 

    bug 1087416

    Write test networking-socket-bugs-ping_init_sock_refcount_bz1086730



==========================================
2014-12-17 week 50

    Summary:


    TODO:

    RHEL 7.1 bug testing

    openstack network training

    mutt calendar

    write regression test for https://bugzilla.redhat.com/show_bug.cgi?id=1167003

    IKEv2 testing

    bug https://bugzilla.redhat.com/show_bug.cgi?id=1158748

    visa/xiecheng

    deal with bz 1153548 , 1171441, 1158748

    Issue:

    IPv6 mode remvoe on PPC64 LE IPv6 is buildin on RHEL7

    exec tcpdump background in a NETNS, then del the NETNS, tcpdump still running

    ip addr show dynamic

    Wed:

    Deal with USGv6 bugs

    Thu:

    Test RHEL7.1 bugs

    Fri:

    Verify bug 1114650

    Write Test case /kernel/networking/igmp/bugs/mld_route_alert_bz1114650

    Mon:

    c3750e-universalk9-mz.152-2.E.bin

    c3750e-universalk9-mz.150-2.SE7.bin

    Verify bug 1110118

    Tue:

    Write test case networking/igmp/bugs/mld_robustness_config_bz1110118

    Report Bug 1174540 - Could not get IPv6 address after stop NetworkManager and change interface name

    Report Bug 1174567 - Veth device was not deleted when delete netns if it has reference

    Test bug 1131503



==========================================
2014-12-10 week 49

    Summary:


    TODO:

    switch 3750 IPv6 support

    deal with bz 1153548 , 1171441, 1158748

    Wed:

    training

    Thu:

    training

    Fri:

    report Bug 1171441 - [TAHI][DHCP] DHCP_CONF.2.1.6: DHCP Server do not support EN and LL DUID format

    DHCPv6 Client 3.10.0-211.el7.x86_64_dhcp-4.2.5-34.el7.x86_64

    http://10.66.13.78/RHEL7.1_211.el7/DHCPv6_Client/

    http://10.66.13.78/RHEL7.1_211.el7/DHCPv6_Client_rfc3736_27_fail/ bz1156356

    DHCPv6 Server

    http://10.66.13.78/RHEL7.1_211.el7/DHCPv6_Server/

    http://10.66.13.78/RHEL7.1_211.el7/DHCPv6_Server_rfc3315_16-19_fail/ bz1171441

    DHCPv6 Relay

    http://10.66.13.78/RHEL7.1_211.el7/DHCPv6_Relay/

    Mon:

    report Bug 1171449 - [TAHI][IKEv2] libreswan still send IKE_SA_INIT request after receive IKE_SA_INIT Response

    Write IPv6 conformance testing summary

    Tue:

    Switch IPv6 Support: CAT3750X c3750e-universalk9-mz.150-2.SE.bin

    DHCP - DHCPv6 Individual Address Assignment

    DHCP - DHCPv6 Server Stateless Auto Configuration

    IPv6 Multicast: Multicast Listener Discovery (MLD) Protocol, Versions 1 and 2

    IPv6 Support for IPSec and IKEv2

    http://tools.cisco.com/ITDIT/CFN/jsp/by-feature-technology.jsp

    DHCPv6 Server bz1171441 testing

    Close bug 1171441




==========================================
2014-12-03 week 48

    Summary:


    TODO:

    6.7 bug review : https://url.corp.redhat.com/2f8631a


    visa request

    USGv6 IKEv2



    test bug 1156137

    FY16 hardware

    post approve

    Wed:

    meeting, read emails

    review RHEL 6.7 bug

    Thu:

    DEBUG IKEv2 IKEv2.EN.R.1.1.10.1,  IKEv2.EN.R.1.1.10.3

    test bug 1156137

    Fri:

    Report bug

     Bug 1168788 - [TAHI][IKEv2] IKEv2.EN.R.1.1.10.3: Add IKE_SA_INIT Response CERTREQ payload option support

    Bug 1168795 -        [TAHI][IKEv2] IKEv2.EN.R.1.1.10.1: Add asymmetric authentication support

    rerun TAHI Core test on kernel 211.el7

    Mon:

    http://10.66.13.78/RHEL7.1_211.el7/Core_Host/

    http://10.66.13.78/RHEL7.1_211.el7/IPsec_ICMP/

    http://10.66.13.78/RHEL7.1_211.el7/IPsec_UDP/

    Tue:

    http://10.66.13.78/RHEL7.1_211.el7/DHCPv6_Server/

    http://10.66.13.78/RHEL7.1_211.el7/DHCPv6_Client/

    http://10.66.13.78/RHEL7.1_211.el7/DHCPv6_Relay/



==========================================
2014-11-12 week 45

    Summary:


    TODO:

    update compass

    compare USGv6 testing items

    IPv6 route test case

    RHEL7.1 bug regression test case

    run test on RHEL7

    remove promious folder

    Ping Paul about x509 config

    send TAHI Core result to UNH

    transfer TAHI/IOL test to lxin

    transfer Protocol tests to Baoliang

    transfer polarion to zenghui, zhouxiong

    Set mail auto reply

    ask for leave 

    hotel http://www.grandhotelbrno.cz/index.html ?

    backup vpnc config

    flower warter

    Wed:

    Review and compare USGv6 test items

    Core

    Finish Spec

    Finish nd

    Finish Addr

    Finish PMTU

    Finish ICMP6

    DHCPv6 Client

    http://10.66.13.78/RHEL7.1_188.el7/DHCPv6_Client_rfc3646_28-29_fail/ asked via usg-list

    http://10.66.13.78/RHEL7.1_188.el7/DHCPv6_Client_rfc3646_30-31_pass/

    Thu:

    Team Building

    Fri:

    http://10.66.13.78/IKEv2/IKEv2_USGv6_196-197_pass/

    http://10.66.13.78/DHCP/DHCPv6_Client_rfc3646_10-11_rhel7_pass/ need developer confirm confirmed bz1147191

    http://10.66.13.78/IKEv2/IKEv2_USGv6_240-241_pass/

    Close bug 1159712

    Mon:

    http://10.66.13.78/RHEL7.1_188.el7/DHCPv6_Client_rfc3646_28-29_pass/

    Report bugs

    report Bug 1162002 - [TAHI][IKEv2] IKEv2.EN.I.1.1.6.1 Part C/D: No support for PRF and Integrity specific config

    Finish IKE testing review

    Tue:

    IKEv2 debug

    transfer jobs before travel



==========================================
2014-11-05 week 44

    Summary:

    IKEv2

    Note: UNH also have the following tests(all PASSed), but we do not have (Risk?)

    IKEv2.EN.I.1.1.6.1 (G)

    IKEv2.EN.I.1.1.6.2 (G)

    IKEv2.EN.R.1.1.6.1 (G)

    IKEv2.EN.R.1.1.6.2 (G)

    IKEv2.EN.R.1.1.10.1(B)

    IKEv2.EN.R.1.1.10.1(C)

    IKEv2.EN.R.1.1.10.3(B)

    IKEv2.EN.R.1.1.10.3(C)

    PASS

    http://10.66.13.78/IKEv2/IKEv2_USGv6/

    http://10.66.13.78/IKEv2/IKEv2_USGv6_36_pass/

    http://10.66.13.78/IKEv2/IKEv2_USGv6_55_pass/

    http://10.66.13.78/IKEv2/IKEv2_USGv6_69-70_pass/

    http://10.66.13.78/IKEv2/IKEv2_USGv6_185-187_pass/

    http://10.66.13.78/IKEv2/IKEv2_USGv6_198_pass/

    http://10.66.13.78/IKEv2/IKEv2_USGv6_237_pass/

    FAIL

    http://10.66.13.78/IKEv2/IKEv2_USGv6_9_fail/ bug 1158771

    http://10.66.13.78/IKEv2/IKEv2_USGv6_136_fail/ bug 1158771

    http://10.66.13.78/IKEv2/IKEv2_USGv6_183_fail/ bug 1131503

    http://10.66.13.78/IKEv2/IKEv2_USGv6_192-194_fail/ X509 CA config

    http://10.66.13.78/IKEv2/IKEv2_USGv6_196-197_fail/ bug 1052811

    http://10.66.13.78/IKEv2/IKEv2_USGv6_240-241_fail/ bug 1159712

    N/S, N/T

    http://10.66.13.78/IKEv2/IKEv2_USGv6_30-31_NS_fail/ bug 1162002

    http://10.66.13.78/IKEv2/IKEv2_USGv6_59-67_NS_fail/

    http://10.66.13.78/IKEv2/IKEv2_USGv6_122-123_NS_fail/ bug 1153548

    http://10.66.13.78/IKEv2/IKEv2_USGv6_159-160_NS_fail/

    http://10.66.13.78/IKEv2/IKEv2_USGv6_184_NT_fail/

    TODO:


    Wed:

    IKEv2 Debug

    Polarion Training

    Thu:

    Debug http://10.66.13.78/IKEv2/IKEv2_USGv6_59-67_NS_fail/

    report Bug 1158748 - [TAHI][IKEv2] IKEv2.EN.I.1.1.10.1: libreswan could not handle CERTREQ payload 

    Debug http://10.66.13.78/IKEv2/IKEv2_USGv6_136_fail/

    report Bug 1158771 - [TAHI][IKEv2] IKEv2.EN.I.1.1.3.1: libreswan send ICMP Fragment message after receive other endpoint's unreachable msg

    Fri:

    IKEv2 DEBUG, retest bugs

    Bug 1052811 - [TAHI][IKEv2]IKEv2.EN.I.1.1.11.1: Non zero RESERVED fields in IKE_SA_INIT response

    Bug 1152625 - [TAHI][IKEv2] IKEv2.EN.I.1.1.6.2 Part D: Integrity Algorithm AUTH_AES_XCBC_96 fail 

    Bug 1055865 - [TAHI][IKEv2] libreswan do not ignore the content of version bit

    Mon:

    FailedQA

    Bug 1052811 - [TAHI][IKEv2]IKEv2.EN.I.1.1.11.1: Non zero RESERVED fields in IKE_SA_INIT response

    Report bug

    Bug 1159712 - [TAHI][IKEv2] IKEv2.EN.R.2.1.1.1: Could not observe IKE_AUTH Response in tunnel mode

    Tue:

    DEBUG http://10.66.13.78/IKEv2/IKEv2_USGv6_192-194_fail/ 

    DEBUG IOL UNKNOWN result

    Team Building generate



==========================================
2014-10-29 week 43

    TODO:


    Summary:


    Wed:

    Polarion Training

    Thu:

    Help Ann debug IKEv2.EN.R.1.1.11.04(A), UNH failed this test on RHEL6.5, but we have passed

    Close Bug 1118121 - [TAHI][IKEv2]IKEv2 device should ignore an IKE request message whose Response bit is set.

    DHCPv6 Server http://10.66.13.78/RHEL7.1_188.el7/DHCPv6_Server/

    DHCPv6 Relay http://10.66.13.78/RHEL7.1_188.el7/DHCPv6_Relay/r

    Fri:

    Review and ack RHEL7.1 bugs

    Run protocol tier1 test on PPC64 LE machines

    https://beaker.engineering.redhat.com/jobs/781875

    https://beaker.engineering.redhat.com/jobs/782243

    run lksctp-tools test manually

    report Bug 1156356 - [TAHI][DHCPv6] DHCP_CONF.7.1.9: found Opt_DHCPv6_CID in dhcpv6 information request message

    DHCPv6 Client http://10.66.13.78/RHEL7.1_188.el7/DHCPv6_Client/

    Core Host http://10.66.13.78/RHEL7.1_188.el7/Core_Host_nd_138_pass/

    Core Host http://10.66.13.78/RHEL7.1_188.el7/Core_Host_nd_169_pass/

    Mon:

    DHCPv6 Client http://10.66.13.78/RHEL7.1_188.el7/DHCPv6_Client_rfc3736_26_pass/

    IKEv2 debuging

     http://10.66.13.78/IKEv2/IKEv2_ENODE_239_fail/

    report Bug 1157379 - [TAHI][IKEv2] IKEv2.EN.R.1.3.3.1: Non RESERVED fields in INFORMATIONAL request

    Tue:

    Debug IKEv2 http://10.66.13.78/IKEv2/IKEv2_USGv6/



==========================================
2014-10-22 week 42

    TODO:

    Start TAHI Core/Addr_arch/IPsec/IKEv2/DHCPv6 testing

    edit slide https://mojo.redhat.com/docs/DOC-988088

    Summary:

    Report Bugs

    Bug 1153548 - [TAHI][IKEv2] IKEv2.EN.I.2.1.1.1: Sending IKE_AUTH request failed

    Polarion training 3 Days

    USGv6 Testing

    Core Host http://10.66.13.78/RHEL7.1_188.el7/Core_Host/ Two items failed in ND

    IPsec ICMP http://10.66.13.78/RHEL7.1_188.el7/IPsec_ICMP/ipsec.p2/index.html

    IPsec UDP http://10.66.13.78/RHEL7.1_188.el7/IPsec_UDP/ipsec.p2/index.html


==========================================
2014-10-15 week 41

    TODO:

    IKEv2 testing  (email) ?

    Start TAHI Core/Addr_arch/IPsec/IKEv2/DHCPv6 testing

    edit slide https://mojo.redhat.com/docs/DOC-988088

    Summary:

    Debug 1147191 and find the root issue. wait for developer's patch

    Finish debug 1147191, developer admit it is a bug and give a fix

    Pass all DHCPv6 relay test

    Write bug 1143429 's reproducer

    Review 7.1 bugs and Update RHEL7.1 IPv6 Test Plan

    Start IKEv2 testing and only test Items UNH tested

    Report Bugs

    Bug 1152368 - Should return an error when adding an already existing tunnel 

    Bug 1152625 - [TAHI][IKEv2] IKEv2.EN.I.1.1.6.2 Part D: Integrity Algorithm AUTH_AES_XCBC_96 fail 

    Bug 1152803 - confread.c:296: load_setup: Assertion `kw->keyword.keydef->validity & kv_config' failed.


==========================================
2014-10-08 week 40

    TODO:

    IKEv2 testing  (email) ?

    USGv6 Core host debug

    Start TAHI Core/Addr_arch/IPsec/IKEv2/DHCPv6 testing

    Summary:

    DEBUG 1146861 and closed as not a bug

    DEBUG 1147191 and still fail, wait for developer's feed back


==========================================
2014-10-01 week 39

    Summary:


    TODO:

    TAHI DHCPv6 debug

    debug TAHI core host fail

    AR: bug 1133734


    Wed:

    Debug Core host fail issue on RHEL6.6

    Install IOL latest version and still got unknown result, debugging

    Thu:

    Finish debug Core host test issue. Thanks for lxin's help

    update netfilter/bugs/ipv6_fragment_reassembly_bz726807

    Fri:

    DEBUG DHCPv6 failure, find out the reason and closed bug 1133734

    debug rfc3316 test 58, find the resaon, modified the rmt file and test passed. close bug 1133880

    Report bug Bug 1146861 - [TAHI][DHCPv6] Test DHCP_CONF.1.3.3 Part D E: client do not send Solicit message after receives a NotOnLink status

    Mon:

    Report Bug 1147240 - [TAHI][DHCPv6] Test DHCP_CONF.9.1.2 Part A: Relay-forwad Message's Hop Limit should be 32

    DHCP bug 1038374 was closed as NOTABUG, debug the case issue and fixed it

    Tue:

    Report bug Bug 1147191 - [TAHI][DHCPv6] Test DHCP_CONF.4.1.4 Part C, D: Cannot get DNS Query message after restart interface

    Finish DEBUG DHCPv6 Client, have reported bugs for some issues.

    Start debug DHCPv6 Relay



==========================================
2014-09-24 week 38

    Summary:


    TODO:

    TAHI DHCPv6 debug

    debug TAHI core host fail

    AR: bug 1048702 1133734

    IKEv2 testing  (email) ?

    IKE debug(icmp)

    Wed:

    update ltp network-stress test cases

    update igmp/ipv6 test cases

    Thu:

    update workflow-network with multi folder and filter support

    sync usgv6 bug status

    Fri:

    review bug 1048702

    IKE debug

    Mon:

    update autojob and workflow-network

    debug IKEv2 bug 1049802

    Tue:

    DEBUG DHCPv6



==========================================
2014-09-17 week 37

    Summary:


    TODO:


    Wed:

    Metting

    test case update:ipv6/proc, misc/regression, igmp/force_igmp_version:

    Thu:

    LTP test suite update

    Fri:

    LTP test suite update

    Mon:

    Config FlexNet server

    USGv6 DHCP result debug

    Tue:

    run 6.6 ipv6 test on i386, ppc64 and s390x

    run igmp test on i386

    report bug https://bugzilla.redhat.com/show_bug.cgi?id=1142099



==========================================
2014-09-10 week 36

    Summary:


    TODO:


    Wed:

    Testing RHEL6.6 bugs

    Debug USGv6 MLDv2 issues

    Thu:

    Verify Bug 1049052 - Request backport of netpoll_send_skb_on_dev() patch to RHEL 6.5

    Fri:

    Sanity only  Bug 1004164 - Use after free in tcp_v6_do_rcv

    Sanity only Bug 1110192 - RHEL6 backport of "neigh: set nud_state to NUD_INCOMPLETE when probing router reachability"

    Mon:


    Tue:

    update IGMP test cases: snooping/force_igmp_version/conformance



==========================================
2014-09-03 week 35

    Summary:


    TODO:


    Wed:

    run TAHI Core host test for RHEL6.6, found some ND issues, debugging

    Thu:

    Run TAHI Core router, ipsec, dhcp snmp testing for RHEL6.6 S3

    Verify Bug 1095252 - oops in netpoll_poll_dev()

    Fri:

    finish  Corporate Compliance and Ethics Training Program

    Mon:

    Finish run TAHI test on RHEL6.6 S3

    finish compass

    rewrite netconsole test case

    Tue:

    Verify Bug 1026520 - Backport MLD v1/v2 switchback timeout fixes

    verify Bug 1087414 - CVE-2014-2851 kernel: net: ping: refcount issue in ping_init_sock() function [rhel-6.6]

    Verify Bug 990694 - [RFE] Ability to set the DSCP field on a per-packet basis in IPv4



==========================================
2014-08-27 week 34

    Summary:


    TODO:


    Wed:

    Update IPv6 proc, snmp test case

    debug IKEv2 test, passed with plutodebug=all

    Thu:

    analise RHEL5.11 test result and update test case

    Fri:

    debug TAHI DHCPv6 client

    report Bug 1132892 - [TAHI][DHCPv6] dhclient -6 lost ipv6 address after link down and up 

    Mon:

    Finish debug DHCPv6 Client rfc3315/C_RFC3315_CnfReply.seq, test pass now

    report Bug 1133465 - [TAHI][DHCPv6] dhclient did not send a Decline msg when find address already used on the link

    Tue:

    Report Bug 1133734 - [TAHI][DHCPv6] First confirm message elapsed-time is not zero.

    Report Bug 1133782 - [TAHI][DHCPv6] RT of Confirm is incorrect 

    Report bug Bug 1133880 - [TAHI][DHCPv6] Client still transmit Release message after got Reply 



==========================================
2014-08-20 week 33

    Summary:


    TODO:


    Wed:


    Thu:

     Bug 1129964 - virtio_net do not support multicast packets count

    Fri:

    report bug  Bug 1130777 - BUG: unable to handle kernel paging request at 0000123c000011fc

    update igmp/bugs

    RHEL5.11 ipv6 and igmp job submit

    Mon:

    update igmp test plan

    update bkr workflow-network

    update 4 igmp bugs regression test

    Tue:

    run fisrt DHCPv6 client rand

    DEBUG IKEv2 IKEv2-EN-R-1-1-6-8-A, try to update test case to catch up UNH

    report Bug 1131503 - [TAHI][IKEv2]IKEv2.EN.R.1.1.6.8.A: responder sends a non-zero responder SPI when receive invalid KE payload



==========================================
2014-08-13 week 32

    Summary:


    TODO:


    Wed:

    update USGv6 test plan

    Verify Bug 1093806 - Incorrect backport of busy poll

    Thu:

    keep on igmp snooping...

    Fri:

    finish igmp snooping

    submit RHEL6 jobs

    Mon:

    update IPv6 proc test case

    Tue:

    finish update ipv6 proc



==========================================
2014-08-06 week 31

    Summary:


    TODO:


    Wed:

    Meeting, igmp test case designing

    Thu:

    write IGMP snooping test case

    Fri:

    keep on write IGMP snooping testing...

    Mon:

    update bkr workflow-network with subtest.desc support

    update usgv6 test plan

    Tue:

    IGMP bugs review

    update pktgen and network/common,debug test log eval issue



==========================================
2014-07-30 week 30

    Summary:


    TODO:


    Wed:

    look bug 1110192

    add proc_net_uid_unsigned-bz996124 regression test

    Thu:

    Polarion training

    virt test with our own NIC/topo searching

    Fri:

    team building

    trinity connection update

    Mon:

    netlink sendto debug

    verify Bug 1049733 - [FJ6.5 BUG] ipv6: don't call fib6_run_gc() until routing is ready

    Tue:

    write bz1049733 test case

    update networking/common with attach-interface

    Verify Bug 1023278 - The net.ipv4.neigh.default.* settings are not applied to devices during bootup

    add regression test apply_neigh_default_to_dev-bz1023278


==========================================
2014-07-23 week 29

    Summary:


    TODO:

    IKEv2 log debug, esp 

    Test IKEv2.EN.R.1.1.6.8: Sending INVALID_KE_PAYLOAD in Initial Exchange

    Test RHEL6.6 bugs

    run igmp test on RHEL5.11 S1

    Wed:

    submit RHEL6 alpha1 job

    learn polarion usage

    Thu:

    review and take 6.6 bugs

    update ipv6 wrong_headroom_check

    update bkr workflow-network

    Fri:

    Verify Bug 1070363 - Cannot change /proc/sys/net/ipv4/neigh/default/gc_* values

    Mon:

    Verify Bug 1073711 - The /proc/sys/net/ipv4/neigh/unres_qlen value is 0 instead of 3 in RHEL 6.5

    DEBUG RHEL6.6 IGMP test failure

    Tue:

    verify Bug 996124 - UIDs output in /proc/net/* should be unsigned

    verify Bug 1028372 - unreachable ipv6 routes are not getting deleted


==========================================
2014-07-16 week 28

    Summary:


    TODO:

    Wed:


    Thu:

    report Bug 1118121 - [TAHI][IKEv2]IKEv2 device should ignore an IKE request message whose Response bit is set.

    DEBUG USGv6 failures

    Verify Bug 1109888 - [panic] 2.6.18-390.el5debug BUG: unable to handle kernel paging request at virtual address

    Fri:

    review lxin's netfilter test case

    Run Fujitsu regression teat on RHEL5.11 Sanpshot 1

    Run TAHI Core_Host/Core_Router/IPsec test on RHEL5.11 392.el5

    http://10.66.13.78/RHEL5.11_392.el5/

    write USGv6 test plan

    Mon:

    Finish write USGv6 test plan

    Report Bug 1119175 - netfilter: ctnetlink: don't permit ct creation with random tuple

    Tue:

    Update USGv6 test plan with interoperability testing

    debug USGv6 IKEv2 test result




==========================================
2014-07-09 week 27

    Summary:


    TODO:


    Wed:

    meeting

    RHEL5.11 jobs debig

    test bug 1109888, could not reproduce

    Thu:

    upload ipv6 test case

    Fri:

    report Bug 1116257 - Linux kernel can ping remote ipv6 address from lo 

    Mon:

    update network-workflow

    Tue:

    Write kpatch


==========================================

2014-07-02 week 26

    Summary:


    TODO:

    learn polarion usage

    Write 999362 reproducer

    Wed:

    review protocol test plan

    help test [Bug 1045356] kernel doesn't send echo request after set ip xfrm sel

    Learn polarion

    Thu:

    auto_job update

    debug USGV6 test, still don't know why the result is UNKNOWN, will ask Ann

    Fri:

    RHEL5.11 result debug

    Mon:

    review RHEL7.1 bugs

    Verify Bug 1090806 - ipv6 route fails to recover after router enters FAILED neighbor state

    Verify Bug 1029585 - ipv6 route fails to recover after router enters FAILED neighbor state

    verify Bug 1035881 - Kernel: net: leakage of uninitialized memory to user-space via recv syscalls [rhel-5.11]

    Verify Bug 1035883 - Kernel: net: leakage of uninitialized memory to user-space via recv syscalls [rhel-6.6]

    Tue:

    Report Bug 1114650 - [TAHI][MLDv2][Listener]Need check hop limit and router alert for MLDv2 query message

    Write bug 1035883 regression test

    Verify bug Bug 999362 - CVE-2013-0343 kernel: handling of IPv6 temporary addresses [rhel-5.11]



==========================================
2014-06-25 week 25

    Summary:


    TODO:

    MLDv2 debug

    Wed:

    MLDv2 DEBUG group 3

    Thu:

    Report Bug 1111200 [TAHI][MLDv2][Listener] Linux kernel send MLDv2 report message with multi records when join src group

    MLDv2 DEBUG group 3

    Fri:

    update auto job

    DEBUG MLDv2 group 4

    Mon:

    DEBUG MLDv2 group 4

    Tue:

    DEBUG MLDv2 group 5

    Help test bug Bug 1108572 - tg3 tso bug



==========================================
2014-06-18 week 24

    Summary:


    TODO:

    igmp test plan

    IOL debug

    Wed:

    USGv6 env config

    Thu:

    Bug 1108572 - tg3 tso bug

    Bug 1108579 - veth get stats64 NULL pointer

    Fri:

    debug cisco issue

    bz regression

    send IPv6 test plan

    Mon:

    [Private] Bug 1109627 - scsi softirq NULL pointer

    Tue:

    report  Bug 1110118 - [TAHI][MLDv2][Listener] Linux do not support MLDv2 Report message robustness value config



==========================================
2014-06-11 week 23

    Summary:


    TODO:

    bkr workflow network list update 

    Wed:

    Report Bug 1104503 - RHEL6 could not add specific gre name

    Thu:

    IPv6 test plan

    Fri:

    IPv6 test plan

    Mon:

    help cisco debug USGv6 spec issue

    Debug UNH fails on RHEL7

    Tue:

    report Bug 1107500 - interface does not init link local address after flush 

    run bz1090806 regresson test

    tunnel/ipsec transfer



==========================================
2014-06-04 week 22

    Summary:


    TODO:

    tunnel/ipsec transfer

    RHEL6.6 test plan

    DO USGv6 comformance test

    cc Jan about the ipsec test plan

    Wed:


    Thu:

    init compass

    Mon:

    tunnel transfer

    Tue:

    report Bug 1104038 - kernel does not insmod ip6_tunnel automatically

    Bug 1104164 - tap device disappear on RHEL6.5

    update netlink/iproute2



==========================================
2014-05-28 week 21

    Summary:


    TODO:

    compass

    beaker tahi test

    RHEL6.6 test plan

    RHEL5.11 test result

    Wed:

    Metting

    analise kcov net/core and prepare core test

    Thu:

    ethtool update

    Fri:

    tcp perf update

    Mon:

    networking common test update

    run 5.11 tire1 regression

    Tue:

    ipv6 tahi test debug

    review RHEL6 IPv6 bugs and prepare test plan



==========================================
2014-05-21 week 20

    Summary:


    TODO:


    Wed:

    meeting and interview

    write pktgen test case

    Thu:

    write ipv6 testing summary

    sanity only  Bug 1011931 - CVE-2013-4387 Kernel: net: IPv6: panic when UFO=On for an interface [rhel-7.0] 

    Mon:

    setup openstack env

    Tue:

    teambuilding


==========================================
2014-05-14 week 19

    Summary:


    TODO:


    Wed:

    IPv6: https://beaker.engineering.redhat.com/jobs/646211

    Fujitse regression https://beaker.engineering.redhat.com/jobs/646226

    netlink https://beaker.engineering.redhat.com/jobs/646213

    Thu:

    write conntrack-tools

    Fri:

    udp https://beaker.engineering.redhat.com/jobs/646448

    report Bug 1096066 - xfs: reading directory .: Structure needs cleaning

    tcp https://beaker.engineering.redhat.com/jobs/648889

    Mon:

    netfilter: https://beaker.engineering.redhat.com/jobs/647977

    sctp: https://beaker.engineering.redhat.com/jobs/649355

    socket https://beaker.engineering.redhat.com/jobs/649593

    tunnel: https://beaker.engineering.redhat.com/jobs/649589

    Tue:

    write pktgen test case



==========================================
2014-05-07 week 18

    Summary:


    TODO:


    Wed:

    review bugs from https://url.corp.redhat.com/a059d91

    submit kcov jobs

    Mon:

    test coverage http://10.66.13.37/kcov/RHEL7_121.el7/kcov.comb/

    Tue:

    verify Bug 1040183 - Labeled networking incorrectly labels SYN-ACK packet on AF_INET sockets

    submit RHEL7 RC3 tests

     Protocol_tire1 https://beaker.engineering.redhat.com/jobs/646209

    IGMP: https://beaker.engineering.redhat.com/jobs/64621


==========================================
2014-04-30 week 17

    Summary:

    Write bkr network-workflow

    Write test case

    TODO



==========================================
2014-04-23 week 16

    Summary:

    Write test case

    write bkr plugin

    TODO:


    Wed:

    Verify Bug 985657 - oom-killer perform bad and trigger kernel panicBug 985657 - oom-killer perform bad and trigger kernel panic

    Write test case memory_allocation_fails_bz608980

    Write test case netfilter_NFQUEUE_failopen_bz819638

    Thu:

    update netfilter most_rules

    add bridge_iptables_REJECT_bz717407

    add null_pointer_in_nf_ct_frag6_reasm_bz833412

    Fri:

    add display_netlabel_bz918803

    add audit_convert_sit_to_string_bz785936

    add ipv4 support for fragment_reassembly

    Mon:

    report Bug 1089581 - kernel panic at nf_nat_cleanup_conntrack+0x40/0x70

    update test cases

    Tue:

    Write bkr plugin



==========================================
2014-04-16  week 15

    Summary:

    e173985 netfilter: it will block there if we run cmd in background with run function

    f1aa6fe netfilter: forgot to add bug info

    9bf75d0 netfilter: add ipv6_fragment_reassembly_bz726807 regression test

    7493992 ipv6: do not check unknown system and treat RHEL7 the same with RHEL6

    94aea6d Merge branch 'master' of ssh://pkgs.devel.redhat.com/tests/kernel

    e55d2d5 common: add ipv6 support for get_iface_and_addr

    b069574 ipv6: disable avc check and only use ip cmd for add_ipv6_addr_on_dummy_dev_bz742588

    4c6dae6 ipv6: set /proc/sys/net/core/somaxconn to 65535 as it will fail when set to 65536 on RHEL7

    5b23a37 ipv6: comment RS test due to bug 1082933

    97e0119 netlink: ip token ping test failed, let's disable_firewall and try again

    ba6b563 netlink: as gretap was hard code in linux, use gretap0 as its test name

    Report bug 1085758, 106498

    TODO:

    netfilter bug and RHEL6.6 bug review


    Wed:

    Metting

    report Bug 1085758 - Can't observe CREATE_CHILD_SA request for rekey         

    Thu:

    82e0b46 ipv6: do not check unknown system and treat RHEL7 the same with RHEL6

    94aea6d Merge branch 'master' of ssh://pkgs.devel.redhat.com/tests/kernel

    e55d2d5 common: add ipv6 support for get_iface_and_addr

    b069574 ipv6: disable avc check and only use ip cmd for add_ipv6_addr_on_dummy_dev_bz742588

    4c6dae6 ipv6: set /proc/sys/net/core/somaxconn to 65535 as it will fail when set to 65536 on RHEL7

    5b23a37 ipv6: comment RS test due to bug 1082933

    review RHEL6 netfilter bugs and make sure which regression should write

    Fri:

    report Bug 1086498 - could not add specific tunnel name with ip tunnel

    Mon:

    learn netlink programing

    review all RHEL6 bugs and add todo list for protocols

    Tue:

    add ipv6_fragment_reassembly_bz726807 regression test



==========================================
2014-04-08 week 14

    Summary:

    update netfilter test case and test plan

    update tunnel transport test case

    wirte libndp test

    finish libndp errata

    test bug 1052834

    TODO:

    test case update

    ask developer change libndp errata to qe state

    netfilter bugs on RHEL6  https://url.corp.redhat.com/04d7242

    Wed:

    PTO half day

    metting

    write networking/ipv6/libndp

    Thu:

    update netfilter test plan

    update tunnel transport test case

    Fri:


    Mon:

    update netfilter test and send patches to baseos

    Tue:

    test Bug 1052834 - [TAHI][IKEv2]libreswan retransmits IKE_SA_INIT instread of CREATE_CHILD_SA after CHILD_SA Lifetime timeout

    Change libndp errata to REL PREP

    Bug 1085758 -        Can't observe CREATE_CHILD_SA request for rekey



==========================================
2014-04-01 week 13

    Summary:

    Verify bug 1075060 836427 1023947 1016064

    sanity only bug 1077351

    report bug 1082933

    write test test case sctp_check_wrong_path_bz830716

    TODO:


    Wed:

    Metting

    write bridge case networking/netlink/iproute2

    Thu:

    Verify Bug 1075060 - CVE-2014-2309 Kernel: net: IPv6: crash due to router advertisement flooding [rhel-7.0] 

    Write sctp_check_wrong_path_bz830716

    Fri:

    Verify Bug 836427 - [FJ7.0 Bug] ipv6: fix incorrect ipsec fragment

    Sanity only  Bug 1077351 - CVE-2014-2523 kernel: netfilter: nf_conntrack_dccp: incorrect skb_header_pointer API usages [rhel-7.0]

    Mon:

    Verify Bug 1023947 - Backport IPv6 MLD version switchback fixes

    Tue:

    reprot Bug 1082933 - ndptool could not monitor RS message

    Verify Bug 1016064 - libndp: NDP option decoder issues


==========================================
2014-03-28 week 12

    Summary:

    Verify 866518, 1073962, 1065257, 1074491, 1035884, 1007739, 1070716, 1070135, 1058284, 998398

    Sanity only 1073602, 1058782, 1062308, 1068666

    review RHEL6.6 bugs

    Write RHEL6.6 regression test

    TODO:

    test Bug 1065257 - ipv6: bridge: system stops responding to neighbor solicitation

    review Bug 997138 -        [RHEL6.5][kernel] WARNING: at net/sched/sch_generic.c:261 dev_watchdog

    Bug 998398 - CVE-2013-4350 kernel: net: sctp: ipv6 ipsec encryption bug in sctp_v6_xmit [rhel-7]

    sinoff ipv6 feature testing

    RHEL6.6 test case writting https://vault.engineering.redhat.com/showRequest?requestid=1649


    Wed:

    Finish review RHEL6.6 bugs

    Sanity only  Bug 1073602 - Add padding to networking structs frozen by kABI whitelist

    Verify Bug 866518 - CVE-2012-4565 kernel: net: divide by zero in tcp algorithm illinois [rhel-7]

    verify Bug 1073962 - possible memory corruption when netns is destroyed with ip fragments in-flight

    Thu:

    Verify Bug 1065257 - ipv6: bridge: system stops responding to neighbor solicitation

    verify Bug 1074491 - ipv6: bridge: possible denial of service with malformed MLD query

    verify Bug 1035884 - Kernel: net: leakage of uninitialized memory to user-space via recv syscalls

    verify Bug 1007739 - CVE-2013-4343 Kernel: net: use-after-free TUNSETIFF [rhel-7.0]

    Fri:

    Sanity only Bug 1058782 - Kernel: netfilter: nf_nat: leakage of uninitialized buffer in IRC NAT helper [rhel-7.0]

    Verify Bug 1070716 - CVE-2014-0101 kernel: net: sctp: null pointer dereference when processing authenticated cookie_echo chunk [rhel-7.0]

    Verify Bug 1070135 - ip tunnel add gre0 mode gre return failed

    Sanity only Bug 1062308 - Make neigh_priv_len member of net_device structure larger 

    Verify Bug 1058284 - [GSS 7.0] 32bit applications on 64-bit kernels will always fail in sctp_connectx

    Mon:

    Sanity only  Bug 1068666 - fix incorrect skb_tail_offset usage (netpoll and ipv4 mrouting) 

    Verify Bug 998398 - CVE-2013-4350 kernel: net: sctp: ipv6 ipsec encryption bug in sctp_v6_xmit [rhel-7]

    Tue:

    Write test case sctp_hang_with_certain_buffer_sizes_bz809792


==========================================
2014-03-19 week 11

    Summary:

    Verify bug 1067042

    Report bug 1077053, 1077968

    Set up pure IPv6 env and run protocol tire1 testing

    DEBUG TAHI SNMP test

    update tcms netfilter test plan

    Review RHEL6.6 bugs

    TODO:

    RHEL7 bug verify https://url.corp.redhat.com/3eb5c6c

    RHEL6.6 review bugs https://url.corp.redhat.com/9c88869 

    Priority bugs with qa_ack+: https://url.corp.redhat.com/0a42fd3

    All bugs with qa_ack+: https://url.corp.redhat.com/8a92da7 (400 kernel bugs total, so less than 120 network bugs would be better)

    network-qe ack+ bugs https://url.corp.redhat.com/00a952f

    ipv6 only test


    Wed:

    Verify Bug 1067042 - missing "--queue-bypass" functionality

    update tcms netfilter test plan

    ipv6 only env: try update RHEL6 to RHEL7

    tahi snmp MIB debug

    Thu:

    report Bug 1075844 - curl do not support ipv6 on RHEL6.5

    report Bug 1075853 - Unable to open file /root/preupgrade/result.xml

    report Bug 1075854 - [TAHI] [SNMP] RFC4293 IP-MIB IP Statistics Table ipSystemStatsInOctets Counter Check

    report Bug 1075855 - [TAHI] [SNMP] RFC4293 ICMP Statistics icmpStatsInMsgs Counter Check

    review RHEL6.6 bugs

    Fri:

    revirew RHEL6.6 bugs

    set up ipv6 only env repos

    Mon:

    report Bug 1077053 - Can not ping ipv6 address after restart loopback interface

    Tue:

    run pure ipv6 tire test

    report Bug 1077968 - ioctl can not get interface info if we flush ipv4 address




==========================================
2014-03-12 week 10

    Summary:

    Finish TAHI testing http://10.66.13.78/RHEL7_105.el7/

    verify bug 872571 916124 1065299

    sanity only bug 1054483 1030994

    report bug 1074748 1075146

    write ltp netns, iptables test

    TODO:

    LTP netns and iproute patch

    TAHI

    IPv6 only

    Wed:

    Meeting

    debug test case

    Thu:

    verify Bug 872571 - [7.0FP] IPv6 Testing

    Sanity only Bug 1054483 - TPROXY+TCP early demux = sk memory leak

    Write netns test case

    Fri:

    verify Bug 916124 - RHEL7: Avoid most common networking KABI workarounds preventively

    Sanity only Bug 1030994 - RHEL7: Extract KABI breaking changes from proposed patch (conntrack / struct netns_ct)

    Mon:

    write test case ltp/iptables

    run tahi test

    ltp netns patch

    Tue:

    report  Bug 1074748 - File exists when add vti device

    verify Bug 1065299 - Bad handling of offloaded TCP checksums when fragmenting IPv4

    report Bug 1075146] /lib64/libnetsnmptrapd.so.31: undefined symbol: my_progname




==========================================
2014-03-05 week 9

    Summary:

    Finish compass

    update auto_job

    Report bug 1071709, 1071714, 1071716

    Verify bug 1014599

    Close bug 994436

    Sanity only bug 797703

    Config IPv6 environment, can start RHEL6 install, RHEL7 install still have some issue

    TODO:

    compass

    config kvm env

    debug auto_job issue

    Wed:

    sanity only Bug 797703 - [HP 7.0 FEAT]: Support of direct network access with macvtap/vhost as well as zero copy capability

    Thu:

    Finish compass

    Finish config IPv6 only environment

    Fri:

    debug ipv6 only env, only could install RHEL6 at present...

    testing bug 1014599

    [246912.597435] IPv4: Attempt to release TCP socket in state 1 ffff8804345b8e80

    [246912.653835] IPv4: Attempt to release TCP socket in state 1 ffff8804345b8e80

    [root@hp-dl385pg8-07 ~]# uname -r

    3.10.0-20.el7.x86_64

    # cat /proc/meminfo | grep SUnreclaim

    SUnreclaim:        41460 kB


    Mon:

    report Bug 1071709 - systemd-udevd Not tainted 3.10.0-97.el7.x86_64

    report Bug 1071714 - systemd-journal Not tainted 3.10.0-97.el7.x86_64

    report Bug 1071716 - WARNING: at drivers/tty/tty_ldisc.c

    Close Bug 994436 - IPv6 SCTP performance is very slow 

    Tue:

    verify Bug 1014599 - backport fixes for UFO processing

    update auto_job


==========================================
2014-02-26 week 8

    Summary:

    Verify bug 875901, 958330

    update netfilter/netlink/common test case

    TODO:

    eng-ops sync kernels

    compass

    ipv6 only env

    Wed:

    update netifilter test cases

    Mettings

    Thu:

    PTO

    Fri:

    write ipset test

    set up ipv6 environment on 7F

    Mon:

    config ipv6 only env with pxe support

    test bug 958330

    Write ipset test

    Tue:

    Verify Bug 875901 - RFE request for backporting IPv6 NAT from netfilter

    verify Bug 958330 - [Intel 7.0 FEAT] Add upstream Low Latency Socket support to RHEL7.0



==========================================
2014-02-18 week 7

    Summary:


    TODO:

    Let tzhang test bug 1032118 and 996122

    test bug 998398, 916037

    test ipv6 env console

    retest [Bug 990386] [TAHI][PMTU] Test v6LC.4.1.4: Reduce PMTU On-link failed

    Wed:

    close bug 916037

    Thu:

    update lamp

    need info for bug 998398

    add sctp_darn state test

    Fri:

    add rds-socket-recv-leak-memory regression test

    close Bug 992908 - backport "net: rtm_to_ifaddr: free ifa if ifa_cacheinfo processing fails

    close  Bug 998588 - Regression fixes after commit 56b765b79 ("htb: improved accuracy at high rates")

    sanity only Bug 1004165 - Use after free in tcp_v6_do_rcv

    Mon:

    verify Bug 1049141 - Mark vxlan_dev flags with VXLAN_F_IPV6 properly

    Bug 1049092 - [TAHI][MLDv2][Listener] MLDv2 retransmit report should use TO_EX instead of IS_EX

    Write case fib6_clean_all_bz809297

    debug common sync isue, change sync to net_sync

    Report bug  Bug 1065909 - formatting error of name types in "Consistent Network Device Naming" Chapter

    Tue:

    DEBUG tunnel test case

    read libndp code


==========================================
2014-02-12 week 6

    Summary:


    TODO:

    send autojob patch

    tunctl del cnm0 failed

    Sat:

    review RHEL7 SCTP DOC

    fix openstack environment

    Update performance test

    Mon:

    check the S6 test result and update TCP test case

    Tue:

    Update tunctl for RHEL7 supprt and send 6 commits

    review RHEL7 bugs

    1. reset qa_contact to network-qe for bug 991026




==========================================
2014-01-29 week 4

    Summary:


    TODO:

    Write netns test case

    Wed:

    network-qe and kernel-qa meeting

    rm unused cases in networking

    Report Bug 1056451 - bkr-workflow-simple use the same taskparam for every tasks

    Finish testing  Bug 1020579 - bridge mdb can't dump IPv6 group info

    Thu:

    Add netns test for ip_link.sh

    rm unused test cases

    Fri:

    update auto_job.sh

    submit protocol test for snapshot4

    test Addr_arch and Core_Host with IOL

    Sun:

    Submit s4 jobs(lots of machines failed install RHEL7 distro, resubmit them)

    Write klogin(kinit refresh tool)

    Mon:

    Report Bug 1058148 - IPv4 SCTP performance is very slow with 10G NIC on RHEL7



==========================================



==========================================
2014-01-22 week 3

    Summary:

    Report Bug 1053325 - kernel BUG at kernel/timer.c:930!

    Report Bug 1054628 - Host kernel panic when we add too much igmp group in guest

    Report Bug 1054631 - kdump kernel panic - not syncing: An NMI occurred

    Report Bug 1055406 - [TAHI][IKEv2]libreswan do not support higher minor version number

    Report Bug 1055865 - [TAHI][IKEv2] libreswan do not ignore the content of version bit

    Finish debug IKEv2_ENODE/ test

    TODO:

    DEBUG RHEL7 Snapshot2 test runs

    Wed:

    Network-qe meeting and Kernel-qa meeting

    Review Bug 1024631 - Labeled networking incorrectly labels SYN-ACK packet on AF_INET sockets

    Report Bug 1053325 - kernel BUG at kernel/timer.c:930!

    DEBUG IKEv2_35 Test IKEv2.EN.I.1.1.6.2 Part C: Encryption Algorithm ENCR_NULL

    DEBUG IKEv2 test to 120, previous test all blocked by bug 1053048 and 1052834

    Review bug  Bug 1052884 - Implement new IPv6 address flag IFA_F_NOPREFIXROUTE to allow user space to add addresses without creating a prefix route

    Thu:

    Test bug 1049092 (MLDv2)'s test kernel, make sure it passed the TO_EX check, we can start MLDv2 DEBUG now

    Review  Bug 1040946 - Stable 3.10.21-3.10.24 backport

    Interview with Dickenson, Russell <rdickens@>

    Test IKEv2_59, successfully build cert keys with xinlong's help, but the test hung while running, don't know why

    looks like MLDv2 Listener on HUT doesn't work, read the code and try to debug.

    Fri:

    Report Bug 1054628 - Host kernel panic when we add too much igmp group in guest

    Report Bug 1054631 - kdump kernel panic - not syncing: An NMI occurred

    review Bug 1054483 - TPROXY+TCP early demux = sk memory leak

    Test Bug 1020579 - bridge mdb can't dump IPv6 group info

    Test Bug 1024631 - Labeled networking incorrectly labels SYN-ACK packet on AF_INET sockets

    Mon:

    Retest Bug 1024631 - Labeled networking incorrectly labels SYN-ACK packet on AF_INET sockets, make sure the patched kernel fix the issue, set qa_ack+

    Finish debug IKEv2_ENODE_INITIATOR/ test

    Report Bug 1055406 - [TAHI][IKEv2]libreswan do not support higher minor version number

    Write auto_submit job script

    Tue:

    Report Bug 1055865 - [TAHI][IKEv2] libreswan do not ignore the content of version bit

    Finish debug IKEv2 test

    start debug MLDv2 test, try to find out why the fisrt test failed



==========================================
2014-01-15 week 2

This week:
---------------
1) Report bug
Bug 1049802 -        [TAHI][IKEv2] IKE_SA_INIT request Notify Payload incorrect
Bug 1050828 -        IPv6 TCP performance regression on all 10Gb NIC cards
Bug 1052811 -        [TAHI][IKEv2]IKEv2.EN.I.1.1.11.1: Non zero RESERVED fields in IKE_SA_INIT response
Bug 1052815 -        [TAHI][IKEv2]Test IKEv2.EN.I.1.1.11.4: Unrecognized Notify Message Type of Error
Bug 1052834 -        [TAHI][IKEv2]libreswan retransmits IKE_SA_INIT instread of CREATE_CHILD_SA after CHILD_SA Lifetime timeout
[Bug 1053048] [TAHI][IKEv2]libreswan doesn't sent CREATE_CHILD_SA after IKE_SA Lifetime timeout

2) Verify bug
Bug 861469 -        [RHEL7] SCTP protocol needs to switch from MD5 to SHA1 usage

3) review bug
[Bug 1024631] Labeled networking incorrectly labels SYN-ACK packet on AF_INET sockets
[Bug 545375] kernel crashes when flushing kernel conntrack table


Next week:
---------------
1. Run USGv6 core and addr_arch testing
2. Send reward to other guys



==========================================
2014-01-07 week 1

This week:
---------------
1) Report bug
Bug 1048702 -        libreswan doesn't  retrans IKE_SA_INIT requests after timeout
Bug 1049092 -        [TAHI][MLDv2][Listener] MLDv2 retransmit report should use TO_EX instead of IS_EX
Bug 1049095 -        USGv6 Tracker Bug
Bug 1049141 -        Mark vxlan_dev flags with VXLAN_F_IPV6 properly
Bug 1049143 -        make sure the src net is infiniband when create new link
Bug 1049144 -        make sure the src net is infiniband when create new link

2) Verify bug
Bug 889455 -        [TAHI][MLDv2][Listener] linux ipv6 stack does not support retransmissions for mldv2 listener report

3) Debug TAHI test suite for some issues. and run IKEv2, MLDv2, SNMP test on RHEL7

Next week:
---------------


