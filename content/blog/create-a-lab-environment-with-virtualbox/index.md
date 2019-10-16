---
title: Create a lab environment with VirtualBox
description: Using VirtualBox to create a lab environment
date: 2019-10-14 23:45:00
comments: true
copy2clipboard: true
tags:
  - virtualbox
  - lab
---

[VirtualBox](https://www.virtualbox.org/) is a great tool often used to simulate a entire lab. It could create for you all the needed to accomplish this goal. 

VirtualBox can create multiples Networks and differents kind of virtual machines. On this post, I will show you, how to create a lab environment step by step.

On this scenario, I'll use Virtualbox 6 and two types of networks: 

* `Nat Network`, the main nic will use it to ougoing packages and to communicate among the others VMs, and 
* `Host-Only Adapter`, it will be use to get access from your desktop into any VMs.

----
## Steps 

#### Installation

Choose your appropiate package from this [URL](https://www.virtualbox.org/wiki/Linux_Downloads)

In my case,  I use Ubuntu, and it is simple:

```shell
sudo apt-get update
sudo apt-get install virtualbox
```
Consider install the VirtualBox Extension Pack from [here](https://download.virtualbox.org/virtualbox/6.0.12/Oracle_VM_VirtualBox_Extension_Pack-6.0.12.vbox-extpack)


----

#### Setup Host Network

![](https://www.nakivo.com/blog/wp-content/uploads/2019/07/VirtualBox-network-settings-%E2%80%93-VMs-use-the-host-only-network.png)

By default, VirtualBox creates a `Host-Only Adapter` network, and it use this CIDR: `192.168.56.0/24`. 

This network is used to communicate with our Host (a.k.a our desktop machine) with the Virtual Machines (VMs). Also, on this lab environment we needed a Nat Network, who is another kind of network.

----

#### Setup Nat Network

![](https://www.nakivo.com/blog/wp-content/uploads/2019/07/VirtualBox-network-modes-%E2%80%93-how-the-NAT-mode-works.png)

A Nat network is use by the VMs, to give access to external world. Virtualbox creates this network using theses steps.

E.g. Create the network `10.0.2.0/24` with name `natnet`

```shell
VBoxManage natnetwork add --netname natnet --network "10.0.2.0/24" --enable --dhcp on
VBoxManage natnetwork start --netname natnet
```

----

#### Create a VM base

I need to create a VM to use it as base to create other VM's. In this example, I will use Centos 7 image, and the VM will be named `centos7`

```shell
export VM=centos7
```

```shell
VBoxManage createvm --name $VM --ostype RedHat_64 --register
```

Now, we need to associate the natnetwork created previously with the nic1 interface

```shell
VBoxManage modifyvm $VM --nic1 natnetwork --nat-network1 natnet
```
Create another nic2 type `Host-Only Adapter`

```shell
VBoxManage modifyvm $VM --nic2 hostonly --hostonlyadapter2 vboxnet0
```

If you want to increase the CPU and Memory, here is a good opportunity

```shell
VBoxManage modifyvm $VM --cpus 2 --memory 512
```

The VM will need a storage, then create a dynamic disk with 10GB and a SATA Controller to attach the disk at the sequence

```shell
VBoxManage createhd --filename ~/VirtualBox\ VMs/$VM/$VM.vdi --size 10240
VBoxManage storagectl $VM --name "SATA Controller" --add sata --controller IntelAHCI
VBoxManage storageattach $VM --storagectl "SATA Controller" --port 0 --device 0 \
  --type hdd --medium ~/VirtualBox\ VMs/$VM/$VM.vdi
```

Create a IDE controller to mount the iso installer image. In my case, I use CentOS 7 minimal iso

```shell
VBoxManage storagectl $VM --name "IDE Controller" --add ide
VBoxManage storageattach $VM --storagectl "IDE Controller" --port 0 \
  --device 0 --type dvddrive --medium ~/isos/CentOS-7-x86_64-Minimal-1810.iso
```

Finally, you could setup other misc system options

```shell
VBoxManage modifyvm $VM --ioapic on
VBoxManage modifyvm $VM --boot1 dvd --boot2 disk --boot3 none --boot4 none
```

And done, you will have some VM created like this:

![](/images/virtualbox_centos7.png)


Starting the VM (in headless mode) to install you new Centos7 VM

```shell
VBoxHeadless -s $VM
```
----

#### Install Centos OS

Start the installation 

![](vb-centos7-01.png)

Setup your localtime. In my case, as I live in Brazil, it is `America/Sao_Paulo`

![](vb-centos7-02.png)

Create a `ansible` user, and make it Administrator

![](vb-centos7-03.png)

Also, you need to setup the root password

![](vb-centos7-04.png)

Finished the installation, login into the VM as root user and edit the first one NIC

![](vb-centos7-05.png)

You need to pay attention on this variables:

```toml
BOOTPROTO=static
ONBOOT=yes
IPADDR=10.0.2.2
PREFIX=24
GATEWAY=10.0.2.1
DNS1=10.0.2.1
```

![](vb-centos7-06.png)

Edit the second NIC

![](vb-centos7-07.png)

Pay attention on this variables

```toml
BOOTPROTO=static
DEFROUTE=no
ONBOOT=yes
IPADDR=192.168.56.2
PREFIX=24
```

![](vb-centos7-08.png)

Restart the system network service and check the network 

```shell
systemctl restart network 
ip addr | grep "inet "
ip route
```

![](vb-centos7-09.png)


#### Testing 

SSH access to VM from the `Host-only Adapter`

```console
$ ssh root@192.168.56.2
root@192.168.56.2's password: 
Last login: Tue Oct 15 23:10:05 2019 from 192.168.56.1
[root@localhost ~]# ping www.terra.com
PING www.terra.com (208.70.188.57) 56(84) bytes of data.
64 bytes from www.terra.com (208.70.188.57): icmp_seq=1 ttl=53 time=151 ms
64 bytes from www.terra.com (208.70.188.57): icmp_seq=2 ttl=53 time=151 ms
^C
--- www.terra.com ping statistics ---
3 packets transmitted, 2 received, 33% packet loss, time 2002ms
[root@localhost ~]# 
```

#### Final Step

Once you have configured the operating system, you can shutdown and eject the DVD.

```console
$ ssh root@192.168.56.2
root@192.168.56.2's password:
Last login: Tue Oct 15 23:10:05 2019 from 192.168.56.1
[root@localhost ~]# halt -p
```

```shell
VBoxManage storageattach $VM --storagectl "IDE Controller" --port 0 \
  --device 0 --type dvddrive --medium none
```


----

### Others usefull commands \#

Remove nat network 
```shell
VBoxManage natnetwork remove --netname natnet
```

Disable the dhcp
```shell
VBoxManage natnetwork modify --netname natnet --dhcp off
```

Start the nat network
```shell
VBoxManage natnetwork start --netname natnet
```

Take snapshots
```shell
VBoxManage snapshot $VM take <name of snapshot>
```

Revert snapshot
```shell
VBoxManage snapshot $VM restore <name of snapshot>
```

Delete VM
```shell
VBoxManage unregistervm $VM --delete
```

----

### Sources \#

https://www.oracle.com/technical-resources/articles/it-infrastructure/admin-manage-vbox-cli.html

https://www.virtualbox.org/manual/ch08.html

https://www.perkin.org.uk/posts/create-virtualbox-vm-from-the-command-line.html

https://www.nakivo.com/blog/virtualbox-network-setting-guide/

https://www.virtualbox.org/manual/ch06.html

https://www.linuxtechi.com/manage-virtualbox-virtual-machines-command-line/

