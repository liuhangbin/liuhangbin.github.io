---
layout: post
Title: Shell Tips
Date: 2013-03-08
tags: [Linux, Shell]
---

Shell 脚本中获取每个管道的退出状态  :
http://blog.chinaunix.net/uid-8326220-id-2939412.html

在Shell脚本中，可以通过$?来获取前面命令的退出状态。但是在多管道链接的命令中，$?返回的就仅仅是所有用管道连接起来的命令按先后顺序执行下来后最后的退出状态。对于每个管道的各种状态却无法知晓。这里bash给开发人员提供了一个强大的变量$PIPESTATUS，它是一个数组，包含了上一条命令中所有管道的退出状态。实例如下：
~$ true | false | true | true | echo "the exit codes of the pipe are :
${PIPESTATUS[*]}"
the exit codes of the pipe are : 0 1 0 0 0
这里可以看到，总共四个管道的退出状态全部显示出来了。这里的${PIPESTATUS[*]}
表示全部管道的退出状态。如果要单独引用，跟C语言一样，第一个元素下标为0，第n个管道的退出状态为${PIPESTATUS[n-1]}。但是当你试图获取超过管道数量范围之外的退出状态时，会没有任何显示：
~$ true | false | true | true | echo "the exit codes of the pipe are
: ${PIPESTATUS[*]}"
the exit codes of the pipe are : 0 1 0 0 0

~$ echo "the exit codes of second and sixth pipe are ${PIPESTATUS[1]}
and ${PIPESTATUS[5]}"
the exit codes of second and sixth pipe are 1 and
所知道的支持获取管道退出状态的除了Bash外，还有zsh，有兴趣的朋友可以google下


Shell 中获取随机数
---
1. date
```
$ date +%s
1287764773
#获得时间戳，当前到：1970-01-01 00:00:00 相隔的秒数
#如果用它做随机数，相同一秒的数据是一样的。在做循环处理，多线程里面基本不能满足要求了。
 
$ date +%N
738710457
#获得当前时间的纳秒数据，精确到亿分之一秒。
#这个相当精确了，就算在多cpu，大量循环里面，同一秒里面，也很难出现相同结果，不过不同时间里面还会有大量重复碰撞
 
$ date +%s%N
1287764807051101270
#这个可以说比较完美了，加入了时间戳，又加上了纳秒

Shell Scripts:
#!/bin/sh

#写个随机函数，调用方法random min max
#在min 与 max直接获得随机整数
#copyright chengmo QQ:8292669

#获得随机数返回值，shell函数里算出随机数后，更新该值
function random()
{
	min=$1;
	max=$2-$1;
	num=$(date +%s+%N);
	((retnum=num%max+min));
	#进行求余数运算即可
	echo $retnum;
	#这里通过echo 打印出来值，然后获得函数的，stdout就可以获得值
	#还有一种返回，定义全价变量，然后函数改下内容，外面读取
}

#得到1-10的seq数据项
for i in {1..10}; do
	out=$(random 2 10000)
	echo $i,"2-10000",$out
done
```

2. $RANDOM
```
$ echo $RANDOM
10918
$ echo $RANDOM
10001

可能有疑问了，如果超过5位的随机数怎么得到呢？
呵呵，加个固定10位整数，然后进行求余，跟例1 一样了。接下来的例子又是我们自立更生做了。
```

3. /dev/random,urandom

/dev/urandom这个设备数据与random里面一样。只是，它是非阻塞的随机数发生器，读取操作不会产生阻塞
它是通过二进制数据保存实时数据的，那么我们怎么样把它变成整型数据呢？

$ head -200 /dev/urandom | cksum
1795644120 55753
#由于urandom的数据是非常多，不能直接通过cat读取，这里取前200行，其实整个数据都是变化的，取多少也一样是唯一的。
#cksum 将读取文件内容，生成唯一的表示整型数据，只有文件内容不变，生成结果就不会变化,与php crc函数

$ head -200 /dev/urandom | cksum | cut -f1 -d" "
484750180
#cut 以” “分割，然后得到分割的第一个字段数据

4. uuid

linux的uuid码也是有内核提供的，在/proc/sys/kernel/random/uuid这个文件内。其实，random目录，里面还有很多其它文件，都与生成uuid有关系的
$ cat /proc/sys/kernel/random/uuid
dff68213-b700-4947-87b1-d9e640334196
$ cat /proc/sys/kernel/random/uuid
7b57209a-d285-4fd0-88b4-9d3162d2e1bc
#连续2次读取，得到的uuid是不同的
