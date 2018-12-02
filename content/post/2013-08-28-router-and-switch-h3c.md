---
layout: post
Title: How to config router and switch on H3C devices
Date: 2013-08-28
tags: [Router, Switch, H3C]
---


How to config MLD on H3C
---
1. enable MLD
```
system-view
multicast ipv6 routing-enable
interface interface-type interface-number
mld enable
```

2. Gloabl MLD version config
```
system-view
mld
version version-number
```

3. Interface MLD version config
```
system-view
interface interface-type interface-number
mld version version-number
```

4. Static gourp in
```
system-view
interface interface-type interface-number
mld static-group ipv6-group-address [ source ipv6-source-address ]
```

5. Group limit
```
system-view
interface interface-type interface-number
mld group-limit limit
```

6. Show MLD info
```
display mld group
display mld interface
```



1. backup config files
---
[H3C]display current-configuration
#
 version 5.20, Release 1509P04, Standard
#
 sysname H3C
#
 ftp server enable
#
 domain default enable system
#
 ipv6 icmpv6 multicast-echo-reply enable
#
 ipv6
#
 multicast ipv6 routing-enable
#
vlan 1
#
domain system
 access-limit disable
 state active
 idle-cut disable
 self-service-url disable
#
interface Aux0
 async mode flow
 link-protocol ppp
#
interface Ethernet0/0
 port link-mode route
 ipv6 address 3FFE:501:FFFF:100::2/64
 undo ipv6 nd ra halt
 ip address 192.168.0.2 255.255.255.0
 mld enable
#
interface Ethernet0/1
 port link-mode route
 ipv6 address 3FFE:501:FFFF:104::1/64
 ipv6 nd ra interval 120 90
 ipv6 nd ra router-lifetime 300
 ipv6 nd ra prefix 3FFE:501:FFFF:104::/64 600 600
 undo ipv6 nd ra halt
 ip address 192.168.4.1 255.255.255.0
#
interface NULL0
#
 ip route-static 192.168.3.0 255.255.255.0 192.168.0.1
 ip route-static 192.168.3.0 255.255.255.0 192.168.4.13 preference 210
#
 ipv6 route-static 3FFE:501:FFFF:103:: 64 3FFE:501:FFFF:104::13
#
 ssh server enable
#
user-interface con 0
user-interface aux 0
user-interface vty 0 4
#
return



2. Normal config
---

进入系统视图
system-view

配置设备名
sysname RouterA


查看FLASH目录下内容
dir

指定下次启动配置文件
startup saved-configuration main.cfg

保存配置
save

重启
reboot

#置CONSOLE用户登陆的口令认证
<H3C>system-view

[H3C]user-interface aux 0

[H3C-ui-aux0]authentication-mode password

[H3C-ui-aux0]set authenticaton password simple 123456
[H3C-ui-aux0]user privilege level 3


用户的命令控制级别设置

[H3C-ui-aux0]user privilege level 0

[H3C]super password level 1 simple 123456

[H3C]super password level 2 simple 123456

[H3C]super password level 3 simple 123456



# 设置super(明文)密码  当低权限向高权限切换时使用！
[RouterA]super password simple quidway

# 设置super(密文)密码
[RouterA]super password cipher quidway

# 启用telnet 管理功能
[RouterA]user-interface vty 0 4
[RouterA-ui-vty0-4]authentication-mode password
[RouterA-ui-vty0-4]set authentication password simple quidway
[RouterA-ui-vty0-4]user privilege level 3
[RouterA-ui-vty0-4]quit
[RouterA]telnet server enable

# 配置端口IP 地址
[RouterA]int e 0
[RouterA-Ethernet0]ip add 192.168.1.1 24

# 启动端口
[RouterA-Ethernet0]undo shutdown
[RouterA-Ethernet0]quit

# 保存,在任何视图下
<RouterA>save

# 查看当前设备配置信息
[RouterA]display current-configuration

# 查看Flash 中的配置(相当于CISCO start-config)
[RouterA]display saved-configuration

# 删除FLASH 中的配置信息(重置设备配置)
[RouterA]reset saved-configuration

# 重新启动设备
<RouterA>reboot

# 显示系统软件版本
[RouterA]display version

