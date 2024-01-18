---
layout: single
tags:
  - traefik
  - docker
  - let's encrypt
title: Install Traefik in Docker with Let's Encrypt
excerpt:
related: true
comments: true
header:
  teaser: /assets/images/posts/traefik.png
---
![](/assets/images/posts/traefik.png)
## How to Traefik with Docker and Let's Encrypt

**Prerequisites:**
 - domain accessible from the internet
 - installed Docker

## 1 Create Docker network
```shell
docker network create traefik-proxy
```

## 2 Create user/password for Traefik Dashboard
```shell
sudo apt-get install -y apache2-utils
echo $(htpasswd -nb username mystrongpassword) | sed -e s/\\$/\\$\\$/g
```
## 3 Create directory for Traefik and configs
```shell
mkdir -p /absolute/path/to/traefik/conf
cd /absolute/path/to/traefik
```
## 4 Create acme.json for Let's Encrypt
Make sure you're in your Traefik directory.

```shell
touch acme.json
chmod 600 acme.json
```
## 5 Create the yaml-files
```shell
traefik
    ├── acme.json
    ├── conf
    ├── docker-compose.yml
    └── traefik.yml
```

Basic docker-compose.yml for Traefik v2
```yaml
#docker-compose.yml
version: "3.3"

services:
  traefik:
    container_name: traefik
    image: "traefik:latest"
    restart: always
    networks:
      - traefik-proxy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./acme.json:/acme.json
      - /absolute/path/to/traefik/traefik.yml:/traefik.yml:ro # traefik configuration
      - /absolute/path/to/traefik/traefik.log:/traefik.log
      - /absolute/path/to/traefik/conf:/conf #this is where you put the non-docker services you want Traefik to handle
    labels:
      # Dashboard
      - "traefik.enable=true"
      - "traefik.http.routers.traefik.rule=Host(`yourdomain.net`)"
      - "traefik.http.routers.traefik.service=api@internal"
      - "traefik.http.routers.traefik.tls.certresolver=leresolver"
      - "traefik.http.routers.traefik.middlewares=authtraefik"
      - "traefik.http.middlewares.authtraefik.basicauth.users=username:mystrongpassword" # user/password
      - "traefik.http.services.dashboard.loadbalancer.server.port=8080"
networks:
  traefik-proxy:
    external: true
```
Basic Traefik configuration with Let's Encrypt
```yaml
#traefik.yml
global:
  checkNewVersion: true
  sendAnonymousUsage: false
log:
  level: DEBUG
  filePath: /traefik.log
api:
  dashboard: true
  debug: true
entrypoints:
  http:
    address: ":80"
    http:
      redirections:
        entrypoint:
          to: https
          scheme: https
  https:
    address: ":443"
serversTransport:
  insecureSkipVerify: true
providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false
  file:
    directory: /conf
    watch: true
certificatesResolvers:
  leresolver:
    acme:
      email: "youraddress@mail.net"
      storage: acme.json    
      #httpChallenge:
       # entryPoint: "http"
      tlschallenge: true
```


## 6 Start your Traefik container
```shell
docker compose up -d
```
