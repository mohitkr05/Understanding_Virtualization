# Four network models of KVM virtualization

## 1. Brief description of the four network models:
### 1.1 Isolation model
The characteristic of this model is that all virtual machines on the host can form a network, but the virtual machine cannot communicate with the host, nor can it communicate with hosts on other networks or with virtual machines on other hosts; The host is just connected to a switch, and this switch is virtualized on the host, which is what we usually call a bridge device

### 1.2 Routing Model
The feature of this model is that on the basis of the isolation model, the IP routing and forwarding function is enabled on the host machine. At this time, the host machine is equivalent to a router to complete the data message forwarding between the virtual machine and the host machine. Therefore, the virtual machine can communicate with the physical network card of the host machine, but it cannot communicate with hosts other than the host machine, because the host machine does not translate the source address, and the message can be sent to the external host, but the external host cannot respond.

### 1.3 NAT model
The characteristic of this model is that when the virtual machine needs to communicate with the outside world, it needs to convert the source IP address to the IP address of the physical network card, so that the external host can correctly send the response message to the target IP address (destination IP address) after receiving the message. The IP address of the physical network card of the host), therefore, the communication between the virtual machine and the outside is realized;

Note: This model only realizes the source address translation, and realizes the communication between the virtual machine and the external network, but the external network cannot access the virtual machine. If it is to be realized, the target address translation needs to be done on the host machine.

### 1.4 Bridge Model
The feature of this model is that by creating a virtual network card, the virtual machine network card is assigned an IP address that can access the external network; at this time, the physical network card is equivalent to a switch device; the virtual machine can be assigned an IP address to communicate with the external network. External network communication; at this time, the virtual machine is equivalent to a single host in the local area network where the host is located, and its status as the host is equal, and there is no dependency relationship.

## 2. Preliminary preparation
### 2.1 Environment description
This test is carried out in the virtual machine environment of VMware workstation version 12, pay attention to enable the cpu virtualization function;
Network adapters share the host network's internet using the host-only model.
OS: centos-6.5_x86-64
eth0: 10.241.96.176/24
Using kvm to deploy virtual machines, the disk image file uses cirros-0.3.5-x86_64-disk.img

### 2.2 Install qemu-kvm and resolve dependencies
```C
## Load the kvm module:
[root@C65-201 ~]# modprobe kvm
[root@C65-201 ~]# modprobe kvm-intel

## Install qemu-kvm, bridge-utils
[root@C65-201 ~]# yum install qemu-kvm bridge-utils

## Make sure the CPU supports HVM
[root@C65-201 ~]# grep -E --color=auto "(vmx|svm)" /proc/cpuinfo
```

## 3. Isolation model

### 3.1 Virtual machine network in isolation model
> In the same host, how does the virtual machine communicate with the bridge in the host?

The virtual machine in the isolation model cannot communicate with the physical network card of the host, but can communicate with the bridge in the host;
The VM in the figure represents a virtual machine, and the network card of each virtual machine includes the first half and the second half; the first half of the vNIC is on the virtual machine, and the second half of the VIF is on the host. In the figure, the second half of the vNIC network card in the virtual machine corresponds to the VIF network card, and the data packets sent to the vNIF network card in the VM virtual machine will be sent to the second half of the network card VIF;

> How do virtual machines communicate with virtual machines in the same host?

In the figure, br-in is the bridge device on the host, also known as Virtual Switch or Bridge; in the isolation model, it is equivalent to a layer-2 virtual switch. To realize the communication between virtual machines in the same host, you need Several conditions are met:

* 1) A Bridge needs to be created in the host machine, which is br-in in the figure
* 2) The first half of the IP address of each virtual machine that needs to communicate with each other must be in the same network segment;
* 3) The second half of the network card VIF network card device of each virtual machine that needs to communicate with each other is added to the bridge device n bridge of br-in.

Note: If you want to realize that virtual machines on the same network segment cannot communicate, that is, isolate virtual machines, you need to create a different network bridge and add the second half of the virtual machine to a different bridge device to achieve isolation.

> How does the virtual machine communicate with the physical network card of the host?

Virtual machines in the isolation model cannot communicate with the host's physical NIC.

> How does the virtual machine communicate with the external network?

Virtual machines in the isolation model cannot communicate with the outside world.

