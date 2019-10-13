---
title: How to compile a custom openconnect version
comments: true
title: Using a custom openconnect version for VPN
date: 2017-10-21
tags:
  - vpn
  - openconnect
---

My company use the Palo Alto Networks solution to offer a VPN service for their employees. Personally, I prefer to use Openvpn when I need to setup a VPN connection. By default, the OpenVpn can not implement the global protect protocol, so I need to compile from the [source](https://github.com/dlenski/openconnect/tree/globalprotect)

### Compiling OpenVPN to use Global Protect Protocol

#### Requeriments

Fedora users

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

Ubuntu users 

```shell
sudo apt-get install \
  build-essential gettext autoconf automake libproxy-dev \
  libxml2-dev libtool vpnc-scripts pkg-config \
  libgnutls-dev
```

#### Build and Install

After, you need to download the repo for dlenski github pages and compile it

```shell
git clone https://github.com/dlenski/openconnect.git
cd openconnect
git checkout globalprotect
./autogen.sh
./configure
make
sudo make install
```

Load the shared libraries

```shell
sudo ldconfig
```

#### Testing 

Finally, you can test it!

```shell
sudo /usr/local/sbin/openconnect --protocol=gp vpn.mycompany.com.br --dump -v
```

### Notes

[Here](https://serverfault.com/questions/584163/supplying-password-to-openconnect-started-via-start-stop-daemon) you have some ideas to create a init script.

