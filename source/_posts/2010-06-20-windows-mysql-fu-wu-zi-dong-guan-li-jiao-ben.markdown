---
layout: post
title: "Windows MySQL 服务自动管理脚本"
date: 2010-06-20 00:09
comments: true
categories: 
- MySQL{mysql}
- 数据库{database}

---

脚本说明：在开发过程中由于经常需要启动或停止MySQL服务，非常麻烦，于是编写了这个简单的批处理脚本，自动判断MySQL服务如果是启动状态则停止该服务，如果未启动，则启动。

{% codeblock 脚本名称：mysql server.bat lang:bat %}
@echo off
color b0 
title 启动/关闭 MySQL服务
tasklist /nh| @findstr /i "mysqld-nt.exe"
set  dyw=%ERRORLEVEL%

if %dyw% == 1 (
  net start mysql
) else (
  net stop mysql 
)
ping -n 3 127.0.0.1>nul
exit
{% endcodeblock %}