# 访问TFTP 服务器
<H3C>tftp 服务器IP {put|get} 源文件名目的文件名
<H3C>tftp 192.168.1.2 put startup.cfg startup.cfg //上传
<H3C>tftp 192.168.1.2 get test.txt test.txt //下载

# 交换机IP 地址
[H3C]int vlan 1
[H3C-Vlan-interface1]ip add 192.168.1.1 24
[H3C-Vlan-interface1]undo ip add 192.168.1.1 24

# 创建VLAN
[H3C]vlan 10
[H3C]undo vlan 10

# 向VLAN 添加一个、多个端口
[H3C-vlan10]port e 1/0/1
[H3C-vlan10]port e 1/0/5 to e 1/0/7
[H3C-vlan10]port e 1/0/8 to e 1/0/9 e 1/0/11 to e 1/0/12

# 显示VLAN 信息
[H3C]dis vlan

# 设置以太网端口的链路类型为TRUNK
[H3C-Ethernet1/0/1]port link-type trunk
[H3C-Ethernet1/0/1]port trunk permit vlan all
[H3C-Ethernet1/0/1]port trunk pvid vlan 1

# 设置以太网端口的链路类型为ACCESS
[H3C-Ethernet1/0/1]port link-type access
[H3C-Ethernet1/0/1]port access vlan 10
[H3C-Ethernet1/0/1]undo port access vlan

# 配置Loopback 环回接口
[H3C]int loopback 0
[H3C-Lookback0]ip add 10.1.1.1 24

# 静态路由
[H3C]ip route 192.168.3.0 255.255.255.0 s 1/0
[H3C]undo ip route 192.168.3.0 255.255.255.0 s 1/0

# 默认路由
[H3C]ip route 0.0.0.0


显示当前配置
dis cu

