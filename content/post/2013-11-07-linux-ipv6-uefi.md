---
layout: post
Title: How to setup IPv6 UEFI PXE on RHEL6
Date: 2013-11-07
tags: [Linux, UEFI, IPv6, PXE]
---


=== Configuring PXE Boot for BIOS

1. `yum install tftp-server dhcpd`
2. edit /etc/xinet.d/tftp and change *disabled* from *yes* to *no*
3. change `flags` to `IPv6` if you want to set pxe for IPv6
4. `cp pxelinux.0 /var/lib/tftpboot/pxelinux/`
5. edit /etc/dhcp/dhcpd.conf for IPv4
```
option space pxelinux;
option pxelinux.magic code 208 = string;
option pxelinux.configfile code 209 = text;
option pxelinux.pathprefix code 210 = text;
option pxelinux.reboottime code 211 = unsigned integer 32;

subnet 192.168.0.0 netmask 255.255.255.0 {
        option routers 192.168.0.254;
        range 192.168.0.2 192.168.0.253;

        class "pxeclients" {
                match if substring (option vendor-class-identifier, 0, 9) = "PXEClient";
                next-server 192.168.0.1;

                filename "pxelinux/pxelinux.0";
        }
}
```
6. edit /etc/dhcp/dhcpd6.conf for IPv6
```
# cat /etc/dhcp/dhcpd6.conf
default-lease-time 2592000;
preferred-lifetime 604800;
option dhcp-renewal-time 3600;
option dhcp-rebinding-time 7200;
option dhcp6.name-servers 2012::1;
option dhcp6.domain-search "test.example.com","example.com";
option dhcp6.info-refresh-time 21600;
option dhcp6.bootfile-url code 59 = string;

subnet6 2012::/64 {
        range6 2012::10 2012::100;
        range6 2012:: temporary;
        prefix6 2012:: 2012:: /64;
        option dhcp6.bootfile-url "tftp://[2012::1]/pxelinux/pxelinux.0";
}

```
5. `mkdir /var/lib/tftpboot/pxelinux/pxelinux.cfg`
6. edit /var/lib/tftpboot/pxelinux/pxelinux.cfg/default
```
UI vesamenu.c32
LABEL rhel6_desktop
        MENU LABEL ^1) Install Desktop
        KERNEL rhel6/vmlinuz
        APPEND initrd=rhel6/initrd.img
```
8. `cp /boot/grub/splash.xpm.gz /var/lib/tftpboot/pxelinux/splash.xpm.gz`
9. `cp /path/to/x86_64/os/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/pxelinux/rhel6/`

After these steps, the directory would like:

/var/lib/tftpboot/pxelinux/
 |
 | -- pxelinux.0
 | -- splash.xpm.gz
 | -- pxelinux.cfg/
 |    |
 |    |-- default
 |
 | -- rhel6/
        |
        | -- initrd.img
        | -- vmlinuz


=== Configuring PXE Boot for EFI

==== Config bios
1. Enable EFI and disable CSM

=== Config Server
1. `yum install tftp-server`
2. edit /etc/xinet.d/tftp and change *disabled* from *yes* to *no*
3. change `flags` to `IPv6` if you want to set pxe for IPv6
4. Create a directory path within tftpboot for the EFI boot images, and then copy them from your boot directory
5. tftpboot topo
```
# pwd
/var/lib/tftpboot
# tree
.
`-- pxelinux
    `-- bootx64.efi
        |-- BOOTX64.efi	(This must be executable file, or it will fail with access denied)
        |-- efidefault
        |-- grubx64.efi
        |-- rhel6
        |   |-- initrd.img
        |   `-- vmlinuz
        `-- rhel7
            |-- initrd.img
            `-- vmlinuz

# cat efidefault
default=0
timeout=5
hiddenmenu
title RHEL6
        root (nd)
        kernel /rhel6/vmlinuz method=http://[2012::1]/rhel6/ lang=en_US keymap=us
        initrd /rhel6/initrd.img
