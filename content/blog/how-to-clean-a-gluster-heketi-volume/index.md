---
title: How to cleanup a Gluster Heketi volume
date: 2019-06-03
description: how to clean a gluster heketi environment
tags:
  - gluster
  - heketi
  - storage
---

Currently, I'm using [gluster + heketi](https://github.com/gluster/gluster-kubernetes) in my kubernetes cluster to give a persistent storage service on demand. I know that there are other options to implement simliar service, like a rooks using ceph. But, at the moment, gluster and heketi attend me very well.

One problem that I found with this approach is to do one operational task. It is to cleanup some volumes via heketi. So, from time to time I need to clean up my gluster volumes because for some reason, I don't know, the heketi volumes don't represent the same size compared with the gluster bricks. It is an inconsistency issue.

In the following lines I show you how to clean up the heketi information to sincronize with the gluster bricks information

### Deleting the orphands bricks

Some bricks haven't the path correctly. So, you need to clean up using the `heketi` binary command (the server, not the client).

#### Steps

Scale down your heketi service

```shell
kubectl scale deployment -n gluster-heketi heketi --replicas=0
```

Find out your `heketidbstorage` mount point 

```shell
kubectl exec -it -n gluster-heketi heketi-5c88f4574d-jxkpl -- df -k
``` 

Mount the gluster volume of `heketi.db`

```shell
sudo apt-get -y install glusterfs-client
sudo mount -t glusterfs 10.64.14.47:/heketidbstorage  /mnt
``` 
> `10.64.14.47` is one of my gluster servers

Download the heketi binary

```shell
wget https://github.com/heketi/heketi/releases/download/v9.0.0/heketi-v9.0.0.linux.amd64.tar.gz 
cd heketi
sudo ./heketi db delete-bricks-with-empty-path --dbfile=/mnt/heketi.db --all
umount /mnt
```

Scale up your heketi service

```shell
kubectl scale deployment -n gluster-heketi heketi --replicas=1
```

Resync your devices in all the nodes

```shell
kubectl exec -it -n gluster-heketi heketi-5c88f4574d-jxkpl -- \ 
  heketi-cli device resync 4c2a876a7829f3660e5a08cd1c0a5b1c
```
> You need to do that in all your devices


### Testing

Checking your heketi environment

```shell
kubectl exec -it -n gluster-heketi heketi-5c88f4574d-jxkpl heketi-cli topology info
``` 

---
Source: https://github.com/heketi/heketi/issues/926

