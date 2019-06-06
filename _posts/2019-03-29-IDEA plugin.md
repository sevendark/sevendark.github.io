---
title: "安利一个自己写的IDEA插件"
categories:
  - Tec
tags:
  - IDEA
toc: true
header:
  teaser: /assets/blog_images/idea.jpeg
---
在工作中遇到一些经常重复做的事，就专门写了一个IDEA小插件。
暂时只能实现三个功能，后期会慢慢添加。

## 功能1: play.libs.F.Option 转 java.util.Optional
有一些小伙伴还在用play framework2.3么，在2.5的时候play移除了自带的Option类
[Release25 Java Migration Guide](https://github.com/playframework/playframework/blob/master/documentation/manual/releases/release25/migration25/JavaMigration25.md){:target="_blank"}
这个插件可以实现替换所有`play.libs.F.Option`到`java.util.Optional`

## 功能2:把JOOQ代码转换成SQL
在IDEA中选中一段java 代码：
```java
select(TBLUSER.ID, TBLUSER.NAME)
.from(TBLUSER)
.join(TBLCLASS).on(TBLCLASS.ID.eq(TBLUSER.CLASS_ID))
.where(TBLUSER.ID.equal(user_id))
.and(TBLUSER.NAME.equal('王二蛋'))
.groupBy(TBLUSER.ID);
```

按下快捷键`ctrl+alt+g`或者选择顶部菜单中的`Tools->CodeTools->Transform Jooq to Sql`生成sql：
```sql
select
    TBLUSER.ID,
    TBLUSER.NAME  
from
    TBLUSER  
join
    TBLCLASS  
        on TBLCLASS.ID = TBLUSER.CLASS_ID   
where
    TBLUSER.ID = 'user_id'   
    and TBLUSER.NAME = "王二蛋"   
group by
    TBLUSER.ID 
```

## 功能3:把SQL转换成JOOQ代码（NEW）
打开方式：`Tools->CodeTools->SQL/Jooq Converter...`，
在同时小伙伴P的努力下，我们的插件多了一个界面，并且可以实现SQL转换成JOOQ代码
![页面](/assets/blog_images/gui.png)
感谢小伙伴的参与，我们的插件一定会越来越棒！

## 菜单位置
![菜单](/assets/blog_images/idea_plugin_menu.jpg)

有需要的小伙伴可以直接在IDEA的插件库中搜索：CodeTools

或者跳转IDEA的插件库网站搜索下载（国内可能无法访问）：[https://plugins.jetbrains.com/plugin/11467-aicoder](https://plugins.jetbrains.com/plugin/11467-aicoder){:target="_blank"}

Github地址：[https://github.com/sevendark/IDEACodeTools](https://github.com/sevendark/IDEACodeTools){:target="_blank"}