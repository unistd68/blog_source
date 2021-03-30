---
title: 【原理】CentOS6-7安装MYSQL数据库
tags:
  - 数据库
  - MySQL
  - Linux
  - CentOS
categories: 原理
comments: true
img: https://blog.xtcgch.ink/img/动漫/pic-10.jpg
urlname: mysql-install
abbrlink: 59937
date: 2018-11-24 22:49:27
---

**摘要**：本文主要讲CentOS6和7中MYSQL的安装和配置相关的知识点。

<!--more-->

---
## 1. 下载mysql的repo源

CentOS7的yum源中默认是没有mysql的。为了解决这个问题，我们要先下载mysql的repo源。

```
wget http://repo.mysql.com/+rpm包
```

## 2、安装rpm包

```
sudo rpm -ivh+rpm包
```
安装了之后在`/etc/yum.repos.d/`文件夹下会有2个repo包：
1、`mysql-community.repo`
2、`mysql-community-source.repo`



## 3、安装mysql

```
sudo yum install mysql-server
```

## 4、启动mysql

```
service mysqld start
或
systemctl start mysqld.service
```

## 5、检查mysql 的运行状态

```
service mysqld status
或
systemctl status mysqld.service
```

## 6、修改临时密码

Mysql5.7默认安装之后root是有密码的。

### 6.1 获取MySQL的临时密码

为了加强安全性，MySQL5.7为root用户随机生成了一个密码，在`error log`中，关于`error log`的位置，如果安装的是`RPM`包，则默认是`/var/log/mysqld.log`。

只有启动过一次mysql才可以查看临时密码

查看临时密码：

```
grep 'temporary password' /var/log/mysqld.log
```

![mysql临时密码](./1507026839436.png)

### 6.2 登陆并修改密码

使用默认密码登录：

```
mysql -uroot -p
```

修改密码

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Abc9876543210.';
```
**注意：可能因为设置的密码安全性太低而会被警告，修改不成功！**


授权其他机器进行连接

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'Abc9876543210.' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```


**注意：这里的mypassword是其他机器连接时的密码。**


如果修改不成功，可能是密码的加密方式不正确

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Abc9876543210.' PASSWORD EXPIRE NEVER;
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Abc9876543210.';
FLUSH PRIVILEGES;

```

监听所有ip地址的连接

```
vim /etc/my.cnf
bind-address=0.0.0.0
```

## 7、防火墙开放3306端口

1、进入防火墙配置文件

```
sudo vim /etc/sysconfig/iptables
```

2、添加防火墙规则

```
-A INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
```

3、重启防火墙服务

```
sudo service iptables 
```