### 3.2 Use qemu-kvm to create a virtual machine and implement the isolation model
#### 3.2.1 Create br-in bridge
```C
## Create bridge
[root@C65-201 ~]# brctl addbr br-in

## View bridges
[root@C65-201 ~]# brctl show
bridge name bridge id STP enabled interfaces
br-in 8000.000000000000 no             

## Activate the bridge to make it start
[root@C65-201 ~]# ip link set br-in up
```

#### 3.2.2 Create script qemu-ifup
When the virtual machine is created and started, add the first half of the network card of the VM through the startup parameters of the qemu-kvm command, and the second half of the network card is automatically created by specifying the ifname parameter, but it needs to be manually activated and added to the bridge br-in; usually this part of the operation It is done by the script qemu-ifup.
```C
[root@kvm ~]# vim /etc/qemu-ifup
#!/bin/bash
BRIDGE=br-in
if [ -n $1 ]; then
    ip link set $1 up
    sleep 1
    brctl addif $BRIDGE $1
	[ $? -eq 0 ] && exit 0 || exit 1
else
    echo "Error: no interface specified."
	exit 1
the fi
```

After saving, add execution permission to the script and check the syntax
```C
[root@C65-201 ~]# chmod +x /etc/qemu-ifup # Add execution permission
[root@C65-201 ~]# bash -n /etc/qemu-ifup # Check if the syntax is wrong
```

When the virtual machine stops, the second half of the virtual machine network card will be automatically down from the host, so there is no need to write down scripts

#### 3.2.3 Create the first virtual machine guest01 and start it
```C
## Create guest01 and start it
[root@C65-201 ~]# qemu-kvm -name guest01 -m 128 -smp 1 --drive file=/images/kvm/guest01.img,if=virtio,media=disk -net nic,model=virtio, macaddr=52:54:00:11:22:aa -net tap,ifname=vif1.0,script=/etc/qemu-ifup -daemonize

## Configure the ip address of guest01
# ip addr add 10.0.1.1/24 dev eth0
```

#### 3.2.4 Create the second virtual machine guest02 and start it
```C
## Create guest02 and start it
[root@C65-201 ~]# qemu-kvm -name guest02 -m 128 -smp 1 --drive file=/images/kvm/guest02.img,if=virtio,media=disk -net nic,model=virtio, macaddr=52:54:00:11:22:bb -net tap,ifname=vif2.0,script=/etc/qemu-ifup -daemonize

## Configure the ip address of guest02
# ip addr add 10.0.1.2/24 dev eth0
```

#### 3.2.5 Communication test
Check the bridge device on the host, and you can see that the second half of the network cards of the two virtual machines have been added to the br-in bridge

