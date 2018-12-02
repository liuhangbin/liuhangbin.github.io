---
layout: post
Title: openstack
Date: 2013-09-01
tags: [openstack, OVS]
---

Tips:
1) we need to use iproute-2.6.32-23.el6ost.netns.2.x86_64.rpm version for OVS
2) we need to use libvirt-0.10.2-23.el6 incase permission denaied
3) we need to use openstack kernel like 2.6.32-358.118.1.openstack.el6 for kernel support


$ cat OpenStack-Grizzly.repo
[OpenStack-Grizzly-Puddle]
name=OpenStack-Grizzly-Puddle
baseurl=http://download.lab.bos.redhat.com/rel-eng/OpenStack/Grizzly/latest/x86_64/os/
gpgcheck=0
enabled=1

[OpenStack-Grizzly-Puddle-debug]
name=OpenStack-Grizzly-Puddle Debuginfo
baseurl=http://download.lab.bos.redhat.com/rel-eng/OpenStack/Grizzly/latest/$basearch/debuginfo
gpgcheck=0
enabled=0

[OpenStack-Grizzly-Puddle-sources]
name=OpenStack-Grizzly-Puddle Sources
baseurl=http://download.lab.bos.redhat.com/rel-eng/OpenStack/Grizzly/latest/source
gpgcheck=0
enabled=0


##### packstack

PASSWD=${PASS:-redhat}
TOKEN=${TOKEN:-redhat}
packstack --gen-answer-file=packstack.cfg
if [ -z "$RANDOM_PASS" ];then
        sed -i s/PW=.*/PW=$PASSWD/ packstack.cfg
        sed -i s/ADMIN_TOKEN=.*/ADMIN_TOKEN=$TOKEN/ packstack.cfg
fi
# we use quantum ovs by default
# so we neednt care about NOVA_NETWORK_PUBIF and NOVA_NETWORK_PRIVIF
sed -i s/NOVA_NETWORK_HOST=.*/NOVA_NETWORK_HOST=/ packstack.cfg
# enable all in one ovs bridge if we are in STANDALONE mode
# or set the client IP address, only support two compute at present
if [ -z "$SERVERS" -o -z "$CLIENTS" ];then
        sed -i s/ALL_IN_ONE_OVS_BRIDGE=n/ALL_IN_ONE_OVS_BRIDGE=y/ packstack.cfg
else
        sed -i s/COMPUTE_HOSTS=.*/\&,`dig $CLIENTS +short`/ packstack.cfg
fi
# Use ovs vlan type by default
sed -i s/OVS_TENANT_NETWORK_TYPE=.*/OVS_TENANT_NETWORK_TYPE=vlan/ packstack.cfg
sed -i s/OVS_VLAN_RANGES=.*/OVS_VLAN_RANGES=physnet1:1:100/ packstack.cfg
sed -i s/OVS_BRIDGE_MAPPINGS=.*/OVS_BRIDGE_MAPPINGS=physnet1:br-link1/ packstack.cfg
# will set the bridge iface after install
#sed -i s/OVS_BRIDGE_IFACES=.*/OVS_BRIDGE_IFACES=br-link1:eth1/ packstack.cfg

packstack --answer-file=packstack.cfg

###### Add port
add_port()
{
        local bridge=$1
        local interface=$2
        ip addr flush $interface
        ovs-vsctl add-port $bridge $interface
        ip link set $interface up
        ip link set $bridge up
        pkill dhclient; sleep 3
        dhclient $bridge
}

# add port to bridge
add_port br-ex $CUR_IFACE
add_port br-link1 $SEC_IFACE
ip link set br-int up

##### start config

# config quantum/plugin.ini after install
sed -i s/^network_vlan_ranges=.*/\&,external/ /etc/quantum/plugin.ini
sed -i s/^bridge_mappings=.*/\&,external:br-ex/ /etc/quantum/plugin.ini

service quantum-openvswitch-agent restart
service quantum-server restart

source ~/keystonerc_admin

#virt-sysprep --add /tmp/images/$image_file
glance image-create --name RHEL6.4 --is-public true --disk-format qcow2 --container-format bare --file /tmp/images/$image_file
image_id=`glance image-list | awk '/RHEL6.4/ {print $2}'`

