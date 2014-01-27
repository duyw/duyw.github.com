---
layout: post
title: "Ubuntu 读写 Windows 文件"
date: 2014-01-27 11:50
comments: true
categories: 
- Ubuntu{ubuntu}
---

前提：windows系统电脑和ubuntu在同一个局域网内。

在图形模式下：

1. 打开任意一个文件浏览器

2. 输入 Ctrl + l

3. 输入windows电脑地址：smb://192.168.71.34

4. 在弹出界面输入 windows 电脑的用户名、密码、工作组等信息

5. 搞定~！


在命令行模式下：
命令：`mount //192.168.10.56/share -o user=DOMIAN\\user,pass=passwd /mnt`

注意有两点

（1）domain这里是域名，并且要大写

（2）这里有两个\\,其中第一个是转义符，第二个是\
