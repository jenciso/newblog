---
title: Create a lab environment with VirtualBox
description: Using VirtualBox to create a lab environment
date: 2019-10-14 23:45:00
tags:
  - virtualbox
  - lab
---

[VirtualBox](https://www.virtualbox.org/) is a great tool used to use for simulate a entire lab. It could create for you all the needed to accomplish this goal. It can create multiples Nat networks and differents kind of virtual machines. In this post, we will show you, step by step, how to create a lab environment for test purpose. 

We will use 2 networks: `Nat Network` and `Host Only Adapter`

----

#### VirtualBox Installation

Choose your appropiate package from this [URL](https://www.virtualbox.org/wiki/Linux_Downloads)

In my case, Ubuntu, I only use this command:

```shell
sudo apt-get update
sudo apt-get install virtualbox
```
----

#### Host Network

![](https://www.nakivo.com/blog/wp-content/uploads/2019/07/VirtualBox-network-settings-%E2%80%93-VMs-use-the-host-only-network.png)

By default, VirtualBox creates a `Host-Only Adapter` network using this CIDR: `192.168.56.0/24`. 

This network is used to communicate with our Host (a.k.a our desktop machine) with the Virtual Machine (VM). To create a network for our lab environment we needed a Nat Network

----

#### Nat Network

![](https://www.nakivo.com/blog/wp-content/uploads/2019/07/VirtualBox-network-modes-%E2%80%93-how-the-NAT-mode-works.png)

Create the network `10.0.2.0/24` with name `natnet`

```shell
VBoxManage natnetwork add --netname natnet --network "10.0.2.0/24" --enable --dhcp on
VBoxManage natnetwork start --netname natnet
```

----

#### Create a VM base

We need to create a VM to use it as base to create other VM's. In this example, we will use Centos 7 image, and the VM will be named `centos7`

```shell
export VM=centos7
```

```shell
VBoxManage createvm --name $VM --ostype RedHat_64 --register
```

Now, we need to associate the natnetwork created previously with the nic1 interface of the VM

```shell
VBoxManage modifyvm $VM --nic1 natnetwork --nat-network1 natnet
```
Create another nic2 type `Host-Only Adapter`

```shell
VBoxManage modifyvm $VM --nic2 hostonly --hostonlyadapter2 vboxnet0
```

Increase the vCPU and Memory

```shell
VBoxManage modifyvm $VM --cpus 2 --memory 512
```

Storage: Create a dynamic disk with 10GB and SATA Controller

```shell
VBoxManage createhd --filename ~/VirtualBox\ VMs/$VM/$VM.vdi --size 10240
VBoxManage storagectl $VM --name "SATA Controller" --add sata --controller IntelAHCI
VBoxManage storageattach $VM --storagectl "SATA Controller" --port 0 --device 0 \
  --type hdd --medium ~/VirtualBox\ VMs/$VM/$VM.vdi
```

Create a IDE controller to mount the iso installer image

```shell
VBoxManage storagectl $VM --name "IDE Controller" --add ide
VBoxManage storageattach $VM --storagectl "IDE Controller" --port 0 \
  --device 0 --type dvddrive --medium ~/isos/CentOS-7-x86_64-Minimal-1810.iso
```

Misc system options

```shell
VBoxManage modifyvm $VM --ioapic on
VBoxManage modifyvm $VM --boot1 dvd --boot2 disk --boot3 none --boot4 none
```

Finally you will have this configuration

![](/images/virtualbox_centos7.png)


Starting the VM

```shell
VBoxHeadless -s $VM
```

Once you have configured the operating system, you can shutdown and eject the DVD.

```
VBoxManage storageattach $VM --storagectl "IDE Controller" --port 0 \
  --device 0 --type dvddrive --medium none
```

----

### Tips \#

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

