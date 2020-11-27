---
title: "NextCloud Update"
categories:
  - Tec
tags:
  - 网盘
toc: true
header:
  teaser: /assets/blog_images/Nextcloud.png
---
Nextcloud Update And issues, "Step 4 is currently in process. Please reload this page later","a padding to disable MSIE and Chrome friendly error page","504 Gateway Time-out"

# Common Update way

1. open settings

![Settings](/assets/blog_images/fix1.png)

2. click `Open Updater`

![Detail](/assets/blog_images/fix2.png)

# Fix stuck on step 4

- click `Download now`, just download `.zip` file use your computer.

![Detail](/assets/blog_images/fix2.png)

- use `scp` copy `.zip` file to your server `/var/lib/nextcloud/data/updater-[random string]/downloads/`.

- update `/var/lib/nextcloud/data/updater-[random string]/.step` change `start` to `end` 

- `Open Updater` again, and click continue on top

# update apache2 php version

- update php version

```sh
sudo apt update
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ondrej/php
```

-- install php 7.4
```sh
sudo apt update
sudo apt-get install -y php7.4
```

-- Install apache2 mods

```sh
sudo apt update
sudo apt-get install -y libapache2-mod-php7.4
```

- Enter apache2 mods directory

```sh
cd /etc/apache2/mods-enabled
```

- Check current php vesion
```sh
ls -l |grep php
```

- Remove `php[version].conf` and `php[version].load` file

- Create link file for new php version

```sh
ln -s ../mods-enabled/php[version].conf php[version].conf 
ln -s ../mods-enabled/php[version].load php[version].load 
```

- Restart apache2

```sh
sudo systemctl restart apache2
```


> <https://websiteforstudents.com/install-php-8-0-on-ubuntu-20-04-18-04/>