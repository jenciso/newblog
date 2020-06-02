---
title: Custom openconnect vpn setup for use in Palo Alto appliances
comments: true
date: 2017-10-21
copy2clipboard: true
tags:
  - vpn
  - openconnect
---

## Intro 

My company use the Palo Alto Networks appliance in order to offer a VPN service for us. Normally, I use openconnect or openvpn client when I need to setup some VPN connection, but it doesn't work with Palo Alto devices. Openconnect client can't implement the gloabl protect protocol, at least not now.

By default, the OpenVpn can not implement the global protect protocol and it is necessary to build from scratch using the code [source](https://github.com/dlenski/openconnect/tree/globalprotect)

## Build your openconnect to use Global Protect Protocol

The prerequisites is to install the libraries and packages needed to compile successfully the openconnect.

For Fedora users the packages needed are:

```shell
sudo dnf -f install libxml2-devel \
  zlib-devel \
  openssl-devel \
  pkg-config \
  p11-kit \
  libp11 \
  libproxy \
  trous \
  libproxy-devel \
  libstoken 
```

And for Ubuntu users:

```shell
sudo apt-get install \
  build-essential gettext autoconf automake libproxy-dev \
  libxml2-dev libtool vpnc-scripts pkg-config \
  libgnutls28-dev
```

Then, you need to download the git repository and make it:

```shell
git clone https://github.com/dlenski/openconnect.git
cd openconnect
git checkout globalprotect
./autogen.sh
./configure
make
sudo make install
```

It is necessary to load the shared libraries

```shell
sudo ldconfig
```

Finally, you can test it!

```shell
sudo /usr/local/sbin/openconnect --protocol=gp vpn.mycompany.com --dump -v
```

## Scripts to setup your credentials

Here we have are scripts examples to automate your personal credentials and execute your vpn connection automatically

Create a `mycompany-vpn.sh` file like this: 

```bash
#!/bin/bash

case $1 in
        disconnect|stop)
                sudo kill $(pidof openconnect) && echo "the openconnect was disconnected"
                sudo systemctl restart systemd-resolved.service && echo "DNS service was restarted"
                ;;
        status)
                pid=$(pidof openconnect) && echo "the openconnect is running | pidof: $pid" || echo "the openconnect is stopped"
                ;;
        *)
                echo "the openconnect is trying to connect..."
                echo $(cat ~/mycompany.ocvpn.pass) | sudo openconnect -b --config=/home/jenciso/mycompany.ocvpn
                ;;
esac
```

A file `~/mycompany.ocvpn.pass` with your password:

```
SuperSecretPass
```

And finally a `mycompany.ocvpn` with aditional configuration:

```
user=myuser
passwd-on-stdin
servercert=pin-sha256:axzKF1qMn0Ncyh8FIvSyg9SIRuSFfyn7ILk/20roII4=
protocol=gp
server=vpn.mycompany.com
```

Run your vpn and test it!

```bash
mycomapny-vpn.sh
mycomapny-vpn.sh status
mycomapny-vpn.sh disconnect
```

## References:

* [Here](https://serverfault.com/questions/584163/supplying-password-to-openconnect-started-via-start-stop-daemon) you have some ideas to create a init script.

