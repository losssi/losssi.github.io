---
layout: single
tags: VPN
title: Install Wireguard Client
excerpt: Wireguard server is already installed. Server and client are Debian or Ubuntu based.
related: true
header:
  teaser: /assets/images/posts/icon_wireguard.svg
---
![](/assets/images/posts/icon_wireguard.svg)

***Time: 20 min***

Wireguard server is already installed. Server and client are Debian or Ubuntu based.

## On client computer as root

1 Install packages

```shell
apt install wireguard resolvconf
```
2 Make keys and display them for copying
```shell
cd /etc/wireguard
wg genkey | tee privatekey | wg pubkey > publickey
cat pubkey
```
3 Write the configuration file and copy client private key and server public key to it.
```shell
#/etc/wireguard/wg0.conf

[Interface]
Address = 10.100.43.5/32
PrivateKey =
DNS = 8.8.8.8

[Peer]
PublicKey =
AllowedIPs = 10.100.43.0/24
Endpoint = IP:55820
PersistentKeepalive = 20
```
Peer is the server and IP can be servers URL. Modify the address space and port to yours.

4 If you have UFW firewall enabled:

```shell
ufw allow 55820/udp
```

Modify the port to yours.

5 Start Wireguard at startup

```shell
systemctl enable wg-quick@wg0.service
```
If you're using servers URL you might need to enable a couple of other services too:

```shell
systemctl enable systemd-networkd.service
systemctl enable systemd-networkd-wait-online.service
```

## On server as root

1 Edit the config file: Add the lines below **in the end of the file** and paste the public key after *PublicKey =*
```shell
#/etc/wireguard/wg0.conf

...

[Peer]
## client VPN public key ##
PublicKey =
## client VPN IP address (note  the /32 subnet) ##
AllowedIPs = 10.100.43.8/32
```

Again modify the address space to yours.

2 Restart Wireguard
```shell
wg-quick down wg0 && wg-quick up wg0
```
