---
layout: single
title: Custom Alpine Server
tags: server VPN docker nextcloud
read_time: true

sidebar:
  title: "Cloud Server <br>with Office Software"
  nav: sidebar-esprimo
---

## Cloud Server with Office Software

***made around year 2020***


This 12 years old little computer was on its way to bin, but ended up to me. After a short rummaging through my elecronics storage (miscellanious boxes of all sorts of computer etc. junk I daren't throw away) I found a matchig (19 V, 4,74 A) adapter and power cord. The computer booted and I got to BIOS. According to settings the computer should've booted from a USB key. Actually the presence of a key froze the computer. Anyway it booted to the login screen of Windows Vista. Booting from CD ROM was also successful. At this point it seemed that what I had was a decent computer suitable for this project.

![](/assets/images/esprimo/Esprimo.png)

While troubleshooting the USB boot I noticed that the BIOS didn't save new settings. It was time to change the CMOS battery. Luckily I also had those in my electronics storage. While at it I also switched an old Linux hard drive to the Esprimo and the few years old Linux immediately ran on the new computer. Unfortunately the hard drive made awful noise and I remembered why it initially got transferred to storage before breaking while in use. Anyway I managed to verify that WiFi and LAN were working.

### Alpine Linux

I chose the OS [Alpine Linux](https://alpinelinux.org/) because
 - you can use it straight from a USB-key and save changes on the key. Easy to test and not save if you make a mistake or just roll back to previous version.
 - if Esprimo dies, I can just move the USB key to another computer.
 - I'll save Docker configurations on the key (containers can be saved and moved as-is).

 <div class="notice--warning">{{ notice-2 | markdownify }}
 <em>Note:</em> Alpine uses musl library, so Docker images like Collabora/CODE must use e.g. Ubuntu base.</div>
 <div class="notice--warning">{{ notice-2 | markdownify }}
<em>Note:</em> Docker + Collabora needs AUFS support in kernel, which Alpine doesn't have by default so custom kernel is a must.
</div>
### Custom AUFS5 kernel and custom Alpine image

I've made Debian, SystemRescue etc based custom images before, so I opted to make whole custom image instead of just couple of patched kernel packages.

I installed Alpine to a Virtual Machine to patch the kernel for AUFS using [these instructions ](http://fxlv.github.io/aufs-kernel-howto/) and built the missing packages to the kernel I compiled according to [this](https://wiki.alpinelinux.org/wiki/Creating_an_Alpine_package). ![](/assets/images/esprimo/VM1.png)

I made the image (3.11 and later 3.12) with [these instructions](https://wiki.alpinelinux.org/wiki/How_to_make_a_custom_ISO_image_with_mkimage).

When I installed the custom Alpine image on a USB key, I bumped into a strange problem: the key worked on other computers but not on Esprimo. Extlinux or default BIOS&EFI boot wouldn't work on Esprimo so I had to use older [manual installation](https://wiki.alpinelinux.org/wiki/Create_a_Bootable_Device) instead of Alpine setup-bootable script.

## Alpine Server

First I installed everything needed for remote administration ***SSH, firewall, DDNS and VPN***. Here's where the custom image came handy, because I already had most of my applications and configurations on the USB key.

### Dynamic DNS
I don't have a static IP so I need a Dynamic DNS Domain. Mine's from [NoIP](https://www.noip.com/). I installed Inadyn app to inform NoIP every time my IP changes. Alpine Linux didn't have a startup script for Inadyn, so I made one:
```shell
#!/sbin/openrc-run
    command=/usr/sbin/inadyn
    command_args="-f /etc/inadyn.conf \
            --cache-dir=/var/cache/inadyn \
            -p nobody"
    name="inadyn"
    depend() {
          need net
          }
```
### OpenVPN
At this point I could use my server from LAN. I still needed VPN to use it remotely e.g. from my mobile phone. I used EasyRSA method to install OpenVPN according to [these instructions](https://www.howtoforge.com/tutorial/how-to-install-openvpn-server-and-client-with-easy-rsa-3-on-centos-8/).

### SSHFS

Since my intention is mostly edit Docker config files and I prefer graphical editors, I use SSHFS. I [mount the server folder](https://www.digitalocean.com/community/tutorials/how-to-use-sshfs-to-mount-remote-file-systems-over-ssh) with the config files on my client computer that has the SSHFS app installed.

### ACF
ACF is Alpines own monitoring software also for remote monitoring. It uses its own mini_httpd web server. It's nice to peek what's going on on the server. I moved it to port 4443 leaving 443 free for the internet services I was going to install with Docker.
![](/assets/images/esprimo/ACF.png)

## Docker & docker-compose

[Instructions](https://wiki.alpinelinux.org/wiki/Docker)
This is why all the previous work had been done!  The Alpine Linux key really shone at this point: every time I messed something up, I just booted the server back to previous configuration.

I had some trouble with Dockremap but in the end I didn't need it. Just

```shell
#/etc/docker/json
"storage-driver": "aufs"
```
and partitions for the formatted Vista drive for Docker and the data itself (I don't fully  trust recovering data from Docker volume yet). Additions to /etc/fstab:
```shell
/dev/sda1 /var/lib/docker ext4    defaults  0 2
/dev/sda2 /data           ext4    defaults  0 2
```
After that just
```shell
sudo lbu ci
```
to save working configuration on the USB key. With
```shell
docker system prune -a --volumes
```
I can erase all the junk after failed configurations and start fresh as often as needed.

## Nextcloud & Collabora/CODE
I've had ownCloud and after that Nextcloud manually installed on Raspberry Pi for years. With docker-compose the whole install took only minutes (after findin out the working configuration) even on old Esprimo.

I copied configurations from the official [Nextcloud github](https://github.com/nextcloud/docker/tree/master/.examples/docker-compose/with-nginx-proxy/postgres/fpm) and edited them for my server.

![](/assets/images/esprimo/Cloud.png)

Nextcloud has the option to use built-in CODE server, but afaik it's slower and allows less concurrent users than separate Docker image. Besides I chose fpm-alpine version for my Nextcloud image so no glic library but musl. So separate Collabora/CODE image it was.

![](/assets/images/esprimo/Writer.png)

The cloud installation on Alpine server had a small hiccup with Office and synchronization that was fixed with
```shell
- OVERWRITEPROTOCOL=https
```
in Nextcloud's docker-compose.yml.

Works now in mobile phone browser too!

![](/assets/images/esprimo/Mobimpress.jpeg)