# create net and subnet
quantum net-create Network_Vlan_10 --provider:network_type vlan --provider:physical_network physnet1 --provider:segmentation_id 10 --shared
quantum subnet-create --gateway 192.168.10.254 --allocation-pool start=192.168.10.2,end=192.168.10.253 Network_Vlan_10 192.168.10.0/24
vlan_net_id=`nova net-list | awk '/Network_Vlan_10/ {print $2}'`
vlan_subnet_id=`quantum subnet-list | awk '/192.168.10.0/ {print $2}'`

quantum net-create Beaker_Network --provider:network_type flat --provider:physical_network external --router:external True  --shared
quantum subnet-create Beaker_Network 10.66.86.0/23 --allocation-pool start=10.66.87.180,end=10.66.87.230 --gateway 10.66.87.254 --enable_dhcp False
beaker_net_id=`nova net-list | awk '/Beaker_Network/ {print $2}'`

# create and config router
quantum router-create router
router_id=`quantum router-list | awk '/router/ {print $2}'`
quantum router-interface-add router $vlan_subnet_id
quantum router-gateway-set router Beaker_Network

if [ "$FLOATINGIP" ];then
        devide_id=`nova list`
        quantum port-show $device_id
        prot_id=`quantum port-list --device_id $device_id`
        quantum floatingip-create Ex_Network
        quantum floatingip-associate $floating_ip_id $port_id
fi

# security gourp config
nova secgroup-add-rule default icmp -1 -1 0.0.0.0/0
# wait for network sync, or nova boot may fail
sleep 30
nova keypair-add --pub-key ~/.ssh/network_rsa.pub network-qe

if [ -z "$SERVERS" -o -z "$CLIENTS" ];then
        nova boot --flavor 1 --key_name network-qe --image $image_id Test_1 --nic net-id=$vlan_net_id
        nova boot --flavor 1 --key_name network-qe --image $image_id Test_2 --nic net-id=$beaker_net_id
else
        nova boot --flavor 1 --key_name network-qe --availability-zone nova:$SERVERS --image $image_id Test_1 --nic net-id=$vlan_net_id
        nova boot --flavor 1 --key_name network-qe --availability-zone nova:$CLIENTS --image $image_id Test_2 --nic net-id=$vlan_net_id
fi


### summary

After install:
1. ovs-vsctl add-port br-ex eth0
1. glance image-create --name RHEL6.4 --is-public true --disk-format qcow2 --container-format bare --file /tmp/images/$image_file
1. nova keypair-add --pub-key ~/.ssh/network_rsa.pub network-qe



####### remove
$ cat rem_all.sh
#!/bin/bash
# Warning! Dangerous step! Destroys VMs
for x in $(virsh list --all | grep instance- | awk '{print $2}') ; do
    virsh destroy $x ;
    virsh undefine $x ;
done ;

# Warning! Dangerous step! Removes lots of packages
yum remove -y nrpe "*nagios*" puppet "*ntp*" "*openstack*" \
"*nova*" "*keystone*" "*glance*" "*cinder*" "*swift*" \
mysql mysql-server httpd "*memcache*" scsi-target-utils \
iscsi-initiator-utils perl-DBI perl-DBD-MySQL ;

# Warning! Dangerous step! Deletes local application data
rm -rf /etc/nagios /etc/yum.repos.d/packstack_* /root/.my.cnf \
/var/lib/mysql/ /var/lib/glance /var/lib/nova /etc/nova /etc/swift \
/srv/node/device*/* /var/lib/cinder/ /etc/rsync.d/frag* \
/var/cache/swift /var/log/keystone /var/log/cinder/ /var/log/nova/ \
/var/log/httpd /var/log/glance/ /var/log/nagios/ /var/log/quantum/ ;

umount /srv/node/device* ;
killall -9 dnsmasq tgtd httpd ;

vgremove -f cinder-volumes ;
losetup -a | sed -e 's/:.*//g' | xargs losetup -d ;
find /etc/pki/tls -name "ssl_ps*" | xargs rm -rf ;
for x in $(df | grep "/lib/" | sed -e 's/.* //g') ; do
    umount $x ;
done

#ovs-vsctl del-port br-link1 eth0
#ovs-vsctl del-br br-ex
ovs-vsctl del-br br-int
ovs-vsctl del-br br-link1
ip link delete dev int-br-link1

yum install -y openstack-packstack kernel-kernel-networking-openstack

