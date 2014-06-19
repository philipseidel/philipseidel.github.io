---
layout: post
title: Tomato Upgrade OpenVPN
categories:
- Posts
tags: []
status: publish
type: post
published: true
---
For a while now I have wanted upgrade my home router's [Tomato Firmware](http://www.polarcloud.com/tomato) to a newer version that supports [OpenVPN](http://openvpn.net/).  The goal of this exercise was to be able to connect to the Internet from my portable devices in a secure fashion from public hot-spots.  I currently do this by using a work VPN while on business trips but I wanted something in place that didn't require use of company resources.  I am currently running an older WRT54GL router so there are a number of firmwares to choose from but I wanted to continue using Tomato since I have had such a good experience with it so far.  I decided to upgrade to the [Shibby](http://tomato.groov.pl/) fork of Tomato and more specifically version [tomato-ND-1.28.5x-117-VPN](http://tomato.groov.pl/download/K24/build5x-117-EN/tomato-ND-1.28.5x-117-VPN.trx) since my router doesn't have a USB or SD slot required to make use of the additional features.

<!--more-->

Installation and configuration was a breeze and the steps are listed below.

###Upgrade Router Firmware

1. Download firmware and validate checksum
2. Backup current router configuration
3. Upgrade router firmware

###Configure OpenVPN Server

Download easyrsa

``` bash
git clone https://github.com/OpenVPN/easy-rsa.git
cd easy-rsa/easyrsa3
```
Create certificate authority

``` bash
./easyrsa init-pki
./easyrsa build-ca
```

Create and sign server certificate without a pass-phrase

```bash
./easyrsa gen-req openvpn-server nopass
./easyrsa sign-req server openvpn-server
```

Generate Diffie-Hellman parameters

```bash
./easyrsa gen-dh
```

Update router VPN configuration with ca certificate, server certificate, server key, and Diffie-Hellman parameters

Configure OpenVPN

* Use TCP port 443 so that you can get to the VPN when networks are locked down
* Direct clients to redirect Internet traffic in order to ensure that all web browsing, etc. goes over the secure tunnel
* Start VPN and validate status

###Configure OpenVPN Client

Create and sign a client certificate without a pass-phrase

```bash
./easyrsa gen-req openvpn-client nopass
./easyrsa sign-req client openvpn-client
```

Create a client configuration file and upload it to your device

```
# Specify that we are a client and that we
# will be pulling certain config file directives
# from the server.
client

# Use the same setting as you are using on
# the server.
# On most systems, the VPN will not function
# unless you partially or fully disable
# the firewall for the TUN/TAP interface.
dev tun

# Are we connecting to a TCP or
# UDP server?  Use the same setting as
# on the server.
proto tcp

# The hostname/IP and port of the server.
# You can have multiple remote entries
# to load balance between the servers.
remote openvpn-server.somedomain.com 443

# Keep trying indefinitely to resolve the
# host name of the OpenVPN server.  Very useful
# on machines which are not permanently connected
# to the internet such as laptops.
resolv-retry infinite

# Most clients don't need to bind to
# a specific local port number.
nobind

# Try to preserve some state across restarts.
persist-key
persist-tun

# SSL/TLS parms.
# See the server config file for more
# description.  It's best to use
# a separate .crt/.key file pair
# for each client.  A single ca
# file can be used for all clients.
ca ca.crt
cert client.crt
key client.key

# Enable compression on the VPN link.
# Don't enable this unless it is also
# enabled in the server config file.
comp-lzo

# Set log file verbosity.
verb 3
```

Install OpenVPN client on your device and import client configuration file, certificates, and key

Disconnect from wifi and connect to VPN to validate that LAN resources are accessible and all IP traffic is being routed through VPN