---
title: Using dnsmasq with networkManager in Ubuntu
date: 2020-06-01
description: How to configure dnsmasq into Ubuntu running networkManager
comments: true
tags:
  - dnsmasq
  - networkmanager
---

## Intro

In pandemic times, remote work increased VPN use in employees that need to consume internal services hosted in companies on-premise environment.

Normally, this VPN connection will be use the DNS server of your company in order to resolve all DNS names of your company and outside internet urls.

Each request to your DNS company will be use the same VPN network. Obviously, all the UDP packets will be encrypted and transmited using your Internet network. This process will repeat for each simple dns record. So, all this round trip create an unnecessary overhead in your communication.

Caching theses dns requests would be a good alternative to diminish your network latency and speed up your internet browsing.

## Install Dnsmasq 

### Prerequisites steps

Uninstall `systemd-resolved`

```shell
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved
sudo apt-get remove systemd-resolved
```

Disabled `dnsmasq` in NetworkManager. Ensure to have the `dns=none` line

```
[main]
dns=none
plugins=ifupdown,keyfile

[ifupdown]
managed=false

[device]
wifi.scan-rand-mac-address=no
```

Restart NetworkManager

```shell
sudo systemctl restart NetworkManager
```

###  Install dnsmasq


```shell
unlink /etc/resolv.conf
```

```shell
echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.conf
sudo apt-get install dnsmasq
```

#### Setup

```shell

```