![image](https://user-images.githubusercontent.com/87458342/135237571-a28b68b9-15d0-478c-9aa3-428fa27e97b3.png)

Test communication between guest01 and guest02

![image](https://user-images.githubusercontent.com/87458342/135237631-e0d9278c-32de-4675-b3d1-267488698b46.png)

Test host access to virtual machine

![image](https://user-images.githubusercontent.com/87458342/135237662-ba3a74d8-c4e3-4116-91ab-94a7ff17ba83.png)

According to the above results, the virtual machine can be accessed between the virtual machines, and the host cannot access the virtual machine, realizing the isolation model

## 4. Routing Model

![image](https://user-images.githubusercontent.com/87458342/135237789-ed751476-57c8-424e-8da0-383257ce0743.png)

### 4.1 Virtual machine network in the routing model model
> In the same host, how does the virtual machine communicate with the bridge in the host?

For the communication mechanism between the virtual machine and the host bridge in the routing model, refer to the isolation model

> How do virtual machines communicate with virtual machines in the same host?

For the communication mechanism between virtual machines and virtual machines in the routing model, refer to the isolation model

> How does the virtual machine communicate with the physical network card of the host?

For the virtual machine in the routing model to communicate with the physical network card of the host, the following conditions must be met:

* 1) The host must enable the IP forwarding function in the kernel, that is, net.ipv4.ip_forward = 1;
* 2) The bridge br-in in the host needs to add an IP address on the same network segment as the virtual machine;
* 3) The virtual machine needs to add a route, and the next hop address is set to the ip of the bridge br-in;

> How does the virtual machine communicate with the external network?

The virtual machine in the routing model cannot communicate with the outside; because when the virtual machine is sent to the host on the external network, the source address is the intranet address, and the intranet address cannot communicate directly with the outside, and there is no conversion of the source address to the IP address of the physical network card , therefore, when the target address in the response message sent by the external network is the IP address of the virtual machine, it cannot reach the physical network card of the host machine, so the virtual machine cannot receive the response message.

### 4.2 Use qemu-kvm to create a virtual machine and implement the routing model
#### 4.2.1 Create scripts qemu-rtup and qemu-rtdown
When the virtual machine is created and started, add the first half of the network card of the VM through the startup parameters of the qemu-kvm command, and the second half of the network card is automatically created by specifying the ifname parameter, but it needs to be manually activated and added to the bridge br-in; usually this part of the operation It is completed by the qemu-rtup script; when the virtual machine is closed, it is completed by qemu-rtdown to remove the second half of the network card;
```C
## Add the first half of the NIC script of the VM
[root@C65-201 ~]# vim /etc/qemu-rtup
#!/bin/bash
bridge="br-in"
ifaddr=192.168.1.254 # IP address of bridge br-in
checkbr() {
    if brctl show | grep -i "^$1"; then
        return 0
    else
        return 1
    the fi
}
initbr() {
    brctl addbr $bridge
    ip link set $bridge up
    ip addr add $ifaddr/24 dev $bridge
}
enable_ip_forward() {
    sysctl -w net.ipv4.ip_forward=1
}
setup_rt() {
    checkbr $bridge
    if [ $? -eq 1 ]; then
        initbr
        enable_ip_forward
    the fi
}

if [ -n "$1" ]; then
    setup_rt
    ip link set $1 up
    brctl addif $bridge $1
    exit 0
else
    echo "Error: no interface specified."
    exit 1
the fi

## Delete the second half of the NIC script of the VM
[root@C65-201 ~]# vim /etc/qemu-rtdown
#!/bin/bash

bridge="br-in"

isalone_bridge() {
    if ! brctl show | awk "/^$bridge/{print \$4}" | grep "[^[:space:]]" &> /dev/null; then
        ip link set $bridge down
        brctl delbr $bridge
    the fi

}
if [ -n "$1" ];then
    ip link set $1 down
    brctl delif $bridge $1
    isalone_bridge
    exit 0
else
    echo "Error: no interface specified."
    exit 1
the fi

## After saving, add execution permission to the script and check the syntax
[root@C65-201 ~]# chmod +x /etc/{qemu-rtup,qemu-rtdown} # Add execution permission
[root@C65-201 ~]# bash -n /etc/qemu-rtup # Check if the syntax is wrong
[root@C65-201 ~]# bash -n /etc/qemu-rtdown # Check if the syntax is wrong
```

#### 4.2.2 Create the first virtual machine guest01 and start it
```C
## Create guest01 and start it
[root@C65-201 ~]# qemu-kvm -name guest01 -m 128 -smp 1 --drive file=/images/kvm/guest01.img,if=virtio,media=disk -net nic,model=virtio, macaddr=52:54:00:11:22:aa -net tap,ifname=vif1.0,script=/etc/qemu-rtup,downscript=/etc/qemu-rtdown -daemonize

## Configure its ip address in guest01
# ip addr add 192.168.1.1/24 dev eth0

## Add a route to guest01
# ip route add default via 192.168.1.254
```

#### 4.2.3 Create the second virtual machine guest02 and start it
```C
## Create guest02 and start it
[root@C65-201 ~]# qemu-kvm -name guest02 -m 128 -smp 1 --drive file=/images/kvm/guest02.img,if=virtio,media=disk -net nic,model=virtio, macaddr=52:54:00:11:22:bb -net tap,ifname=vif2.0,script=/etc/qemu-rtup,downscript=/etc/qemu-rtdown -daemonize

## Configure its ip address in guest02
# ip addr add 192.168.1.2/24 dev eth0

 
## Add a route to guest02
# ip route add default via 192.168.1.254
```

#### 4.2.4 Communication test
![image](https://user-images.githubusercontent.com/87458342/135238397-43b18204-92d1-41e9-8054-be0b3ddf579c.png)

![image](https://user-images.githubusercontent.com/87458342/135238560-9e6f6ad4-df9b-4645-a1c4-588669a46f63.png)

Unable to communicate with hosts on the external network

![image](https://user-images.githubusercontent.com/87458342/135238617-874161f5-d3cc-4c14-a23d-23f5fa4c9bcf.png)

According to the above results, the virtual machine and the host can communicate with each other, but cannot communicate with the external network, realizing the routing model

## 5. NAT model

![image](https://user-images.githubusercontent.com/87458342/135238713-dcc0bada-e45d-4c3d-bffd-07bfbe2d3833.png)

### 5.1 Virtual machine network in NAT model
The communication mechanism of the NAT model is basically the same as that of the routing model. The only difference is that the NAT model can enable the virtual machine to communicate directly with the external network; that is, only in the NAT model, in the host machine SNAT address translation, the source address of the virtual machine The IP is converted to the IP address of the physical network card, and the message is sent to the host on the external network; the target IP address of the response message constructed by the host on the external network after receiving the message is the IP address of the physical network card of the host machine, and then the host machine After sending the message to the virtual machine, the communication between the virtual machine and the external network is realized.

### 5.2 Use qemu-kvm to create a virtual machine and implement the NAT model
#### 5.2.1 Create scripts qemu-natup and qemu-natdown
When the virtual machine is created and started, add the first half of the network card of the VM through the startup parameters of the qemu-kvm command, and the second half of the network card is automatically created by specifying the ifname parameter, but it needs to be manually activated and added to the bridge br-in; usually this part of the operation It is completed by the qemu-natup script; when the virtual machine is closed, it is completed by qemu-natdown to remove the second half of the network card, delete the nat rules, etc.;

```C
## Add script
[root@C65-201 ~]# vim /etc/qemu-natup
#!/bin/bash
bridge="br-in"
net="192.168.1.0/24"
ifaddr=192.168.1.254

checkbr() {
         if brctl show | grep -i "^$1"; then
                  return 0
         else
                  return 1
         the fi
}

initbr() {
         brctl addbr $bridge
         ip link set $bridge up
         ip addr add $ifaddr dev $bridge
}

enable_ip_forward() {
         sysctl -w net.ipv4.ip_forward=1
}

setup_nat() {
         checkbr $bridge
         if [ $? -eq 1 ]; then
                  initbr
                  enable_ip_forward
                  iptables -t nat -A POSTROUTING -s $net ! -d $net -j MASQUERADE
         the fi
}

if [ -n "$1" ]; then
         setup_nat
         ip link set $1 up
         brctl addif $bridge $1
         exit 0
else
         echo "Error: no interface specified."
         exit 1
the fi

## delete script
[root@C65-201 ~]# vim /etc/qemu-natdown
#!/bin/bash
bridge="br-in"
remove_rule() {
         iptables -t nat -F
}

isalone_bridge() {
         if ! brctl show | awk "/^$bridge/{print \$4}" | grep "[^[:space:]]" &> /dev/null; then
                  ip link set $bridge down
                  brctl delbr $bridge
                  remove_rule
         the fi
}

if [ -n "$1" ];then
         ip link set $1 down
         brctl delif $bridge $1
         isalone_bridge
         exit 0
else
         echo "Error: no interface specified."
         exit 1
the fi

 

## After saving, add execution permission to the script and check the syntax
[root@C65-201 ~]# chmod +x /etc/{qemu-natup,qemu-natdown} # Add execution permission
[root@C65-201 ~]# bash -n /etc/qemu-natup # Check if the syntax is wrong
[root@C65-201 ~]# bash -n /etc/qemu-natdown # Check if the syntax is wrong
```

#### 5.2.2 Create the first virtual machine guest01 and start it
```C
## Create guest01 and start it
[root@C65-201 ~]# qemu-kvm -name guest01 -m 128 -smp 1 --drive file=/images/kvm/guest01.img,if=virtio,media=disk -net nic,model=virtio, macaddr=52:54:00:11:22:aa -net tap,ifname=vif1.0,script=/etc/qemu-natup,downscript=/etc/qemu-natdown -daemonize

## Configure its ip address in guest01
# ip addr add 192.168.1.1/24 dev eth0

## Add a route to guest01
# ip route add default via 192.168.1.254
```

#### 5.2.3 Create the second virtual machine guest02 and start it
```C
## Create guest02 and start it
[root@C65-201 ~]# qemu-kvm -name guest02 -m 128 -smp 1 --drive file=/images/kvm/guest02.img,if=virtio,media=disk -net nic,model=virtio, macaddr=52:54:00:11:22:bb -net tap,ifname=vif2.0,script=/etc/qemu-natup,downscript=/etc/qemu-natdown -daemonize

## Configure its ip address in guest02
# ip addr add 192.168.1.2/24 dev eth0

## Add a route to guest02
# ip route add default via 192.168.1.254
```

#### 5.2.4 Communication test

![image](https://user-images.githubusercontent.com/87458342/135239140-12c5692b-396c-4963-9a98-7cb8480f9a7d.png)

![image](https://user-images.githubusercontent.com/87458342/135239138-33fedcda-3317-4b8f-a300-265201f70e95.png)

![image](https://user-images.githubusercontent.com/87458342/135239139-649d8e6e-537a-40fd-bff7-bc1455e9cb10.png)

According to the above results, the virtual machine and the host can communicate with each other and communicate with the external network, realizing the NAT model

## 6. Bridge model

![image](https://user-images.githubusercontent.com/87458342/135239214-f7f8f2ec-bfd8-464a-8d2d-d47eaf9c309e.png)

### 6.1 Virtual Machine Networking in the Bridge Model
#### The bridge model is different from the isolation model, routing model and NAT model; under the model, the host will virtualize a network card as the communication network card of the host, and the physical network card of the host will become a bridge device (also called Switch), at this time, the virtual machine is equivalent to a single host in the local area network where the host is located, and its status as the host is equal, and there is no dependency relationship.

### 6.2 Use qemu-kvm to create a virtual machine and implement the bridge model
#### 6.2.1 Create br-in bridge and add eth0 to br-in bridge

```C
## Create a virtual network card for the host, and use the physical network card eth0 as a bridge device
[root@C65-201 ~]# cd /etc/sysconfig/network-scripts/
[root@C65-201 network-scripts]# cp ifcfg-eth0 ifcfg-br-in

##Modify br-in
[root@C65-201 network-scripts]# vim ifcfg-br-in
DEVICE=br-in
TYPE=Bridge
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
IPADDR=10.241.96.176
NETMASK=255.255.255.0
GATEWAY=10.241.96.1
DNS1=10.241.96.1
 
## Modify eth0
[root@C65-201 network-scripts]# vim ifcfg-eth0
DEVICE=eth0
HWADDR=00:0C:29:64:9C:CD
TYPE=Ethernet
UUID=62a2f66a-754e-4c46-810c-fd23abff3d5a
ONBOOT=yes
BRIDGE=br-in
NM_CONTROLLED=yes
BOOTPROTO=static

## Restart network service
[root@C65-201 network-scripts]# service network restart
```

view ip

![image](https://user-images.githubusercontent.com/87458342/135239386-a61ad168-9051-4d3a-a9a6-b5a204cd24f1.png)

View Bridge Devices

![image](https://user-images.githubusercontent.com/87458342/135239430-fa60888a-7b0e-477e-a34e-a26a08ac0a99.png)

At this point, the physical network card eth0 has been added to br-in

#### 6.2.2 Create script qemu-ifup

This script is consistent with the qemu-ifup script of the isolation model, just refer to 3.2.2

#### 6.2.3 Create virtual machine guest01 and start it
```C
## Create guest01 and start it
[root@C65-201 ~]# qemu-kvm -name guest01 -m 128 -smp 1 --drive file=/images/kvm/guest01.img,if=virtio,media=disk -net nic,model=virtio, macaddr=52:54:00:11:22:aa -net tap,ifname=vif1.0,script=/etc/qemu-ifup -daemonize

## Configure its ip address in guest01
# ip addr add 10.241.96.90/24 dev eth0

## Add routing in guest01
# ip route add default via 10.241.96.1
```

#### 6.2.4 Communication test

![image](https://user-images.githubusercontent.com/87458342/135239576-385c8319-3bc9-46f1-a73f-b1944c6c3f68.png)

The above are the four simplest network models of KVM. The bridge model is the most used in the production environment, but the security of the bridge model is the lowest, because the virtual machine is directly exposed to the external network; and in the current container technology, The NAT network model is widely used, such as docker
