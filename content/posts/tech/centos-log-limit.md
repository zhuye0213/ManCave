---
title: "Centos日志频率限制" #标题
date: 2024-03-08T13:26:21+08:00 #创建时间
lastmod: 2024-03-08T13:26:21+08:00 #更新时间
author: ["Jack Choo"] #作者
description: "取消centos日志频率限制"
categories: 
- centos
tags: 
- log
series: 
- centos
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "https://oss.rgsc.com.cn:29000/image/blog/centos.png" #图片路径
    caption: "封面图"
    alt: "图片迷路了"
---
## 查看
journalctl
~~~
journalctl --since today |grep Suppressed

3月 08 09:17:56 nginx.pack.localdomain systemd-journal[543]: Suppressed 1903 messages from /system.slice/datasync.service
3月 08 09:18:27 nginx.pack.localdomain systemd-journal[543]: Suppressed 2023 messages from /system.slice/datasync.service
3月 08 09:18:58 nginx.pack.localdomain systemd-journal[543]: Suppressed 2025 messages from /system.slice/datasync.service
3月 08 09:19:29 nginx.pack.localdomain systemd-journal[543]: Suppressed 1903 messages from /system.slice/datasync.service
3月 08 09:20:01 nginx.pack.localdomain systemd-journal[543]: Suppressed 1773 messages from /system.slice/datasync.service
~~~
syslog
~~~
cat /var/log/messages |grep -i rate-limiting
Mar  4 08:55:37 nginx rsyslogd: imjournal: 41 messages lost due to rate-limiting
Mar  4 09:06:08 nginx rsyslogd: imjournal: 46 messages lost due to rate-limiting
Mar  4 09:16:39 nginx rsyslogd: imjournal: 42 messages lost due to rate-limiting
Mar  4 14:36:05 nginx rsyslogd: imjournal: 3416 messages lost due to rate-limiting
Mar  4 14:46:35 nginx rsyslogd: imjournal: 1676 messages lost due to rate-limiting
Mar  6 10:17:46 nginx rsyslogd: imjournal: 861 messages lost due to rate-limiting
~~~
## 取消限制
修改/etc/systemd/journald.conf
~~~
RateLimitInterval=0
RateLimitBurst=0
~~~
修改/etc/rsyslog.conf，带注释部分为新增
~~~
#### MODULES ####

# The imjournal module bellow is now used as a message source instead of imuxsock.
$ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
$ModLoad imjournal # provides access to the systemd journal
#$ModLoad imklog # reads kernel messages (the same are read from journald)
#$ModLoad immark  # provides --MARK-- message capability

#取消日志写入限制
$imjournalRatelimitInterval 0
$imjournalRatelimitBurst 0
$SystemLogRateLimitInterval 0
$SystemLogRateLimitBurst 0

# Provides UDP syslog reception
#$ModLoad imudp
#$UDPServerRun 514
~~~
## 重启服务
~~~
systemctl restart systemd-journald
systemctl restart rsyslog
~~~