title RHEL7
        root (nd)
        kernel /rhel7/vmlinuz
        initrd /rhel7/initrd.img
```

5. edit /etc/dhcp/dhcpd.conf for IPv4
```
option space pxelinux;
option pxelinux.magic code 208 = string;
option pxelinux.configfile code 209 = text;
option pxelinux.pathprefix code 210 = text;
option pxelinux.reboottime code 211 = unsigned integer 32;

subnet 192.168.0.0 netmask 255.255.255.0 {
        option routers 192.168.0.254;
        range 192.168.0.2 192.168.0.253;

        class "pxeclients" {
                match if substring (option vendor-class-identifier, 0, 9) = "PXEClient";
                next-server 192.168.0.1;

                filename "pxelinux/bootx64.efi/BOOTX64.efi";
        }
}
```
6. edit /etc/dhcp/dhcpd6.conf for IPv6
```
# cat /etc/dhcp/dhcpd6.conf
default-lease-time 2592000;
preferred-lifetime 604800;
option dhcp-renewal-time 3600;
option dhcp-rebinding-time 7200;
option dhcp6.name-servers 2012::1;
option dhcp6.domain-search "test.example.com","example.com";
option dhcp6.info-refresh-time 21600;
dhcpv6-lease-file-name "/usr/local/var/db/dhcpd6.leases";
option dhcp6.bootfile-url code 59 = string;

subnet6 2012::/64 {
        range6 2012::10 2012::100;
        range6 2012:: temporary;
        prefix6 2012:: 2012:: /64;
        option dhcp6.bootfile-url "tftp://[2012::1]/pxelinux/bootx64.efi/BOOTX64.efi";
}

