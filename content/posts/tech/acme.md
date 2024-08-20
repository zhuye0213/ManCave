---
title: "Acme" #标题
date: 2024-08-20T11:48:35+08:00 #创建时间
lastmod: 2024-08-20T11:48:35+08:00 #更新时间
author: ["Jack Choo"] #作者
description: "SSL证书，自动续签"
categories: 
tags: 
- ssl
- https
- bash
series: 
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "./images/ssl.jpg" #图片路径
    caption: "封面图"
    alt: "图片迷路了"
---
下载脚本
## 1. 介绍
一个完全用Shell（Unix shell）语言编写的ACME协议客户端
[官网](https://github.com/acmesh-official/acme.sh)


## 2. 安装
~~~
curl https://get.acme.sh | sh -s email=xxxxxx@icloud.com
~~~
切换证书机构
~~~
./acme.sh --set-default-ca --server letsencrypt
~~~
注册账户，返回账户指纹
~~~
./acme.sh --register-account -m xxxxxx@icloud.com

ACCOUNT_THUMBPRINT='xxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
~~~

## 3. DNS验证

自动DNS（推荐）

阿里云添加用户，生成AccessKey和AccessSecret（过程略）

用户增加 *AliyunDNSFullAccess* 权限

[支持的DNS列表](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)

[阿里云DNS](https://github.com/acmesh-official/acme.sh/wiki/dnsapi#dns_ali)

导入环境变量，导入后脚本会保存到`~/.acme.sh/account.conf`文件中，不用担心重启环境变量丢失

~~~
export Ali_Key="xxxxxxxxxxxxxxxxx"
export Ali_Secret="xxxxxxxxxxxxxxxxxxx"
~~~

## 4. 证书
### 4.1 申请证书

签发的证书会保存在`~/.acme.sh/`目录下（不建议使用这里的证书）
~~~
./acme.sh --issue --dns dns_ali -d xxx.com -d *.xxx.com
~~~
### 4.2 安装证书

安装证书到指定目录，需要指定证书路径和重启命令

~~~
./acme.sh --install-cert -d xxx.center \
--key-file       /etc/nginx/ssl/xxx.center.key.pem  \
--fullchain-file /etc/nginx/ssl/xxx.center.cert.pem \
--reloadcmd     "nginx -s reload"
~~~

## 5. 自动续签

### 5.1 acme会加入定时任务，每60天自动续签，不需要手动续签
~~~
[root@proxy2 .acme.sh]# crontab -l

9 18 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
~~~
### 5.2 强制续签
续签一次
~~~
./acme.sh --cron --force
~~~
## 6. 软件升级

acme.sh 正在不断开发中，因此强烈建议使用最新的代码。

您可以将 acme.sh 更新到最新代码：
~~~
acme.sh --upgrade
~~~
您还可以启用自动升级：
~~~
acme.sh --upgrade --auto-upgrade
~~~
然后 acme.sh 将自动保持最新状态。

禁用自动升级：
~~~
acme.sh --upgrade --auto-upgrade 0
~~~