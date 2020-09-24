---
title: "Linux命令, grep 和 diff"
categories:
  - Tec
tags:
  - Bash
  - GNU/Linux
toc: true
header:
  teaser: /assets/blog_images/Gnu-bash-logo.svg
---
在工作中慢慢发现Linux命令的熟悉真的可以大大提高自己的工作效率，所以打算以后有空就多学学Linux命令，这片博客就是一个开始，首先从一些常用的命令学起，希望大家也能从中学到一些东西😁

# Prepare Data File

```sh
$ echo "java" > aaa.txt
$ echo "php" >> aaa.txt
$ echo "java" > bbb.txt
```
# 常用命令

## grep

```sh
-x, 匹配整行
-f, 从文件中获取匹配值
-F, 使用换行分割匹配值
-v, 反向过滤
```

展示bbb.txt中包含的aaa.txt中的行
```sh
$ grep -x -F -f aaa.txt bbb.txt
java
```

展示aaa.txt中除去bbb.txt包含的行
```sh
$ grep -x -F -v -f bbb.txt aaa.txt
php
```

## diff

查看aaa.txt与bbb.txt的不同
```sh
$ diff -c aaa.txt bbb.txt
*** aaa.txt	Thu Jun  6 11:01:49 2019
--- bbb.txt	Thu Jun  6 11:02:10 2019
***************
*** 1,2 ****
  java
- php
--- 1 ----
```


