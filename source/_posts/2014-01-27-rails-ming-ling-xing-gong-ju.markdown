---
layout: post
title: "Rails 命令行工具"
date: 2014-01-27 11:58
comments: true
categories: 
- Rails{rails}
---

rails 命令行工具

整理一些不常用的但是很有用的命令行工具：

1 rails c --sandbox

在沙盒模式下启动rails命令行，再此所做的对数据的修改都会会滚。

2 rails runner "puts User.first.id"

在rails环境下执行ruby代码，无需启动应用服务。

3 rails dbconsole

启动数据库的命令行，无需数据用户名密码，支持MySQL, PostgreSQL, SQLite and SQLite3.

4 rails destroy

回滚 rails g 对项目的改变。

5 rake -T

查看所有的rake任务。

6 rake about

查看rails应用的环境，包括ruby版本，rails版本等。

7 rake notes

查看项目中的 TODO（待完成）、FIXME（待修复）、OPTIMIZE（待优化）

8 rails g 

用于生成脚手架、控制器、模型、数据库迁移任务等。  如果加一个 `-p` 参数，只输出生成的文件列表，而不是真的创建文件

参考资料：

http://guides.ruby-china.org/command_line.html[http://guides.ruby-china.org/command_line.html]



