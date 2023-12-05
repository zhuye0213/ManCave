---
title: "Worldpress" #标题
date: 2023-12-05T13:07:26+08:00 #创建时间
lastmod: 2023-12-05T13:07:26+08:00 #更新时间
author: ["Jack Choo"] #作者
categories: 
- 技术
- wordpress
tags: 
- wordpress
- php
- linux
- nginx
- mysql
series: 
- 建站
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
comments: true #是否展示评论
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "https://oss.rgsc.com.cn:29000/image/blog/%E6%88%91%E4%BB%AC%E5%81%9A%E4%BA%8B%E5%B0%B1%E6%98%AF%E8%BF%99%E6%A0%B7.gif" #图片路径
    caption: "真相在此" #图片底部描述
    alt: "真相"
    relative: false
---
## MYSQL
安装MySql5.7,并配置uff8字符集
安装完成后创建数据库wordpress
```
yum install -y wget
wget https://repo.mysql.com//mysql80-community-release-el7-10.noarch.rpm
yum install -y yum-utils
yum localinstall -y mysql80-community-release-el7-10.noarch.rpm 
yum-config-manager --enable mysql57-community
yum-config-manager --disable mysql80-community
yum repolist enabled | grep mysql
yum install -y mysql-community-server
systemctl start mysqld
systemctl status mysqld
ss -natl |grep 3306
grep "password" /var/log/mysqld.log 
mysql -uroot -p'xeRZv7EBfH,s'
vi /etc/my.cnf
systemctl status mysqld
```
## 防火墙
开放端口
```
firewall-cmd --permanent --add-port=3306/tcp
firewall-cmd --permanent --add-port=443/tcp
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload
firewall-cmd --list-all
```
## 关闭selinux
```
vi /etc/selinux/config 
reboot 
getenforce 
```
# NGINX
### 安装NGINX
```
vi /etc/yum.repos.d/nginx.repo
yum-config-manager --enable nginx-stable
yum install -y  nginx
systemctl start nginx
systemctl enable nginx
systemctl status nginx
```
### 配置NGINX PHP
```
    location / {
        root   /usr/share/nginx/html;
```
### 增加index.php
```
        ...
        ...
        index  index.php index.html index.htm;
    }
```
### 启用PHP
```
    location ~ \.php$ {
        root           html;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/html/$fastcgi_script_name;
        include        fastcgi_params;
    }
```

# PHP7.4 
## 安装PHP
```
yum install -y epel-release
yum --enablerepo = remi install php74-php
rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
yum --enablerepo=remi install php74-php
yum --enablerepo=remi install php74-php php74-php-gd php74-php-xml php74-php-sockets php74-php-session php74-php-snmp php74-php-mysql php74-php-fpm
php74 -v
```
## 修改用户和组为nobody
```
vi /etc/opt/remi/php74/php-fpm.d/www.conf 

# 修改内容
user = nobody
group = nobody
```
## 启动php-fpm
```
systemctl start php74-php-fpm
systemctl status php74-php-fpm
systemctl enable php74-php-fpm
```
## 查看端口
```
ss -natl |grep 9000
ps aux |grep php-fpm
```
# 运行
网站首页或者https://host:port/readme.html里面有安装向导连接