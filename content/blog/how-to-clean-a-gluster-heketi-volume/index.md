---
title: How to cleanup a Gluster Heketi volume
date: 2019-06-03
description: how to clean a gluster heketi environment
comments: true
tags:
  - gluster
  - heketi
  - storage
---

I'm using [gluster + heketi](https://github.com/gluster/gluster-kubernetes) in my current kubernetes cluster, with it I give a persistent storage service on demand, it is easy to use, robust and simple. I know others options to implement a simliar service, like a [rooks](https://rook.io) using ceph, however, gluster and heketi support my cluster very well.

One little problem with this approach is to do some operational tasks. For instance, it is needed to cleanup some volumes via heketi CLI. So, from time to time I need to do in my gluster volumes because for some reason, heketi volumes don't represent the same size when I compared with the gluster bricks sizes. It represent an inconsistency issue.

I'll show you how to clean up the heketi information to sincronize with gluster bricks information.


### Deleting the orphands bricks

Some bricks haven't the path correctly. So, you need to clean up using the `heketi` binary command (the server, not the client).

#### Steps

Scale down your heketi service

```bash
kubectl scale deployment -n gluster-heketi heketi --replicas=0
```

Find out your `heketidbstorage` mount point 

```bash
kubectl exec -it -n gluster-heketi heketi-5c88f4574d-jxkpl -- df -k
``` 

Mount the gluster volume of `heketi.db`

```sh
sudo apt-get -y install glusterfs-client
sudo mount -t glusterfs 10.64.14.47:/heketidbstorage  /mnt
``` 
> `10.64.14.47` is one of my gluster servers

Download the heketi binary

```sh
wget https://github.com/heketi/heketi/releases/download/v9.0.0/heketi-v9.0.0.linux.amd64.tar.gz
cd heketi
sudo ./heketi db delete-bricks-with-empty-path --dbfile=/mnt/heketi.db --all
umount /mnt
```

Scale up your heketi service

```sh
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

