---
title: "Linux SSH 免密登录" #标题
date: 2023-12-20T18:54:35+08:00 #创建时间
lastmod: 2023-12-20T18:54:35+08:00 #更新时间
author: ["Jack Choo"] #作者
description: "CentOS使用RSA公钥登录"
categories: 
- ssh
- centos
tags: 
- passwordless
series: 
- centos
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "https://oss.rgsc.com.cn:29000/image/blog/passwordless-cove.png" #图片路径
    caption: "封面图"
    alt: "图片迷路了"
---
## 1 生成密钥对
我这里使用的是xshell，也可以使用linux自带的程序
### 1.1 工具
![图片迷路了](https://oss.rgsc.com.cn:29000/image/blog/xshell-rsa-menu.png)
### 1.2 设置
类型rsa，密钥长度4096
![图片迷路了](https://oss.rgsc.com.cn:29000/image/blog/xshell-rsa-build1.png)
### 1.3 生成中
![图片迷路了](https://oss.rgsc.com.cn:29000/image/blog/xshell-rsa-build2.png)
### 1.4 获取私钥的密码
![图片迷路了](https://oss.rgsc.com.cn:29000/image/blog/xshell-rsa-build3.png)
### 1.5 公钥
![图片迷路了](https://oss.rgsc.com.cn:29000/image/blog/xshell-rsa-build4.png)
### 1.6 私钥
用户密钥管理里面可以导出私钥，需要输入刚才的设置的密码
## 2 CentOS配置公钥
### 2.1 拷贝公钥到authorized_keys文件
将公钥文件上传到服务器，然后执行命令，authorized_keys文件不存在就创建
文件权限配置为600
```
cat ../id_rsa_4096.pub  >/root/.ssh/authorized_keys

chmod 600 authorized_keys
```
### 2.2 修改ssh配置
```
vi /etc/ssh/sshd_config
```
修改以下内容
```
 ...
 ...
 #密钥登录
 PubkeyAuthentication yes
 #禁止密码远程登录
 PasswordAuthentication no
 #密钥登录配置文件路径
 AuthorizedKeysFile      .ssh/authorized_keys
 ...
 ...
```
```
systemctl restart sshd
```
## 3 真相
![图片迷路了](https://oss.rgsc.com.cn:29000/image/blog/xshell-public-key-login.png)


