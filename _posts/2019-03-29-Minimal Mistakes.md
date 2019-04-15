---
title: "Minimal Mistakes踩坑记录"
categories:
  - Tec
---
使用Minimal Mistakes和GitHub Pages搭建个人Blog时的踩坑记录，如：切换语言为简体中文，科学使用Algolia作为检索插件，
集成Utterances评论插件，根据Tag和Category进行分类。

## 踩坑
* 第一坑： 如果是远程主题引用，切换语言为中文后，一定要拷贝`_data/ui-text.yml`文件到本地
* 第二坑： 如果使用algolia查询，第一行文字一定要是正常文字内容，不包括标题等特殊文字，否则js会报错
```
Uncaught TypeError: Cannot read property 'value' of undefined
    at hitTemplate ((index):559)
```
* 第三坑：关于集成utterances评论插件，这个评论插件只会在生产环境下显示，所以在测试时请不要惊讶为什么它不展示，
顺带说一下这个插件是真的好用。
* 第四坑：根据时间、标签和类别访问博客时要拷贝原库中下列文件，否则在博客中点击tag或者category只能看到**404**😂
    * `minimal-mistakes/docs/_pages/category-archive.md`
    * `minimal-mistakes/docs/_pages/tag-archive.md`
    * `minimal-mistakes/docs/_pages/year-archive.md`

## 相关小知识：

- 超链接跳转新页面：`[Text](#link){:target="_blank"}`
- 超链接添加按钮样式与控制大小：`[Text](#link){: .btn .btn--primary .btn--x-large}`
- 小提示：`paragraph with {: .notice--success}`