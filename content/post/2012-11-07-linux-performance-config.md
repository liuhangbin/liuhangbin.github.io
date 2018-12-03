---
layout: post
Title: Linux performance config
Date: 2012-11-07
tags: [Linux, Performance, ]
---

参考链接 : [ 性能优化领域 : linu各层面可调性能参数介绍 ](http://lsq6063.iteye.com/blog/754923)

第一节：cpu 性能瓶颈
------

计算机中，cpu是最重要的一个子系统，负责所有计算任务；基于摩尔定律的发展，cpu是发
展最快的一个硬件，所以瓶颈很少出现在cpu上；我们线上环境的cpu都是多核的，并且基于
SMP(symmetric multiprocessing)结构的。通过观察线上机器cpu使用率会发现，使用率很低
很低，不到5%； 说明我们的资源浪费情况多么严重啊；(但为什么不能一台机器多部署几个
应用呢，后边我会解释）; 我们线上的cpu一个核支持超级线程，也就是一个核上可以并行运
行几个线程）

1. 通过调整进程优先级调整： nice 命令来调整进程优先级别；可调范围（-20到 19） 
    如： renice 5 pid

2. 通过调整cpu的亲和度来集中处理某一个中断类型:将系统发出的中断都绑定在一个cpu上，
这样其他cpu继续执行自己正在执行的线程，不被中断打扰，从而较少了线程上下文切换时间，
增强性能；

`注： cpu亲和度的概念： 在多核cpu中，linux操作系统抢占式调度系统，按照cpu时间片/
中断/等 不断调度进程给cpu去执行的；如果在一个时间片调度线程1在cpu1上运行，另外一
个时间片调度线程1在cpu2上去运行，这样会造成线程执行速度慢，性能降低。为什么呢？ 
我们知道SMP上多核都是共享L1 ,L2 CPU Cache的。并且各个核的内存空间都是不可共享的，
一个线程如果多次时间片上在不同的cpu上运行，会造成cache的不断失效和写入；性能会降
低； 而linux的进程调度有个亲和度算法可以将尽量将进程每次都调度到同一个cpu上处理；
linux调度时当然也有Loadbalance算法保证进程调度的均匀负载的；`

例如： echo 03 > /proc/irq/19/smp-affinity (将中断类型为19的中断绑定到第三个cpu上
处理）


第二节：内存性能瓶颈
------

首先，linux的内存管理是聪明和智能的；linux通过（virtual memory manage)来管理内存
的； 对于大多数应用，linux是不直接写到硬盘上去的，而是先写到 virtual memory 
manage 管理的文件系统缓存(也在内存中的)里 .方便应用的后续的读请求；因为和磁盘的
I/O操作是昂贵的；linux会根据一些算法策略适当的时候同步到硬盘的；这就是为什么我们
运行linux一段时间后，发现可用内存那么少的原因，多数被cache+buffer占用咧；

所以我们提高性能的办法就是减少写到磁盘的次数，提高每次写磁盘时的效率质量；

1. 通过调节缓存的脏数据同步到硬盘的策略：（脏数据表示没有被当前的线程使用的数据）

例如： `echo 10 > /proc/sys/vm/dirty_background_rato` (当脏数据占据物理内存10%时，
触发pdflush同步到硬盘）：小心调节，会大幅度的影响性能；

`echo 2000 > /proc/sys/vm/dirty_expire_centisecs (当脏数据在物理内存的逗留时间超
过2000ms时被同步到硬盘）；`

2. 通过调节swap参数，来优化linux虚拟内存管理：基于程序的局部性原理，linux通过虚拟
内存机制来实现并发运行进程，linux发现物理内存不够用时，会根据LRU算法将一部分内存
swap out到硬盘；当运行被换出的那个线程时，在swap in 到内存里；

例如： `echo 10 > /proc/sys/vm/swappiness` (值为0表示尽量都用物理内存，值为100表
示积极的使用swap分区；）这个参数很重要；小心调节； 一般为60;


第三节： 磁盘I/O可调性能参数
------

linux的子系统VFS(virtural file system)虚拟文件系统；从高层将各种文件系统，以及底
层磁盘特性隐藏，对程序员提供：read,write,delete等文件操作；这就是之所以我们可以在
linux上mount多种不同格式的文件系统的，而window确不行； 当然基于：虚拟文件系统，文
件系统，文件系统驱动程序，硬件特性方面，都能找到性能瓶颈；

1. 选择适合应用的文件系统;

2. 调整进程I/O请求的优先级，分三种级别：1代表 real time ; 2代表best-effort; 3代表idle ;

    如：`ionice -c1 -p1113` (给进程1113的I/O优先级设置为最高优先级）

3. 根据应用类型，适当调整page size 和block size;

4. 升级驱动程序；


第四节 ：网络可调性能参数
------

对于我们web应用来说，网络性能调整如此重要，linux的网络支持是无与伦比的；是作为网
络服务器的首先；对于web服务来说：除了应用的响应速度外，linux网络管理子系统，网卡，
带宽都可能成为性能瓶颈；

1. 查看网卡设置是否全双工传输的： echtool  eth0

2. 设置MTU（最大传输单元），在带宽G以上的时候，要考虑将MTU增大，提高传输性能；
    如： `ifconfig eth0 mtu 9000 up`

3. 增加网络数据缓存；传输数据时linux是将包先放入缓存，填满缓存后即发送出去；读操
作类似；

    `sysctl -w net.ipv4.tcp_rmem="4096 87380 8388608" ` :设置tcp读缓存：最小缓存，
    初始化时，最大缓存

4. 禁用window_scaling,并且直接设置window_size;(就像我们经常设置jvm的参数：
    xms = xmx一样

    `sysctl -w net.ipv4.tcp_window_scaling=0`

5. 设置TCP连接可重用性： 对于TIME_OUT状态的TCP连接可用于下一个TCP重用，这样减少了
三次握手和创建时间，非常提高性能，尤其对于web server；

    如： 开启可重用tcp功能： 
    `sysctl -w net.ipv4.tcp_tw_reuse=1  sysctl -w net.ipv4.tcp_tw_recyle=1 `

6. 禁用掉没必要的tcp/ip协议功能：比如icmp;broadcast包的接收；

7. linux对于keeplive的tcp连接有一个默认的过期时间；可以减小这个时间，让没用的连
接释放掉，毕竟tcp连接数是有限的嘛；

    如： `sysctl -w net.ipv4.tcp_keepalive_time=1800` （设置过期时间，1800s)

8. 设置最大tcp正在连接状态（还没ESTABLISHED)队列长度；避免由于太多的tcp连接过来，
导致服务器挂掉；比如DoS攻击

    如：`sysctl -w net.ipv4.tcp_max_syn_backlog=4096`

9. 绑定tcp类型的中断到一个cpu上；（让cpu去亲和这个类型中断，避免频繁的中断，影响
线程调度性能）


总结： 我们在性能优化一个应用时，首要的是设定优化要达到的目标，然后寻找瓶颈，调整
参数，达到优化目的；但是寻找瓶颈时可能是最累的，要从大范围，通过很多用例，很多测
试报告，不断的缩小范围，最终确定瓶颈点；以上这些参数只是个认识，系统性能优化中可
能用到，但并不是放之四海而皆准的； 有的参数要边测试，边调整的.