---
title: "Hugo博客" #标题
date: 2023-12-08T10:53:49+08:00 #创建时间
lastmod: 2023-12-08T10:53:49+08:00 #更新时间
author: ["Jack Choo"] #作者
description: "使用hugo创建个人博客"
categories: 
- hugo
- hugo-papermod-7.0
tags: 
- hugo
- papermod
series: 
- hugo
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
disableShare: true # 底部不显示分享栏
#showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "./images/hugo.png" #图片路径
    caption: "封面图"
    alt: "图片迷路了"
---
## 新建文章
~~~
hugo new --kind post posts/tech/hugo.md
~~~
## 本地调试
~~~
 hugo server -D
~~~