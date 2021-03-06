---
title: "NextCloud 网盘搭建"
categories:
  - Tec
tags:
  - 网盘
toc: true
header:
  teaser: /assets/blog_images/Nextcloud.png
---
珍藏许久的东西总是无处安放，终有一日发现了NextCloud这个好东西，于是立马开干。这个开源的网盘有苹果安卓App，还有MAC，Windows桌面软件，简直太全乎了好嘛。为了搭建网盘，我在腾讯云上购买了2核8G内存100GB的服务器，高配是流畅体验的前提，么的办法，虽然贵，但是为了爽，忍了。后续更新：终究还是太贵了，用回了一个月47的1G+50G服务器，🤣

# snap 安装（目前snap只有旧版(16.0.0),不支持最新的18.0.0）

服务器配置：

```
云服务商 ： 腾讯云
系统：  Ubuntu 18.04.1 LTS
运存：  1G
内存：  50G
```

## 执行snap安装命令
```sh
sudo snap install nextcloud
```

## 启动服务
```sh
sudo snap start nextcloud
```

## 开启https模式
```sh
sudo nextcloud.enable-https lets-encrypt
```

## 设置启动端口
```sh
sudo snap set nextcloud ports.http=80
```

官方snap仓库地址：[https://github.com/nextcloud/nextcloud-snap](https://github.com/nextcloud/nextcloud-snap){:target="_blank"}

# 手动安装（支持最新的版本，但是比较繁琐）

## 安装mysql

```sh
sudo apt-get install mysql-server-5.7
```

## 配置编码

```
[mysqld]
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci
transaction_isolation = READ-COMMITTED
binlog_format = ROW
innodb_large_prefix=on
innodb_file_format=barracuda
innodb_file_per_table=1
```

## 配置root密码，并允许非sudo登陆
注意在这里登录时可以直接回车进入，初始安装没有密码
```sh
sudo mysql -u root -p
myqsl > ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your-password';
```

## 创建用户，仓库
如果需要远程登陆，替换`localhost`为`%`
```sh
mysql > create database nextcloud;
mysql > CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY '000000';
mysql > GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost';
mysql > flush privileges;
```

## 安装php，及其模块

```sh
sudo apt-get install php
sudo apt-get install php-dompdf unzip
sudo apt-get install php-gd php-json php-mysql php-curl php-mbstring
sudo apt-get install php-intl php-imagick php-xml php-zip
sudo apt-get install libapache2-mod-php php-bcmath php-gmp
```


## 配置php.ini
apache2 服务器使用的php.ini: `/etc/php/7.2/apache2/php.ini`
cloud cron jobs使用的php.ini: `/etc/php/7.2/cli/php.ini`

在apache2的php.ini中设置最大文件上传大小以及php内存限制
```
memory_limit = 512M
upload_max_filesize = 50M
```


## 安装reids
```sh
sudo apt-get install php-redis redis-server
sudo apt-get install php-apcu 
```


## 安装apache2
```sh
sudo apt-get install apache2
```

## 开启ssl，并设置证书
```sh
sudo wget https://dl.eff.org/certbot-auto && sudo chmod a+x certbot-auto
sudo ./certbot-auto --apache --agree-tos --rsa-key-size 4096 --email your@email.com --redirect -d your.domain.com
```

### 自动更新证书

> https://certbot.eff.org/lets-encrypt/ubuntubionic-apache

添加库
```sh
 sudo apt-get update
 sudo apt-get install software-properties-common
 sudo add-apt-repository universe
 sudo add-apt-repository ppa:certbot/certbot
 sudo apt-get update
```

安装certbot
```sh
 sudo apt-get install certbot python-certbot-apache
```

在apache中安装certbot
```sh
 sudo certbot --apache
```

配置证书自动更新
```sh
 sudo certbot renew --dry-run
```


## 配置apache2
开启`mod_headers`模块
```sh
sudo a2enmod headers
```

编辑`/etc/apache2/sites-available/000-default-le-ssl.conf`文件：
1. 在`ServerName`下面追加

```
<IfModule mod_headers.c>
    Header always set Strict-Transport-Security "max-age=15768000; preload"
</IfModule>
```

2. 更改`/var/www/html`为`/var/www/nextcloud`（如果你不想nextcloud直接作为根目录，需要在访问时使用：`https://host/nextcloud`）同样更改`/etc/apache2/sites-enabled/000-default.conf`文件中的`/var/www/html`。

编辑`/etc/apache2/apache2.conf`文件, 将所有的`AllowOverride None`更改为`AllowOverride All`

## 下载nextcloud压缩包

[https://nextcloud.com/install/](https://nextcloud.com/install/){:target="_blank"}

解压内容至`/var/www/`, 解压完成最终目录为`/var/www/nextcloud/...`

## 配置redis

在`/var/www/nextcloud/conf.d/config.php`中追加内容

```
  'memcache.local' => '\OC\Memcache\APCu',
  'memcache.locking' => '\OC\Memcache\Redis',
  'memcache.distributed' => '\OC\Memcache\Redis',
  'redis' => [
      'host' => 'localhost',
      'port' => 6379,
  ],
```

## 打开网站，配置mysql，创建admin等


# 附录

## migrate 旧的云盘至新云盘
直接拷贝`/var/www/nextcloud/data/your-user-name/files`的内容到新版本的目录中
然后执行

```sh
cd /var/www/nextcloud
sudo -u www-data php occ files:scan --all
```

## 数据库索引及cache更新

```sh
cd /var/www/nextcloud
sudo -u www-data php occ db:convert-filecache-bigint
sudo -u www-data php occ db:add-missing-indices
```

## 使用Nginx代理apache2中的Nextcloud

Nginx 代理配置
```
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    server_name cloud.example.com;

    client_max_body_size 0;
    underscores_in_headers on;

    location ~ {
        proxy_headers_hash_max_size 512;
        proxy_headers_hash_bucket_size 64;

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_buffering off;
        proxy_redirect off;
        proxy_max_temp_file_size 0;
        proxy_pass https://127.0.0.1:8443;
    }
}
```

Apache2 更改SSL默认监听端口为8443

`/etc/apache2/sites-enabled/000-default-le-ssl.conf`

```
<VirtualHost *:8443>
```

Nextcloud 配置服务器Host
`/var/www/nextcloud/config/config.php`
```
'trusted_domains' =>
array (
        0 => 'cloud.sevendark.cn',
        1 => '127.0.0.1:8000',
),
'overwritehost' => 'cloud.sevendark.cn',
'overwriteprotocol' => 'https',
'overwritewebroot' => '/',
'overwrite.cli.url' => 'https://cloud.sevendark.cn/',
'htaccess.RewriteBase' => '/',
'trusted_proxies' => ['127.0.0.1'],
```

参考：[https://breuer.dev/tutorial/Setup-NextCloud-FrontEnd-Nginx-SSL-Backend-Apache2.html](https://breuer.dev/tutorial/Setup-NextCloud-FrontEnd-Nginx-SSL-Backend-Apache2.html){:target="_blank"}


官方网站地址：[https://nextcloud.com/](https://nextcloud.com/){:target="_blank"}

