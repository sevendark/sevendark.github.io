---
title: "Minimal Mistakes踩坑记录"
categories:
  - Tec
---
Minimal Mistakes踩坑记录

* 第一坑： 如果是远程主题引用，切换语言为中文后，一定要拷贝`_data/ui-text.yml`文件到本地
* 第二坑： 如果使用algolia查询，第一行文字一定要是正常文字内容，不包括标题等特殊文字，否则js会报错
```js
Uncaught TypeError: Cannot read property 'value' of undefined
    at hitTemplate ((index):559)
```