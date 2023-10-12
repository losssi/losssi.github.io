---
layout: single
tags: SSH dropbear
title: Passwordless SSH client on Termux with dropbear
excerpt: This is a bit trickier than using OpenSSH, because you can't use ssh-copy-id, you have to add the id on the server manually.
related: true
header:
  teaser: /assets/images/posts/Dropbear.jpg
---
![](/assets/images/posts/Dropbear.jpg)

**This is a bit trickier than using OpenSSH, because you can't use ssh-copy-id, you have to add the id on the server manually.**

***Time: 15min***

## 1 On Termux

1. Install dropbear if you dont already have it

   ```shell
   pkg install dropbear
   ```
2. Generate keys
```shell
dropbearkey -t rsa -f ~/.ssh/id_dropbear
```
This generates the key and displays it.

3. Copy the key (without the fingerprint part).

## 2 On server

1. Login to server with password
2. ```shell
nano ~/ssh/authorized_keys
```
Paste the key as last line on the file. If you don't already have the file, just create it.

## 3 Login to server without password
