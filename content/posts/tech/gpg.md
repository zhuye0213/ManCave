---
title: "GPG" #标题
date: 2024-01-05T14:22:56+08:00 #创建时间
lastmod: 2024-01-05T14:22:56+08:00 #更新时间
author: ["Jack Choo"] #作者
description: "一个开源免费PGP加密软件"
categories: 
- GPG
tags: 
- GPG
- 加密
series: 
- GPG
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "https://oss.rgsc.com.cn:29000/image/blog/oldlock.jpg" #图片路径
    caption: "封面图"
    alt: "图片迷路了"

---

## 1. 介绍
### 1.1 言论
*Arguing that you don't care about the right to privacy because you have nothing to hide is no different from saying you don't care about free speech because you have nothing to say.—Edward Snowden*

翻译: 争辩说你不在乎隐私权 因为你没有什么可隐瞒的 说你不在乎言论自由，因为你有 没什么好说的。——爱德华·斯诺登
### 1.2 一些传送门
GnuPG官方[传送门1](https://gnupg.org/)

Protecting Code Integrity with PGP(老外写的文章)[传送门2](https://www.linux.com/news/protecting-code-integrity-pgp-part-1-basic-pgp-concepts-and-tools/)

debian subkey(debian系统 关于如何使用子密钥)[传送门3](https://wiki.debian.org/Subkeys)

The GNU Privacy Handbook(一个英文的手册)[传送门4](https://www.gnupg.org/gph/en/manual/book1.html)

### 1.3 GPG是什么
机器翻译自官网

GnuPG 是 OpenPGP 标准的完整且免费的实现，作为 由 *[RFC4880](https://www.ietf.org/rfc/rfc4880.txt)* 定义（也称为 PGP）。GnuPG 允许您加密和 签署您的数据和通信;它具有多功能的密钥管理 系统，以及各种公钥的访问模块.


**说人话就是一个免费开源好用的加解密软件**


### 1.4 本文环境
- 系统:win11
- 软件:[gpg4win](https://www.gpg4win.org/)
## 2. 开始
### 2.1 生成密钥
#### 2.1.1 生成主密钥
~~~
gpg --expert --full-generate-key
~~~
- --expert 专家模式，更多的配置方法
- --full-generate-key 交互式设置
~~~
D:\gpg>gpg --expert --full-generate-key
gpg (GnuPG) 2.4.3; Copyright (C) 2023 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
   (9) ECC (sign and encrypt) *default*
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (13) Existing key
  (14) Existing key from card
Your selection? 8

Possible actions for this RSA key: Sign Certify Encrypt Authenticate
Current allowed actions: Sign Certify Encrypt

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? S

Possible actions for this RSA key: Sign Certify Encrypt Authenticate
Current allowed actions: Certify Encrypt

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? E

Possible actions for this RSA key: Sign Certify Encrypt Authenticate
Current allowed actions: Certify

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? Q
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) Y

GnuPG needs to construct a user ID to identify your key.

Real name: xxxxx
Email address: xxxxxx@xxx.com
Comment: xxxxxxxxxxxxx
You selected this USER-ID:
    "xxxxx (xxxx) <xxxxxx@xxx.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
public and secret key created and signed.

pub   rsa4096 2024-01-08 [C]
      xxxxxxxxxxxxxxxxxxxxxxxxxxxx
uid                      xxxx (xxxxxx) <xxxxxx@xxx.com>
~~~
#### 2.1.2 生成子密钥


查询key ID
~~~
gpg -k
~~~
~~~
[keyboxd]
---------
pub   rsa4096 2024-01-08 [C]
      xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx #这里是 key id
uid           [ultimate] xxxxx (xxxxxxxx) <xxxxxxx@xxxxx.com>
~~~
编辑密钥，依次添加[只签名][只加密][只验证]子密钥

~~~
gpg --expert --edit-key keyid
~~~
~~~
gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 11

Possible actions for this ECC key: Sign Authenticate
Current allowed actions: Sign

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? Q
Please select which elliptic curve you want:
   (1) Curve 25519 *default*
   (2) Curve 448
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection? 1
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 5Y
Key expires at 2029/1/6 13:35:11 �й���׼ʱ��
Is this correct? (y/N) Y
Really create? (y/N) Y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
~~~
添加完成后保存
~~~
gpg> save
~~~
查询
~~~
gpg --list-secret-key
~~~
其中
- C 证书 (主密钥特有 只能有一个)
- E 加密
- S 签名
- A 认证

椭圆曲线ed25519只能用于签名，加密需要用cv25519，具体请搜索椭圆曲线加密
~~~
[keyboxd]
---------
sec   rsa4096 2024-01-06 [C]
      xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
uid           [ultimate] xxxxxx <xxxxxxx@xxxxx.com>
ssb   cv25519 2024-01-05 [E] [expires: 2025-01-06]
ssb   ed25519 2024-01-05 [S] [expires: 2025-01-06]
ssb   ed25519 2024-01-05 [A] [expires: 2025-01-06]
~~~
#### 2.1.3 导出密钥
~~~
gpg -a -o file-name.key --[export|export-secret-key|export-secret-subkey] keyId
~~~
- -a 为 –armor 的简写，表示密钥以 ASCII 的形式输出，默认以二进制的形式输出；
- -o 为 –output 的简写，指定写入的文件，不加导出到屏幕输出

下面参数任选其一
- --export-key 导出公钥
- --export-secret-key 导出私钥
- --export-secret-subkey 导出全部子私钥

导出单个子密钥(gpg4win可以直接导出 用于验证的 openssh公钥 和 子私钥)
~~~
gpg --list-secret-keys --keyid-format=long
~~~
~~~
[keyboxd]
---------
sec#  rsa4096/xxxxxxxxxxxxxxxx 2024-01-08 [C]
      xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
uid                 [ultimate] xxxxx (xxxxxxxxxxxxx) <xxxxxxxxx@xxxxx.com>
ssb   ed25519/xxxxxxxxxxxxxxxx 2024-01-08 [S] [expires: 2025-01-01]
ssb   ed25519/xxxxxxxxxxxxxxxx 2024-01-08 [A] [expires: 2025-01-01]
ssb   cv25519/xxxxxxxxxxxxxxxx 2024-01-08 [E] [expires: 2025-01-01]
~~~
子密钥Keygrip后加叹号
~~~
gpg --output secret-subkeys --export-secret-subkeys SUBKEYID! [SUBKEYID! ..]
~~~

#### 2.1.4 导出吊销证书
~~~
gpg --gen-revoke keyId
~~~
#### 2.1.5 备份私钥，删除私钥

**以下三个步骤很重要 !!!**
1. 主私钥打印到纸上


2. 吊销证书拷贝到别的地方放好


3. 删除主私钥（平时只使用子私钥）

    ~~~
    gpg -K --with-keygrip
    ~~~
    找到主密钥的 keygrip ，记住。

    在 ~/.gnupg/private-keys-v1.d 下删除对应的主密钥文件。


主要私钥只有以前情况使用
You will need to use the primary keys only in exceptional circumstances, namely when you want to modify your own or someone else's key. More specifically, you need the primary private key:

- when you sign someone else's key or revoke an existing signature,
- when you add a new UID or mark an existing UID as primary,
- when you create a new subkey,
- when you revoke an existing UID or subkey,
- when you change the preferences (e.g., with setpref) on a UID,
- when you change the expiration date on your primary key or any of its subkey, or
- when you revoke or generate a revocation certificate for the complete key.

(Because each of these operation is done by adding a new self- or revocation signatures from the private primary key.)
详见1.2 [传送门3](https://wiki.debian.org/Subkeys)

翻译
只有在特殊情况下，即当您想要修改自己或其他人的密钥时，才需要使用主密钥。更具体地说，您需要主私钥：

- 当您对其他人的密钥进行签名或撤销现有签名时，
- 当您添加新 UID 或将现有 UID 标记为主要 UID 时，
- 创建新的子项时，
- 当您撤销现有 UID 或子项时，
- 当您更改 UID 上的首选项（例如，使用 setpref）时，
- 当您更改主密钥或其任何子项的到期日期时，或者
- 当您吊销或生成完整密钥的吊销证书时。

（因为这些操作中的每一个都是通过从私钥添加新的自签名或吊销签名来完成的。
#### 2.1.6 密钥真相
~~~
gpg --list-secret-key
~~~
注意sec后面的井号，标识主私钥不在这台电脑上
~~~
[keyboxd]
---------
sec#  rsa4096 2024-01-02 [C]
      xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
uid           [ultimate] xxxxxx (xxxxxxxx) <xxxxxxxxxx@xxxxx.com>
ssb   ed25519 2020-01-02 [S] [expires: 2020-01-06]
ssb   ed25519 2020-01-02 [A] [expires: 2020-01-06]
ssb   cv25519 2020-01-02 [E] [expires: 2020-01-06]
~~~
![图片迷路了](https://oss.rgsc.com.cn:29000/image/blog/pgp-sub-keys.png)

用gpg4win很多操作有界面。。。。

## 3 我的公钥
指纹:B1F76745CAA58A450DECA7B4F61D3BDF67A4142D

[下载公钥文件]( https://zhuye0213.github.io/ManCave/gpg-public-key.asc)
~~~
-----BEGIN PGP PUBLIC KEY BLOCK-----

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
7T4/cZVjC+P5Fcah
=pRMN
-----END PGP PUBLIC KEY BLOCK-----
~~~