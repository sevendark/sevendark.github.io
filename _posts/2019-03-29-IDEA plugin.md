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
在工作中遇到一些经常重复做的事，就专门写了一个小插件。
暂时只能实现两个功能，后期会慢慢添加。

## 功能1: play.libs.F.Option 转 java.util.Optional
有一些小伙伴还在用play framework2.3么，在2.5的时候play移除了自带的Option类
[Release25 Java Migration Guide](https://github.com/playframework/playframework/blob/master/documentation/manual/releases/release25/migration25/JavaMigration25.md){:target="_blank"}
这个插件可以实现替换所有`play.libs.F.Option`到`java.util.Optional`

## 功能2:把JOOQ代码转换成SQL
java 代码
```java
select(TBLUSER.ID, TBLUSER.NAME)
.from(TBLUSER)
.join(TBLCLASS).on(TBLCLASS.ID.eq(TBLUSER.CLASS_ID))
.where(TBLUSER.ID.equal(user_id))
.and(TBLUSER.NAME.equal('王二蛋'))
.groupBy(TBLUSER.ID);
```
生成的sql
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

## 菜单位置
![菜单](/assets/blog_images/idea_plugin_menu.jpg)

有需要的小伙伴可以直接在IDEA的插件库中搜索：CodeTools

或者跳转IDEA的插件库网站搜索下载（国内可能无法访问）：[https://plugins.jetbrains.com/plugin/11467-aicoder](https://plugins.jetbrains.com/plugin/11467-aicoder){:target="_blank"}

Github地址：[https://github.com/sevendark/IDEACodeTools](https://github.com/sevendark/IDEACodeTools){:target="_blank"}