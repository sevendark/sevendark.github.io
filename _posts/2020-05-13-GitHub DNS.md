---
title: "GitHub DNS (hosts)"
categories:
  - Tec
tags:
  - GitHub
  - DNS
header:
  teaser: /assets/blog_images/github-icon.png
---
从去年年底开始加载Github头像，还有以raw方式打开github上的文件都会失败。添加DNS记录到hosts文件即可

windows hosts 文件位置：`C:\Windows\System32\drivers\etc\hosts`
linux hosts 文件位置：`/etc/hosts`

```
140.82.113.4       github.com
140.82.112.4       gist.github.com
140.82.114.5       api.github.com
185.199.108.153    assets-cdn.github.com
199.232.68.133     raw.githubusercontent.com
199.232.68.133     gist.githubusercontent.com
199.232.68.133     cloud.githubusercontent.com
199.232.68.133     camo.githubusercontent.com
199.232.68.133     avatars0.githubusercontent.com
199.232.68.133     avatars1.githubusercontent.com
199.232.68.133     avatars2.githubusercontent.com
199.232.68.133     avatars3.githubusercontent.com
199.232.68.133     avatars4.githubusercontent.com
199.232.68.133     avatars5.githubusercontent.com
199.232.68.133     avatars6.githubusercontent.com
199.232.68.133     avatars7.githubusercontent.com
199.232.68.133     avatars8.githubusercontent.com
```