dis命令集(汉化）

acl                     ACL状态和配置信息
arp                     显示ARP信息
aspf                    ASPF状态和配置信息
atm                     ATM状态和配置信息
bgp                     BGP 协议信息
boot-loader             显示启动文件
bootp                   自举协议
bridge                  桥的信息
brief                   接口状态和配置简要信息
channel                 信息通道状态和配置信息
clipboard               剪贴板的状态和配置信息
clock                   时钟状态和配置信息
configure-user          具有配置权限的用户
connection              显示连接信息
connection-limit        连接统计模块的状态和配置信息
controller              指定controller接口
cpu-usage               CPU占用率统计信息
current-configuration   系统当前配置
dar                     DAR状态和配置信息
debugging               显示当前调试开关设置情况
device                  显示设备信息
dhcp                    DHCP命令
diagnostic-information  显示系统诊断信息
dialer                  DCC状态和配置信息
dns                     DNS 状态和配置信息
domain                  显示域配置方案
dot1x                   802.1X配置信息
dsl                     DSL状态和配置信息
environment             显示环境信息
fan                     显示风扇状态
fe1                     接口FE1状态和配置信息
fib                     FIB状态和配置信息
firewall                防火墙的配置信息
firewall-statistics     防火墙的状态和配置信息
fr                      帧中继状态和配置信息
ft1                     接口FT1状态和配置信息
ftp                     FTP客户端状态和配置信息
ftp-server              FTP服务器状态和配置信息
ftp-user                FTP用户状态和配置信息
history-command         历史命令配置信息
hotkey                  快捷键的状态和配置信息
hwtacacs                HWTACACS服务器的相关信息
icmp                    ICMP信息
igmp                    状态和配置信息
info-center             显示信息中心配置及内容
interface               接口状态和配置信息
ip                      IP 协议
ipv6                    IPv6状态和配置信息
isdn                    ISDN 状态和配置信息
l2tp                    L2TP状态和配置信息
local-proxy-arp         显示本地ARP代理信息
local-server            本地服务器信息
local-user              显示本地用户信息
logbuffer               日志缓冲区状态和配置信息
logfile                 显示日志文件信息
loopback-detection      检测端口是否存在自环
mac-address             MAC地址信息
memory                  显示内存信息
mfr                     显示MFR状态和配置信息
mirroring-group         设置镜像组
mld                     MLD状态和配置信息
msdp                    MSDP状态和配置信息
multicast               多播配置
nat                     地址转换状态和配置信息
nqa                     显示NQA信息
ntp-service             NTP网络时间服务
oap                     开放应用平台的操作
ospf                    OSPF路由协议
pim                     PIM状态和配置信息
pki                     PKI信息
policy-based-route      策略路由的状态和配置信息
port                    显示端口信息
port-isolate            隔离组信息
portal                  Portal 协议
power                   显示电源状态
ppp                     PPP状态和配置信息
pppoe-client            PPPoE Client状态和配置信息
pppoe-server            PPPoE服务器端状态和配置信息
proxy-arp               显示ARP代理信息
qos                     服务质量状态和配置信息
radius                  显示RADIUS配置信息
reboot-type             显示启动类型
rip                     RIP状态和配置信息
rmon                    RMON信息
route-policy            显示路由策略配置
router                  配置路由器信息
routing                 显示路由信息
rps                     显示冗余电源状态
rsa                     显示RSA模块的状态和配置信息
saved-configuration     显示保存的系统配置信息
schedule                设定系统任务
sftp                    显示设置的源
snmp-agent              显示SNMP状态和配置信息
ssh                     SSH 状态和配置信息
standby                 备份接口状态和配置信息
startup                 显示系统启动配置
stop-accounting-buffer  查询备份在本地的无响应的停止计费报文
stp                     生成树协议
tcp                     TCP状态和配置信息
telnet                  TELNET状态和配置信息
tftp                    TFTP客户端状态和配置信息
this                    当前视图的运行配置
time-range              时间段状态和配置信息
traffic                 数据流的状态和配置信息
transceiver
trapbuffer              告警缓冲区状态和配置信息
udp                     UDP状态和配置信息
udp-helper              UDP HELPER模块
user-interface          用户接口的状态和配置信息
userlog                 用户日志特性
users                   用户接口状态及配置信息
version                 系统的硬件和软件版本信息
virtual-access          Virtual-access 接口状态和配置信息
vlan                    VLAN配置信息
voice                   Voice VLAN

==========================================================================================
<Quidway>reset save          ；清除所有配置
[Quidway]super password                                     ；修改特权用户密码
[Quidway]sysname                                     ；交换机命名
[Quidway]interface ethernet 0/1                            ；进入接口视图
[Quidway]interface vlan x                            ；进入接口视图
[Quidway-Vlan-interfacex]ip address 10.65.1.1 255.255.0.0     ；配置VLAN的IP地址
[Quidway]ip route-static 0.0.0.0 0.0.0.0 10.65.1.2          ；静态路由＝网关


[Quidway]user-interface vty 0 4                            ；进入虚拟终端
[S3026-ui-vty0-4]authentication-mode password                 ；设置口令模式
[S3026-ui-vty0-4]set authentication password simple 222   ；设置口令
[S3026-ui-vty0-4]user privilege level 3                       ；用户级别


[Quidway-Ethernet0/1]duplex {half|full|auto}                ；配置端口工作状态
[Quidway-Ethernet0/1]speed {10|100|auto}                ；配置端口工作速率
[Quidway-Ethernet0/1]flow-control                      ；配置端口流控
[Quidway-Ethernet0/1]mdi {across|auto|normal}                 ；配置端口平接扭接
[Quidway-Ethernet0/1]port link-type {trunk|access|hybrid}     ；设置端口工作模式
[Quidway-Ethernet0/1]undo shutdown                       ；激活端口
[Quidway-Ethernet0/2]quit                            ；退出系统视图


[Quidway]vlan 3                                   　　　　　　；创建VLAN
[Quidway-vlan3]port ethernet 0/1 to ethernet 0/4 　　　　　；在VLAN中增加端口
[Quidway-Ethernet0/2]port access vlan 3          　　　　　；当前端口加入到VLAN
[Quidway-Ethernet0/2]port trunk permit vlan {ID|All}    　　；设trunk允许的VLAN
[Quidway-Ethernet0/2]port trunk pvid vlan 3     　　　　　；设置trunk端口的PVID


[Quidway]monitor-port <interface_type interface_num>        　；指定镜像端口
[Quidway]port mirror <interface_type interface_num>    　　；指定被镜像端口
[Quidway]port mirror int_list observing-port int_type int_num ；指定镜像和被镜像


[Quidway]description string                      　　　　　；指定VLAN描述字符
[Quidway]description                         　　　　　　 　；删除VLAN描述字符
[Quidway]display vlan [vlan_id]                            ；查看VLAN设置


[Quidway]stp {enable|disable}                               ；设置生成树,默认关闭
[Quidway]stp priority 4096                          　　　　　；设置交换机的优先级
[Quidway]stp root {primary|secondary}             　　　　　；设置为根或根的备份
[Quidway-Ethernet0/1]stp cost 200                　　　　　；设置交换机端口的花费


[SwitchA-vlanx]isolate-user-vlan enable          　　　　　；设置主vlan
[SwitchA]Isolate-user-vlan <x> secondary <list>     　　　　　；设置主vlan包括的子vlan
[Quidway-Ethernet0/2]port hybrid pvid vlan <id>     　　　　　；设置vlan的pvid
[Quidway-Ethernet0/2]port hybrid pvid             　　　　　；删除vlan的pvid
[Quidway-Ethernet0/2]port hybrid vlan vlan_id_list untagged 　；设置无标识的vlan
如果包的vlan id与PVId一致，则去掉vlan信息. 默认PVID=1。
所以设置PVID为所属vlan id, 设置可以互通的vlan为untagged.
－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－
路由器命令

[Quidway]display version                          　　　　　　；显示版本信息
[Quidway]display current-configuration          　　　　　　；显示当前配置
[Quidway]display interfaces                       　　　　　　；显示接口信息
[Quidway]display ip route                      　　　　　　；显示路由信息


[Quidway]sysname aabbcc                         　　　　　　；更改主机名
[Quidway]super passwrod 123456                    　　　　　　；设置口令
[Quidway]interface serial0                      　　　　　　；进入接口
[Quidway-serial0]ip address <ip> <mask|mask_len>   　　　　　　；配置端口IP地址
[Quidway-serial0]undo shutdown                    　　　　　　；激活端口
[Quidway]link-protocol hdlc                       　　　　　　；绑定hdlc协议
[Quidway]user-interface vty 0 4
[Quidway-ui-vty0-4]authentication-mode password
[Quidway-ui-vty0-4]set authentication-mode password simple 222
[Quidway-ui-vty0-4]user privilege level 3
[Quidway-ui-vty0-4]quit


[Quidway]debugging hdlc all serial0             　　　　　　；显示所有信息
[Quidway]debugging hdlc event serial0          　　　　　　；调试事件信息
[Quidway]debugging hdlc packet serial0          　　　　　　；显示包的信息


静态路由：
[Quidway]ip route-static <ip><mask>{interface number|nexthop}[value][reject|blackhole]
例如：
[Quidway]ip route-static 129.1.0.0 16 10.0.0.2
[Quidway]ip route-static 129.1.0.0 255.255.0.0 10.0.0.2
[Quidway]ip route-static 129.1.0.0 16 Serial 2
[Quidway]ip route-static 0.0.0.0 0.0.0.0   10.0.0.2


动态路由：
[Quidway]rip                                                  ；设置动态路由
[Quidway]rip work                                           ；设置工作允许
[Quidway]rip input                                            ；设置入口允许
[Quidway]rip output                                        ；设置出口允许
[Quidway-rip]network 1.0.0.0                       　　　　　 ；设置交换路由网络
[Quidway-rip]network all                                      ；设置与所有网络交换
[Quidway-rip]peer ip-address                               ；
[Quidway-rip]summary                                        ；路由聚合
[Quidway]rip version 1                                     ；设置工作在版本1
[Quidway]rip version 2 multicast                            ；设置工作在版本2，多播方式
[Quidway-Ethernet0]rip split-horizon                          ；水平分隔


[Quidway]router id A.B.C.D                                  ；配置路由器的ID
[Quidway]ospf enable                                        ；启动OSPF协议
[Quidway-ospf]import-route direct                             ；引入直联路由
[Quidway-Serial0]ospf enable area <area_id>                ；配置OSPF区域


标准访问列表命令格式如下：
acl <acl-number> [match-order config|auto]                    ；默认前者顺序匹配。
rule [normal|special]{permit|deny} [source source-addr source-wildcard|any]
例：
[Quidway]acl 10
[Quidway-acl-10]rule normal permit source 10.0.0.0 0.0.0.255
[Quidway-acl-10]rule normal deny source any


扩展访问控制列表配置命令
配置TCP/UDP协议的扩展访问列表：
rule {normal|special}{permit|deny}{tcp|udp}source {<ip wild>|any}destination <ip wild>|any}
[operate]


