---
layout: post
Title: Linux Basic Service
Date: 2012-12-05
tags: [Linux, Service]
---

Echo (port 7)
---
echo 服务基于TCP 端口号 7

可以通过netstat  -ant 查看没有开启, red hat 上 默认没有开启

守护进程是xinetd

启动方法：
vim /etc/xinetd.d/echo-stream
```
service echo
{
# This is for quick on or off of the service
        disable = yes
```
将这里 disable = yes 修改成 disable = no, 然后重启 xinetd 服务`service xinetd restart`

netstat -ant 查看发现已经启动

Perl 实现
-----
perl 网络编程中的 tcp_echo_cli1.pl 就可以正常运行了
```
#!/usr/bin/perl
use strict;
use Socket;
use IO::Handle;
my ($byte_out,$byte_in) = (0,0);
my $host = shift || 'localhost';
my $port = shift || getservbyname('echo','tcp');
my $protocol = getprotobyname('tcp');
$host = inet_aton($host) or die "$host:unknown host";

socket(SOCK,AF_INET,SOCK_STREAM,$protocol)
        or die "socket() failed:$!";
my $dest_addr = sockaddr_in($port,$host);
connect(SOCK,$dest_addr) or die "connect() failed:$!";

SOCK->autoflush(1);

while(my $msg_out = <>){
        print SOCK $msg_out;
        my $msg_in = <SOCK>;
        print $msg_in;

        $byte_out += length($msg_out);
        $byte_in += length($msg_in);
}

close SOCK;
print "byte_sent = $byte_out,byte_received = $byte_in\n";

OUTPUT:
Hello guys!
Hello guys!
byte_sent = 12,byte_received = 12
```
