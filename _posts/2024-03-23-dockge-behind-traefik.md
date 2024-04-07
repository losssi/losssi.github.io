---
layout: single
tags:
  - traefik
  - docker
  - dockge
title: Install Dockge Docker manager behind Traefik
excerpt: How to install Dockge behind Traefik
related: true
comments: true
header:
  teaser: /assets/images/posts/Dockge.png
---
![](/assets/images/posts/Dockge.png)
## Installing Dockge Docker manager behind Traefik

**Prerequisites:**
 - domain accessible from the internet
 - installed Docker
 - Traefik container

**[https://github.com/louislam/dockge](https://github.com/louislam/dockge)**

## 1 Create directories that store your stacks and stores Dockge's stack
```shell
mkdir -p /opt/stacks /opt/dockge
cd /opt/dockge
```

## 2 Download the compose.yaml
```shell
curl https://raw.githubusercontent.com/louislam/dockge/master/compose.yaml --output compose.yaml
```

## 3 Add Traefik labels and networks
Compose.yml for Dockge docker manager
```yaml
#compose.yml
version: "3.8"
services:
  dockge:
    image: louislam/dockge:latest
    restart: unless-stopped
    ports:
      # Host Port : Container Port
      - 5001:5001
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./data:/app/data

      # If you want to use private registries, you need to share the auth file with Dockge:
      # - /root/.docker/:/root/.docker

      # Stacks Directory
      # ⚠️ READ IT CAREFULLY. If you did it wrong, your data could end up writing into a WRONG PATH.
      # ⚠️ 1. FULL path only. No relative path (MUST)
      # ⚠️ 2. Left Stacks Path === Right Stacks Path (MUST)
      - /opt/stacks:/opt/stacks
    environment:
      # Tell Dockge where is your stacks directory
      - DOCKGE_STACKS_DIR=/opt/stacks
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.dockge.entrypoints=https"
      - "traefik.http.routers.dockge.rule=Host(`dockge.yourdomain.net`)"
      - "traefik.http.routers.dockge.tls=true"
      - "traefik.http.routers.dockge.tls.certresolver=leresolver"
      - "traefik.http.services.dockge.loadbalancer.server.port=5001" # This should match the container port (to the right of the ":")
      - "traefik.docker.network=traefik-proxy"
    networks:
      - traefik-proxy
networks:
  traefik-proxy:
    external: true

```

## 4 Start your container
```shell
docker compose up -d
```
## 5 Access Dockge on https://dockge.yourdomain.net

Remember to move your existing docker compose folders to /opt/stacks and make sure the folder names are in **lowercase**.
