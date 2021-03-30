---
title: 【原理】Centos7下编译boost
tags:
  - CentOS
  - C++
urlname: Centos7下编译boost
categories: 原理
comments: true
img: https://blog.xtcgch.ink/img/电影/pic-01.jpg
abbrlink: 21888
date: 2019-01-08 10:59:19
---

**摘要：**新手入门，保持更新！

<!--more-->

---

## 1、脑图

![{{暂无}}](./脑图.png)

---

## 2、boost的下载&解压

- [官网](https://www.boost.org/)：https://www.boost.org/
- 下载centos版本，如boost_1_69_0.tar.gz
- 解压：tar -zxvf boost_1_69_0.tar.gz

---

## 3、执行bootstrap.sh

```
cd boost_1_69_0
./bootstrap.sh
```

---

## 4、编译：./b2 参数

这时没有说编译安装，是因为在非root账户下，编译安装过程中产生的各种库文件放不进去/usr/local/目录下。

默认的编译参数保存在project-config.jam下，可用vim自己查看。

./b2有诸多的参数可以选择，如下（./b2 --help）：

![b2编译的参数选项](./b2编译参数.png)

先使用比较简单的参数：
```
./b2 --without-python stage debug
```

编译时间会比较久，编译成功后会生成boost目录（头文件）、stage/lib目录（动态库及静态库）。

---

## 5、头文件及库文件拷贝

将编译产生的boost目录拷贝至/usr/include/，将stage/lib/下的所有文件拷贝至/usr/lib64/下，如下：
```
sudo cp -rf boost /usr/include
sudo cp -rf stage/lib/* /usr/lib64
```

---

## 6、测试

测试文件test.cpp:

```
#include <iostream>
#include <string>
#include <boost/regex.hpp>

int main()
{
    std::string str = "192.168.1.1";

    boost::regex expression("([0-9]+).([0-9]+).([0-9]+)");
    boost::smatch what;

    if(boost::regex_search(str, what, expression))
    {
        std::cout << what.size() << std::endl;
        for(size_t i = 0; i < what.size(); i++)
        {
            if(what[i].matched)
            {
                std::cout << what[i] << std::endl;
            }
        }
    }

    return 0;
}
```

Makefile:

```
INC_DIR=-I/usr/include/
LIB_DIR=-L/usr/lib64/
LIB=-lboost_regex

CC=g++ -g
CFLAGS=-Wall
EXE=test

all:
    $(CC) $(CFLAGS) $(EXE).cpp -o $(EXE) $(INC_DIR) $(LIB_DIR) $(LIB)

clean:
    rm -rf *.o $(EXE)
```

---

## 7、Boost库“卸载”

因为我们只是简单地拷贝头文件和库文件，因此，我们直接删除Boost库头文件和库文件就可以了，如下：
```
sudo rm -rf /usr/include/boost
sudo rm /usr/lib64/*boost*
```