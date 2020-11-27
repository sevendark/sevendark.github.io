---
title: "PHP Version Update"
categories:
  - Tec
tags:
  - 网盘
  - PHP
toc: true
header:
  teaser: /assets/blog_images/php-logo.png
---
Update PHP Version, Update Apache2 PHP Version, Swich PHP version on Ubuntu

# Update PHP version

- Update php version
```sh
sudo apt update
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ondrej/php
```

- install php 7.4
```sh
sudo apt update
sudo apt-get install -y php7.4
```

# Swich PHP version

```sh
sudo update-alternatives --set php /usr/bin/php7.4
```

# Update Apache2 PHP version

- Install apache2 mods
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