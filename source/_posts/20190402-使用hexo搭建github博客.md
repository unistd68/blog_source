title: 使用hexo搭建github博客
date: 2019-03-31 17:24:36
tags:
- hexo
- github
urlname: 使用hexo搭建github博客
categories: 其他
comments: true
img: https://blog.xtcgch.ink/img/萌图/pic-04.jpg

---

**摘要：**主要介绍利用hexo搭建个人的github博客！

<!--more-->

---

## <table><tr><td bgcolor=#C7C7C7>总体思路介绍</td></tr></table>

1、创建和配置github仓库

这一步主要有2个作用
- 存放博客相关文件
- 能够建立本地git仓库和远程github仓库的连接，便于远程管理。这个功能还需要配置本地git和其他依赖环境

2、配置本地环境

这一步主要是配置git工具和hexo博客的环境
- 配置git和依赖的工具，主要是nodejs
- 搭建hexo博客环境

3、配置博客主题

hexo支持很多博客主题，这些主题可以从github上面去clone和update，对于假装很喜欢写博客的我来说，用得爽才是最好的

4、进行一些个性化的改造

就像对待自己女朋友一样，要把她变成私有的，烙上自己独特的印记，最常用的有配置域名啦，对主题进行配置啦，模板配置啦，一键部署啦，等等

---


## <table><tr><td bgcolor=#C7C7C7>各个步骤说明</td></tr></table>


### <font color=#0000FF >创建github仓库</font>

1、创建一个public的repos

2、设置启用https

---


### <font color=#0000FF >部署本地环境</font>

1、安装git

git可以把本地的博客文件commit到远程仓库，即对博客进行更新

2、创建ssh的rsa公钥和密钥，配置连接远程仓库的参数

本地git和远程仓库进行连接时，首先需要进行安全验证，目前使用ssh的rsa key来保证，远程仓库使用public key，本地使用private key

其次需要配置连接到哪个用户，哪个仓库，这就需要显示地指定目标地址

一般有2中配置方式：直接配置用户环境、配置文件信息

其中配置用户环境比较简单，但是可配置性也比较差

配置文件信息稍微复杂点，但是可配置性比较高，适合个性化配置，见后面说明

3、安装nodejs

nodejs是管理hexo博客的主要工具，主要用到nodejs里面的npm，nodejs支持把markdown文件转为静态网页

同时npm也可以安装一些主题需要的模块


---


### <font color=#0000FF >配置博客主题</font>

hexo的默认主题是landscape，我用的是snippet

在根目录的_config.yml文件里面可以指定要使用的主题，然后在generate阶段会去themes目录下寻找对应的主题

配置博客主题主要有2方面：

- 在根目录的配置文件中指定主题
- 在主题目录里面进行个性化配置

第二点的坑比较多，在配置snippet时，我遇到的坑有：
- 要安装ejs 模版引擎 、 Less CSS预编译语言，安装项目的依赖
- 要对根目录的_config.yml结构进行更改，还要拷贝package.json到根目录等等
- 修改url地址，这点无疑是最坑的，无论修改成github仓库地址还是域名


---


### <font color=#0000FF >配置域名</font>

1、阿里云/腾讯/万网进行解析，添加CNAME记录即可

2、github仓库设置好域名，添加CNAME文件

---


### <font color=#0000FF >常见问题</font>

1、hexo d 时出现错误 Error: ERROR: Permission to

原因：登录账号和push到仓库的账号不一致，要么配置错误，要么是存在多账号的问题

解决方法：使用配置文件的方式来进行登录和push

2、hexo d后 ERROR Deployer not found: git

原因：未指定hexo的发布工具

解决方法:
```
npm install --save hexo-deployer-git
```

---

## <table><tr><td bgcolor=#C7C7C7>具体步骤</td></tr></table>