配置ICMP协议的扩展访问列表：
rule {normal|special}{permit|deny}icmp source {<ip wild>|any]destination {<ip wild>|any]
[icmp-code] [logging]


扩展访问控制列表操作符的含义
equal portnumber                                     ；等于
greater-than portnumber                                     ；大于
less-than portnumber                                     ；小于
not-equal portnumber                                     ；不等
range portnumber1 portnumber2                               ；区间


扩展访问控制列表举例
[Quidway]acl 101
[Quidway-acl-101]rule deny souce any destination any
[Quidway-acl-101]rule permit icmp source any destination any icmp-type echo
[Quidway-acl-101]rule permit icmp source any destination any icmp-type echo-reply


[Quidway]acl 102
[Quidway-acl-102]rule permit ip source 10.0.0.1 0.0.0.0 destination 202.0.0.1 0.0.0.0
[Quidway-acl-102]rule deny ip source any destination any


[Quidway]acl 103
[Quidway-acl-103]rule permit tcp source any destination 10.0.0.1 0.0.0.0 destination-port equal ftp
[Quidway-acl-103]rule permit tcp source any destination 10.0.0.2 0.0.0.0 destination-port equal www



[Quidway]firewall enable
[Quidway]firewall default permit|deny
[Quidway]int e0
[Quidway-Ethernet0]firewall packet-filter 101 inbound|outbound



