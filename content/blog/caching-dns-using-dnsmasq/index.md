---
title: Using dnsmasq with networkManager in Ubuntu
date: 2020-06-01
description: How to configure dnsmasq into Ubuntu running networkManager
comments: true
tags:
  - dnsmasq
  - networkmanager
---

## Overview 

Remote work increased since covid-19 appears. The VPN appliances turned crucial devices and employees are using more than ever their companies internal services. VPN connections often setting a custom DNS server, your internal DNS server, in order to resolve hostnames or services of the internal company.

Thus each dns request are going to use the same VPN network and these UDP packets are encrypt and transmit using your own Internet. This process will repeat for each simple dns record. So, all this round trip create an unnecessary communication overhead. To reduce this overhead, you could implement a dns cache of these requests as alternative to improve your network latency and speed up your internet browsing.

## Installing a Dnsmaq service Hero

> For ubuntu 

Uninstall `systemd-resolved` package. It isn't necessary to have.

```shell
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved
sudo apt-get remove systemd-resolved
```

Disabled `dnsmasq` in the NetworkManager configuration. Ensure to have the `dns=none` line

```ini
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

###  Install

Remove the current `/etc/resolvconf` file and install dnsmasq

```shell
unlink /etc/resolv.conf
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

