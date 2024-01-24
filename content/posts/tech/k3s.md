---
title: "K3s" #标题
date: 2024-01-24T20:25:23+08:00 #创建时间
lastmod: 2024-01-24T20:25:23+08:00 #更新时间
author: ["Jack Choo"] #作者
description: "k3s环境搭建"
categories: 
- k3s
- kubernetes
tags: 
- k3s
- kubernetes
series: 
- k3s
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "https://oss.rgsc.com.cn:29000/image/blog/k3s-rancher.png" #图片路径
    caption: "封面图"
    alt: "图片迷路了"
---
## 安装
~~~
curl –sfL \
     https://rancher-mirror.oss-cn-beijing.aliyuncs.com/k3s/k3s-install.sh | \
     INSTALL_K3S_MIRROR=cn sh -s - \
     --system-default-registry "registry.cn-hangzhou.aliyuncs.com"
~~~
## 配置镜像加速（TODO 配置阿里云加速待完善）
~~~
cat >> /etc/rancher/k3s/registries.yaml <<EOFmirrors:  "docker.io":    endpoint:      - "https://docker.mirrors.ustc.edu.cn" # 可根据需求替换 mirror 站点      - "https://registry-1.docker.io"EOFsystemctl restart k3s
~~~
## 创建管理用户
<https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md>
## 部署仪表盘
<https://kubernetes.io/zh-cn/docs/tasks/access-application-cluster/web-ui-dashboard/>
## 部署命令
访问需要魔法，可以把文件下载到本地，在上传到服务器，再执行
~~~
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
~~~
需要自己映射一个外部端口，注释部分修改2个地方
~~~
片段
kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
spec:
  type: NodePort #增加type: NodePort
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 31000 #增加nodePort:31000
  selector:
    k8s-app: kubernetes-dashboard
~~~
## 获取令牌命令
~~~
kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d
~~~
## 真相
![图片迷路了](https://oss.rgsc.com.cn:29000/image/blog/kubernetes-dashboard-main-page.png)

委婉待续~