```
7. mount RHEL ISO in /var/www/html and start httpd
8. start radvd for IPv6 autoconfig, because when init install program, it will
start NetworkManager and auto config IPv6, If you choose DHCP type IPv6, then
no need for radvd.
```
# cat /etc/radvd.conf
interface eth0
{
        AdvSendAdvert on;
        MinRtrAdvInterval 30;
        MaxRtrAdvInterval 100;
        prefix 2012::/64
        {
                AdvOnLink on;
                AdvAutonomous on;
                AdvRouterAddr off;
        };

};
# echo 1 > /proc/sys/net/ipv6/conf/all/forwarding
# echo 1 > /proc/sys/net/ipv6/conf/eth0/forwarding
```
9. restart services
```
service xinetd restart
service httpd restart
service radvd restart
```

=== Start each servers
1. `chkconfig xinetd on`
2. `chkconfig ftfp on`
3. `ip addr add 192.168.0.1/24 dev eth0`
4. `ip addr add 2012::1/64 dev eth0`
5. `service dhcpd start`
5. `service dhcpd6 start`

Reference :
https://docs.fedoraproject.org/en-US/Fedora/18/html/Installation_Guide/s1-netboot-pxe-config-efi.html
https://access.redhat.com/site/documentation//en-US/Red_Hat_Enterprise_Linux/6/html/Installation_Guide/ap-install-server.html


=== An example for all steps
install
url --url=http://download.englab.brq.redhat.com/pub/rhel/released/RHEL-6/6.4/Workstation/x86_64/os/
lang en_US.UTF-8
keyboard us
cmdline
rootpw redhat
timezone --utc Europe/Prague

# partitioning: autopart
bootloader --location=mbr
zerombr
clearpart --all --initlabel
autopart

network --device=eth0 --bootproto=dhcp --onboot=no --activate

#services --enabled=dhcpd,xinetd,httpd
services --enabled=xinetd,httpd
firewall --enable --trust eth0

reboot


%packages
@base
@client-mgmt-tools
@core
@debugging
@basic-desktop
@desktop-debugging
@desktop-platform
@directory-client
@fonts
@input-methods
@internet-browser
@java-platform
@legacy-x
@network-file-system-client
@print-client
@remote-desktop-clients
@server-platform
@workstation-policy
@x11
mtools
pax
python-dmidecode
oddjob
sgpio
device-mapper-persistent-data
abrt-gui
samba-winbind
certmonger
pam_krb5
krb5-workstation
libXmu

wireshark-gnome
dhcp
tftp-server
httpd
syslinux
emacs
tftp
%end

# NB.PY
%post
wget http://file.brq.redhat.com/~mbanas/nb.py -O /root/nb.py

cat > /etc/yum.repos.d/nb.repo << EOF
[nb_repo]
name=nb_repo
baseurl=http://download.eng.brq.redhat.com/pub/rhel/released/RHEL-6/6.4/Workstation/x86_64/os/
gpgcheck=0

EOF
%end

# SCRIPTS
%post
cat > /usr/local/sbin/pxe-setup <<EOF
#!/bin/bash
if [ -z "\$1" ]; then
    exit 1
fi
wget --progress=bar -r \$1 -nH --cut-dirs 8 -P /var/www/html/os/
cp -r /var/www/html/os/images /var/lib/tftpboot
# TODO: rozbalit grub.efi a vytvorit grub.conf v $TFTPBOOT
mkdir /mnt/efiboot
mount -o loop /var/lib/tftpboot/images/efiboot.img /mnt/efiboot
cp /mnt/efiboot/EFI/BOOT/BOOTX64.efi /var/lib/tftpboot/grub.efi
umount /mnt/efiboot
rmdir /mnt/efiboot
EOF
chmod +x /usr/local/sbin/pxe-setup

cat > /usr/local/sbin/pxe-start <<EOF
#!/bin/bash
echo "Be sure to have cable unplugged from internal network!!!"
read -p "press enter to continue"
nmcli con down id 'System eth0'
nmcli con up id static
service dhcpd start
service dhcpd6 start
EOF
chmod +x /usr/local/sbin/pxe-start

cat > /usr/local/sbin/pxe-stop <<EOF
#!/bin/bash
service dhcpd stop
service dhcpd6 stop
nmcli con down id static
nmcli con up id 'System eth0'
EOF
chmod +x /usr/local/sbin/pxe-stop
%end

# CONFIGURATION
%post --log=/dev/console
# INIT 5
sed -i -re 's/^id:[0-9]:initdefault:$/id:5:initdefault:/'  /etc/inittab
# AUTOLOGIN                                                                     
sed -ri '/^\[daemon\]$/ a AutomaticLogin = root' /etc/gdm/custom.conf
sed -ri '/^\[daemon\]$/ a AutomaticLoginEnable = true' /etc/gdm/custom.conf

cat > /etc/dhcp/dhcpd.conf <<EOF
option domain-name "local";
option domain-name-servers 192.168.100.1;

# UEFI
#  option space PXE;
#  option PXE.mtftp-ip    code 1 = ip-address;
#  option PXE.mtftp-cport code 2 = unsigned integer 16;
#  option PXE.mtftp-sport code 3 = unsigned integer 16;
#  option PXE.mtftp-tmout code 4 = unsigned integer 8;
#  option PXE.mtftp-delay code 5 = unsigned integer 8;
option arch code 93 = unsigned integer 16; # MOZNA POVOLIT PRO UEFI

class "pxeboot" {
	match if substring(option vendor-class-identifier, 0, 9)="PXEClient";
	next-server 192.168.100.1;
	if option arch = 00:07 {
            filename "grub.efi";
        } else {
	    filename "pxelinux.0";
        }
}


subnet 192.168.100.0 netmask 255.255.255.0 {
        range 192.168.100.10 192.168.100.100;
        option routers 192.168.100.1;
}
EOF

cat > /etc/dhcp/dhcpd6.conf <<EOF
default-lease-time 2592000;
preferred-lifetime 604800;
option dhcp-renewal-time 3600;
option dhcp-rebinding-time 7200;
allow leasequery;
option dhcp6.name-servers 2001:db8:1:0::1;
option dhcp6.domain-search "test.example.com","example.com";
option dhcp6.info-refresh-time 21600;
option dhcp6.bootfile-url code 59 = string;
subnet6 2001:db8:1:0::/64 {
        range6 2001:db8:1:0::100 2001:db8:1:0::200;
        range6 2001:db8:1:0:: temporary;
        prefix6 2001:db8:1:0:: 2001:db8:1:11:: /64;
	option dhcp6.bootfile-url "tftp://[2001:db8:1:0::1]/grub.efi";
}
EOF

TFTPBOOT=/var/lib/tftpboot
mkdir $TFTPBOOT/pxelinux.cfg
cat >$TFTPBOOT/pxelinux.cfg/default <<EOF
DISPLAY boot.txt
TIMEOUT 100
ONTIMEOUT l
LABEL i
	kernel images/pxeboot/vmlinuz
	append initrd=images/pxeboot/initrd.img repo=http://192.168.100.1/os/
	ipappend 2
LABEL l
        LOCALBOOT 0
EOF

cat >$TFTPBOOT/boot.txt <<EOF
Boot Menu
---------
i - install
l - local boot
EOF

cp /usr/share/syslinux/pxelinux.0 $TFTPBOOT
cp /usr/lib/syslinux/pxelinux.0 $TFTPBOOT

cat >$TFTPBOOT/efidefault <<EOF
splashimage=/foo
timeout 5
title install
	root (nd)
	kernel /images/pxeboot/vmlinuz ip=dhcp repo=http://192.168.100.1/os/
	initrd /images/pxeboot/initrd.img
title simple.ks
	root (nd)
	kernel /images/pxeboot/vmlinuz ip=dhcp ks=http://192.168.100.1/simple.ks
	initrd /images/pxeboot/initrd.img
EOF

cat >/var/www/html/simple.ks <<EOF
install
url --url=http://192.168.100.1/os/
lang en_US.UTF-8
keyboard us
network --device eth0 --bootproto dhcp
rootpw rootoor
authconfig --useshadow
timezone --utc Europe/Prague
bootloader --location=mbr
clearpart --all --initlabel
autopart
reboot
%%packages
%%end

%%post
if [ -x '/usr/sbin/efibootmgr' ]; then
    BOOT=\$(/usr/sbin/efibootmgr -v | grep BootOrder | awk '{print $2}')
    PXE_SLOT=\$(/usr/sbin/efibootmgr -v | grep -Ei '(netboot|pxe|IPv4)' |head -n 1 |cut -c5-8)
    # If There is no Netboot Entry we can't continue
    # You have to manually setup a Netboot entry first
    # from EFI maint menu.
    if [ ! -z $PXE_SLOT ]; then
       NEWBOOT=\$(echo $BOOT| sed -e "s/\s$PXE_SLOT,//")
       NEWBOOT=\$(echo $NEWBOOT| sed -e "s/,$PXE_SLOT//")
       /usr/sbin/efibootmgr -o $PXE_SLOT,$NEWBOOT
       /usr/sbin/efibootmgr -n \$(echo $BOOT | cut -d, -f 1)
    fi
fi

%%end
EOF

chkconfig tftp on
sed -ri 's/(.*flags.*)/#\1/' /etc/xinetd.d/tftp

/usr/local/sbin/pxe-setup ftp://download.englab.brq.redhat.com/pub/rhel/released/RHEL-6/6.4/ComputeNode/x86_64/os/

# TODO: pridat staticky nastaveni NM
cat >/etc/sysconfig/network-scripts/ifcfg-pxe_server <<EOF
TYPE=Ethernet
BOOTPROTO=none
IPADDR=192.168.100.1
PREFIX=24
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
IPV6INIT=yes
IPV6FORWARDING=yes
IPV6ADDR=2001:db8:1:0::1
NAME=pxe-server
UUID=e913932d-a73c-4736-9c58-a05cdc055dcc
ONBOOT=no
EOF

%end