参考文章：
- [综合配置](https://blog.csdn.net/u011976726/article/details/78217467)
- [snippet主题](https://github.com/shenliyang/hexo-theme-snippet)
- [账号的配置](https://www.jianshu.com/p/0ee8b976ceab)

### <font color=#0000FF >1、创建和配置仓库</font>

略

### <font color=#0000FF >2、安装和配置git</font>

安装：略

**创建SSH KEY**

```
cd ~/.ssh/
ssh-keygen -t rsa -f  ~/.ssh/abc_rsa -C "yourmail@xxx.com" //abc为自定义名，常用github登录名
```


**配置git连接到github仓库**


配置文件 - 配置多个账号时要根据邮箱创建不同的SSH KEY
```
cd ~/.ssh/
touch config
# 账号名 abc
Host abc.github.com # abc为github的登录账号名
 HostName github.com
 User git
 IdentityFile ~/.ssh/abc_rsa
```

原理分析:

1. ssh 客户端是通过类似 git@abc.github.com:abc/blog.git 的地址来识别使用本地的哪个私钥的，(abc是github登录名，blog是仓库名)
2. 如果所有账号的 User 和 Host 都为 git 和 github.com，那么就只能使用一个私钥。
所以要对User 和 Host 进行配置，让每个账号使用自己的 Host，每个 Host 的域名做 CNAME 解析到 github.com，
如上面配置中的Host abc.github.com。
3. 配置了别名之后，新的地址就是git@abc.github.com:abc/blog.git。
这样 ssh 在连接时就可以区别不同的账号了。

**github仓库添加SSH KEY**

打开你的github主页，进入个人设置 -> SSH and GPG keys -> New SSH key：
- title：
- 内容：填abc_rsa.pub里面的公钥

**在.ssh根目录下, 清空本地的 SSH 缓存，添加新的 SSH 密钥 到 SSH agent中**

添加：

```
cd ~/.ssh
ssh-add -D
ssh-add abc_rsa
```

验证新秘钥已经添加成功：

```
ssh-add -l
```

使用环境变量配置时，输入指令, 验证配置是否成功:

```
ssh -T git@github.com
```

使用配置文件时，输入指令, 验证配置是否成功:

```
ssh -T git@abc.github.com
```

进入各自项目文件夹，单独设置用户名/邮箱

- 注意：如果有多个账号，建议使用局部的。
```
# 取消全局 用户名/邮箱 配置
git config –global –unset user.name
git config –global –unset user.email

# 单独设置每个repo 用户名/邮箱
git config user.name "用户名"  //在config后加上--global即全局
git config user.email "邮箱"
```

- 查看设置是否成功
```
git config --list
```

- 在 hexo 配置文件_config.yml修改git地址

```
deploy:
  type: git
  repository: git@abc.github.com:abc/blog.github.io.git
  branch: master
```

### <font color=#0000FF >3、安装和配置git</font>

1. 前提条件：安装nodejs
   - 配置淘宝镜像：pm install -g cnpm --registry=https://registry.npm.taobao.org
   - 安装:npm install -g hexo-cli
   - 查看：hexo -version

2. 初始化hexo框架
   - 进入文件夹：hexo init
   - 启动Hexo服务：hexo s
   - 查看效果：localhost:4000
3. 设置bat快捷命令文件
   
新建：
```
@echo off
echo Wait a minute ...
D: && cd D:\github_workplace\blog && hexo n new
echo Finished !
start D:\github_workplace\blog\source\_posts
pause
```

发布：
```
@echo off
echo Wait a minute ...
D: && cd D:\github_workplace\blog && hexo clean && hexo g && hexo d
echo Finished !
pause
```

### <font color=#0000FF >4、域名</font>

1. 在本地根目录的source文件夹下创建CNAME文件，输入域名，如：blog.abc.com

2. github仓库的setting/GitHub Pages/Custom domain一栏输入域名

3. 在阿里云/腾讯/万网添加域名解析记录CNAME，主机记录为blog，记录值是abc.github.io

**温馨提示**：配置域名后，几乎可以立即使用http的方式进行访问，但如果要启用https，则可以泡一杯咖啡，耐心等待5分钟，等待https证书的传输等过程完成，然后就可以进行访问了

---


## <table><tr><td bgcolor=#C7C7C7>snippet主题设置</td></tr></table>

唉，坑

---

## <table><tr><td bgcolor=#C7C7C7>扩展：个性化的设置</td></tr></table>

### <font color=#0000FF >1、迁移到另一台电脑或重装系统后的配置</font>