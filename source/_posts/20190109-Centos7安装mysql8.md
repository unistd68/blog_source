---
title: 【原理】Centos7安装mysql8
tags:
  - CentOS
  - MySQL
urlname: Centos7安装mysql8
categories: 原理
comments: true
img: https://blog.xtcgch.ink/img/电影/pic-02.jpg
abbrlink: 25122
date: 2019-01-09 20:48:51
---

**摘要：**本文主要介绍在CentOS7下安装MySQL的方法。

<!--more-->

## 1、脑图

![{{title}}](./脑图.png)

---


**前言**:都是在root权限下进行安装和配置。


## [**小白**]方法一：使用repo源在线安装

(1）repo源下载和安装

- 下载一：https://dev.mysql.com/downloads/repo/yum/
- 下载二：wget https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
- 安装：rpm -ivh mysql80-community-release-el7-1.noarch.rpm

(2) 安装mysql-server
```
yum install mysql-server
```

至此，mysql已经安装完成！可以小高兴一会！

（3）mysql默认安装位置：

- 头文件：
- lib包：

（4）mysql服务的管理

- 启动：`service mysqld start`或`systemctl start mysql`
- 重启：`service mysqld restart`或`systemctl restart mysql`
- 关闭：`service mysqld stop`或`systemctl stop mysql`

（5）设置密码

- 进入：`mysql -uroot`,注意，不要使用-p
- 查看：`use mysql;` , `show tables;` , `describe user;` 
- 配置新密码：`update user set password=password('new password') where user='root';`注意，密码要使用password('')函数进行散列化
- 刷新缓存，让配置立即生效：`flush privileges;`


---

## [**进阶**]方法二：使用rpm包进行安装



---




## [**高**]方法三：使用压缩包进行安装





---

---

## 5、总结


