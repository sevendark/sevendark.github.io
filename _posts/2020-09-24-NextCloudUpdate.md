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

1. click `Download now`, just download `.zip` file use your computer.

![Detail](/assets/blog_images/fix2.png)

2. use `scp` copy `.zip` file to your server `/var/lib/nextcloud/data/updater-[random string]/downloads/`.

3. update `/var/lib/nextcloud/data/updater-[random string]/.step` change `start` to `end` 

4. `Open Updater` again, and click continue on top
