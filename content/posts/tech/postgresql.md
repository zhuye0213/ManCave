---
title: "Postgresql" #标题
date: 2024-08-20T11:48:18+08:00 #创建时间
lastmod: 2024-08-20T11:48:18+08:00 #更新时间
author: ["Jack Choo"] #作者
description: "Postgresql安装"
categories: 
- 数据库
tags: 
- 数据库 Postgresql
series: 
- 数据库
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "./images/postgresql.jpg" #图片路径
    caption: "封面图"
    alt: "图片迷路了"
---

## 1. 介绍

- 操作系统：AlmaLinux 9.4
- postgresql版本：16

## 2. 安装

### 2.1 添加postgresql源

~~~
dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-9-x86_64/pgdg-redhat-repo-latest.noarch.rpm
~~~

#禁用系统内置的pgsql(很多linux发行版都内置postgresql，而且版本不是最新的)
~~~
dnf -qy module disable postgresql
~~~

### 2.2 安装postgresql
contrib为异化功能包，如pg_dump、pg_restore等
~~~
dnf install -y postgresql16-server postgresql16-contrib
~~~
### 2.3 初始化数据库
~~~
/usr/pgsql-16/bin/postgresql-16-setup initdb
~~~
### 2.4 设置开机启动postgresql
~~~
systemctl enable postgresql-16
~~~
### 2.5 启动postgresql
~~~
systemctl start postgresql-16
~~~
### 2.6 防火墙

开放5432端口
~~~
firewall-cmd --zone=public --add-port=5432/tcp --permanent
firewall-cmd --reload
~~~

## 3. 配置

### 3.1 修改密码 

进入数据库(需要使用postgres用户才可以进入，并且无需密码)
~~~
sudo -u postgres psql postgres
~~~
#修改密码
~~~
ALTER USER postgres WITH PASSWORD 'Rgscasdzxc@123';
~~~
#退出
~~~
\q
~~~
### 3.2 配置远程访问
#### 3.2.1 修改 *pg_hba.conf* 文件
默认路径 */var/lib/pgsql/16/data/pg_hba.conf*
~~~
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
~~~
修改为
~~~
# IPv4 local connections:
host    all             all             0.0.0.0/0            	scram-sha-256
~~~

#### 3.2.2 修改 *postgresql.conf*

默认路径 */var/lib/pgsql/16/data/postgresql.conf*
~~~
#listen_addresses = 'localhost'
~~~
修改为
~~~
listen_addresses = '*'
~~~
#### 3.2.3 重启postgresql
~~~
systemctl restart postgresql-16
~~~

### 3.3 配置 *pg_stat_statements* 扩展

默认路径 */var/lib/pgsql/16/data/postgresql.conf*
#### 3.3.1 配置 *postgresql.conf*
~~~
#需要重启
shared_preload_libraries = 'pg_stat_statements'
~~~
#### 3.3.2 为数据库增加扩展
进入数据库执行
~~~
#切换数据库
\c databased_name
~~~
~~~
create extension pg_stat_statements;
~~~
查看扩展
~~~
SELECT * FROM pg_available_extensions WHERE name = 'pg_stat_statements';

        name        | default_version | installed_version |                                comment                                 
--------------------+-----------------+-------------------+------------------------------------------------------------------------
 pg_stat_statements | 1.10            | 1.10              | track planning and execution statistics of all SQL statements executed
~~~
## 4 配置prometheus监控

### 4.1 下载 postgres_exporter
~~~
wget https://github.com/prometheus-community/postgres_exporter/releases/download/v0.15.0/postgres_exporter-0.15.0.linux-amd64.tar.gz
~~~
### 4.2 解压
~~~
tar xvfz postgres_exporter-0.15.0.linux-amd64.tar.gz
~~~
### 4.3 创建服务
~~~
cat > /etc/systemd/system/postgres_exporter.service << "EOF"
[Unit]
Description=postgres_export
 
[Service]
WorkingDirectory=/opt/postgres_exporter-0.15.0.linux-amd64

Environment="DATA_SOURCE_URI=localhost:5432/postgres?sslmode=disable"
Environment="DATA_SOURCE_USER=postgres"
Environment="DATA_SOURCE_PASS=Rgscasdzxc@123"

ExecStart=/opt/postgres_exporter-0.15.0.linux-amd64/postgres_exporter
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF
~~~
### 4.4 其它
- 重载daemon
- 启动服务
- 配置开机启动
- 开放端口
~~~
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
firewall-cmd --zone=public --add-port=9187/tcp --permanent
firewall-cmd --reload
~~~

## 5. pgadmin4

未完待续 [官方传送门](https://www.pgadmin.org/download/pgadmin-4-rpm/)