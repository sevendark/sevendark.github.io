---
title: "Install Nginx On Ubuntu"
categories:
  - Tec
tags:
  - Nginx
toc: true
toc_label: "Outline"
toc_icon: "cog"
---

some thing about nginx: install, run 

## Install

install the required package
```sh
   sudo apt install curl gnupg2 ca-certificates lsb-release
```

set apt repository for nginx
```sh
   echo "deb http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list
```

import nginx signing key
```sh
   curl -fsSL https://nginx.org/keys/nginx_signing.key | sudo apt-key add -
```

verify key, output should contain `573B FD6B 3D8F BC64 1079 A6AB ABF5 BD82 7BD9 BF62`
```sh
   sudo apt-key fingerprint ABF5BD827BD9BF62
```

install nginx
```sh
   sudo apt update
   sudo apt install nginx
```

## File Path

### log file
error.log and access.log
- `/usr/local/nginx/logs`
- `/var/log/nginx`

### pid file
nginx.pid
- `/var/local/nginx/logs`
- `/var/run`

### config file
nginx.conf
- `/user/local/nginx/conf`
- `/etc/nginx`
- `/usr/local/etc/nginx`

## command

```sh
nginx -s reload
nginx -s quit
```