地址转换配置举例
[Quidway]firewall enable
[Quidway]firewall default permit
[Quidway]acl 101
[Quidway-acl-101]rule deny ip source any destination any
[Quidway-acl-101]rule permit ip source 129.38.1.4 0 destination any
[Quidway-acl-101]rule permit ip source 129.38.1.1 0 destination any
[Quidway-acl-101]rule permit ip source 129.38.1.2 0 destination any
[Quidway-acl-101]rule permit ip source 129.38.1.3 0 destination any
[Quidway]acl 102
[Quidway-acl-102]rule permit tcp source 202.39.2.3 0 destination 202.38.160.1 0
[Quidway-acl-102]rule permit tcp source any destination 202.38.160.1 0 destination-port great-than
1024


[Quidway-Ethernet0]firewall packet-filter 101 inbound
[Quidway-Serial0]firewall packet-filter 102 inbound


[Quidway]nat address-group 202.38.160.101 202.38.160.103 pool1
[Quidway]acl 1
[Quidway-acl-1]rule permit source 10.110.10.0 0.0.0.255
[Quidway-acl-1]rule deny source any
[Quidway-acl-1]int serial 0
[Quidway-Serial0]nat outbound 1 address-group pool1


[Quidway-Serial0]nat server global 202.38.160.101 inside 10.110.10.1 ftp tcp
[Quidway-Serial0]nat server global 202.38.160.102 inside 10.110.10.2 www tcp
[Quidway-Serial0]nat server global 202.38.160.102 8080 inside 10.110.10.3 www tcp
[Quidway-Serial0]nat server global 202.38.160.103 inside 10.110.10.4 smtp udp



PPP验证：
主验方：pap|chap
[Quidway]local-user u2 password {simple|cipher} aaa
[Quidway]interface serial 0
[Quidway-serial0]ppp authentication-mode {pap|chap}
[Quidway-serial0]ppp chap user u1        //pap时，不用此句


pap被验方：
[Quidway]interface serial 0
[Quidway-serial0]ppp pap local-user u2 password {simple|cipher} aaa


chap被验方：
[Quidway]interface serial 0
[Quidway-serial0]ppp chap user u1
[Quidway-serial0]local-user u2 password {simple|cipher} aaa
