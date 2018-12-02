---
layout: post
Title: How to show network brandwidth in linux
Date: 2013-07-12
Tag: Linux, Network
---

Three tools to show network brandwith under linux cmd
===

1. Iftop : Display brandwidth by host(address)
---
$ iftop --help
iftop: unknown option --
iftop: display bandwidth usage on an interface by host

Synopsis: iftop -h | [-npblNBP] [-i interface] [-f filter code]
                               [-F net/mask] [-G net6/mask6]

   -h                  display this message
   -n                  dont do hostname lookups
   -N                  dont convert port numbers to services
   -p                  run in promiscuous mode (show traffic between other
                       hosts on the same network segment)
   -b                  dont display a bar graph of traffic
   -B                  Display bandwidth in bytes
   -i interface        listen on named interface
   -f filter code      use filter code to select packets to count
                       (default: none, but only IP packets are counted)
   -F net/mask         show traffic flows in/out of IPv4 network
   -G net6/mask6       show traffic flows in/out of IPv6 network
   -l                  display and count link-local IPv6 traffic (default: off)
   -P                  show ports as well as hosts
   -m limit            sets the upper limit for the bandwidth scale
   -c config file      specifies an alternative configuration file

iftop, version 1.0pre2
copyright (c) 2002 Paul Warren <pdw@ex-parrot.com> and contributors

Ref: http://www.thegeekstuff.com/2008/12/iftop-guide-display-network-interface-bandwidth-usage-on-linux/


2. bwm-ng: display brandwidth by interface
---
$ bwm-ng --help
Bandwidth Monitor NG (bwm-ng) v0.6
Copyright (C) 2004-2007 Volker Gropp <bwmng@gropp.org>
USAGE: bwm-ng [OPTION] ... [CONFIGFILE]
displays current ethernet interfaces stats

Options:
  -t, --timeout <msec>    displays stats every <msec> (1msec = 1/1000sec)
                          default: 500
  -d, --dynamic [value]   show values dynamicly (Byte KB or MB)
  -a, --allif [mode]      where mode is one of:
                          0=show only up (and selected) interfaces
                          1=show all up interfaces (default)
                          2=show all and down interfaces
  -I, --interfaces <list> show only interfaces in <list> (comma seperated), or
                          if list is prefaced with % show all but interfaces
                          in list
  -S, --sumhidden [value] count hidden interfaces for total
  -A, --avglength <sec>   sets the span of average stats (Default 30s)
  -D, --daemon [value]    fork into background and daemonize
  -h, --help              displays this help
  -V, --version           print version info

Input:
  -i, --input <method>    input method, one of: proc netstat libstatgrab disk libstatdisk
  -f, --procfile <file>   filename to read raw data from. (/proc/net/dev)
      --diskstatsfile <file> filename to read diskstats (Linux 2.6+) from. (/proc/diskstats)
      --partitionsfile <file> filename to read diskstats (Linux 2.4) from. (/proc/partitions)

Output:
  -o, --output <method>   output method, one of:
                          plain, curses, curses2, csv, html
  -u, --unit <value>      unit to show. one of bytes, bits, packets, errors
  -T, --type <value>      type of stats. one of rate, max, sum, avg
  -C, --csvchar <char>    delimiter for csv
  -F, --outfile <file>    output file for csv and html (default stdout)
  -R, --htmlrefresh <num> meta refresh for html output
  -H, --htmlheader        show <html> and <meta> frame for html output
  -c, --count <num>       number of query/output for plain & csv
  -N, --ansiout           disable ansi codes for plain output
                          (ie 1 for one single output)

Ref: http://archive09.linux.com/feature/52443


3. nethogs: display network brandwidth by process

$ nethogs --help
nethogs: invalid option -- '-'
usage: nethogs [-V] [-b] [-d seconds] [-t] [-p] [device [device [device ...]]]
                -V : prints version.
                -d : delay for update refresh rate in seconds. default is 1.
                -t : tracemode.
                -b : bughunt mode - implies tracemode.
                -p : sniff in promiscious mode (not recommended).
                device : device(s) to monitor. default is eth0

