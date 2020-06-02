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

### Prerequisites

Uninstall `systemd-resolved` package. It isn't necessary to have.

```shell
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved
sudo apt-get remove systemd-resolved
```

Disabled `dnsmasq` in the NetworkManager configuration. Ensure to have the `dns=none` line

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

Remove the current `/etc/resolvconf` file
```shell
unlink /etc/resolv.conf
```

Install dnsmasq
```shell
sudo apt-get install dnsmasq
```

Config your upstream dns
```shell
echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.dnsmaq
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.dnsmaq
```

Config your `dnsmasq.conf` file. Use a cache-size parameter:
```shell
sudo tee /etc/dnsmasq.conf << EOF
listen-address=127.0.0.1
resolv-file=/etc/resolv.dnsmasq
cache-size=2048
EOF
```

Use the dnsmasq as main dns provider
```
echo "nameserver 127.0.0.1" | sudo tee /etc/resolv.conf
```

Restart dnsmasq service
```shell
systemctl restart dnsmasq
systemctl enable dnsmasq
```

## Test

You should get `0 msec` in the next dns query.

```shell
$ dig www.google.com | grep "Query time"
;; Query time: 0 msec
$
```

## References

https://vdemir.github.io/app/2018/06/16/Dnsmasq.html
https://fedoramagazine.org/using-the-networkmanagers-dnsmasq-plugin/

