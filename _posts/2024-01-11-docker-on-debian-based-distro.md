---
layout: single
tags: docker
title: Install Docker Engine on a Debian-based distro
excerpt:
related: true
comments: true
header:
  teaser: /assets/images/posts/Docker.png
---
![](/assets/images/posts/Docker.png)
**How to install Docker Engine on a Debian-based (in this case MX Linux 23.1) distro**

***Time: 5 min***

First 2 steps were straightforward per [Docker docs](https://docs.docker.com/engine/install/debian/). The third step took a bit of googling to find out.

## 1 Set up Docker's apt repository.

```shell
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
## 2 Install the Docker packages.

```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 3 Fix the _Devices cgroup isn't mounted_ error.

```shell
sudo apt install cgroupfs-mount
sudo service docker start
```

## 4 Verify that the installation is successful by running the hello-world image:

```shell
sudo docker run hello-world
```