When nethogs is running, press:
 q: quit
 m: switch between total and kb/s mode

Ref: http://nethogs.sourceforge.net/

4. Other tools

http://www.tecmint.com/command-line-tools-to-monitor-linux-performance/

nmon: a tool to analise linux performance
http://www.ibm.com/developerworks/aix/library/au-analyze_aix/

4. sar: Collect, report, or save system activity information

$ sar -n DEV 1 3
Linux 3.8.8-202.fc18.x86_64 (localhost.localdomain)     07/26/2013      _x86_64_        (4 CPU)

04:20:01 PM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
04:20:02 PM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00
04:20:02 PM       em1      5.00      2.00      0.63      0.16      0.00      0.00      4.00

04:20:02 PM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
04:20:03 PM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00
04:20:03 PM       em1      2.00      2.00      0.12      0.12      0.00      0.00      0.00

04:20:03 PM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
04:20:04 PM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00
04:20:04 PM       em1      2.00      1.00      0.30      0.06      0.00      0.00      0.00

Average:        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
Average:           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00
Average:          em1      3.00      1.67      0.35      0.12      0.00      0.00      1.33

5. Use Scripts:

```shell
#!/bin/bash
IF=$1
if [ -z "$IF" ]; then
        IF=`ls -1 /sys/class/net/ | head -1`
fi
RXPREV=-1
TXPREV=-1
echo "Listening $IF..."
while [ 1 == 1 ] ; do
        RX=`cat /sys/class/net/${IF}/statistics/rx_bytes`
        TX=`cat /sys/class/net/${IF}/statistics/tx_bytes`
        if [ $RXPREV -ne -1 ] ; then
                let BWRX=$RX-$RXPREV
                let BWTX=$TX-$TXPREV
                echo "Received: $BWRX B/s    Sent: $BWTX B/s"
        fi
        RXPREV=$RX
        TXPREV=$TX
        sleep 1
done
```


```Perl
#!/usr/bin/perl

use strict;
use warnings;

use POSIX qw(strftime);
use Time::HiRes qw(gettimeofday usleep);

my $dev = @ARGV ? shift : 'eth0';
my $dir = "/sys/class/net/$dev/statistics";
my %stats = do {
    opendir +(my $dh), $dir;
    local @_ = readdir $dh;
    closedir $dh;
    map +($_, []), grep !/^\.\.?$/, @_;
};

if (-t STDOUT) {
    while (1) {
        print "\033[H\033[J", run();
        my ($time, $us) = gettimeofday();
        my ($sec, $min, $hour) = localtime $time;
        {
            local $| = 1;
            printf '%-31.31s: %02d:%02d:%02d.%06d%8s%8s%8s%8s',
            $dev, $hour, $min, $sec, $us, qw(1s 5s 15s 60s)
        }
        usleep($us ? 1000000 - $us : 1000000);
    }
}
else {print run()}

sub run {
    map {
        chomp (my ($stat) = slurp("$dir/$_"));
        my $line = sprintf '%-31.31s:%16.16s', $_, $stat;
        $line .= sprintf '%8.8s', int (($stat - $stats{$_}->[0]) / 1)
            if @{$stats{$_}} > 0;
        $line .= sprintf '%8.8s', int (($stat - $stats{$_}->[4]) / 5)
            if @{$stats{$_}} > 4;
        $line .= sprintf '%8.8s', int (($stat - $stats{$_}->[14]) / 15)
            if @{$stats{$_}} > 14;
        $line .= sprintf '%8.8s', int (($stat - $stats{$_}->[59]) / 60)
            if @{$stats{$_}} > 59;
        unshift @{$stats{$_}}, $stat;
        pop @{$stats{$_}} if @{$stats{$_}} > 60;
        "$line\n";
    } sort keys %stats;
}

sub slurp {
    local @ARGV = @_;
    local @_ = <>;
    @_;
}
```
