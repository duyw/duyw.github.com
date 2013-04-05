---
layout: post
title: "Ubuntu12.04 安装 MySQL"
date: 2013-04-05 20:15
comments: true
categories: 
- Ubuntu{ubuntu}
- MySQL{mysql}
- 数据库{database}
---


### 第一步：卸载mysql

```
sudo apt-get autoremove --purge mysql-server-5.0
sudo apt-get remove mysql-server
sudo apt-get autoremove mysql-server
sudo apt-get remove mysql-common (非常重要)
```

### 第二步：清理残留数据

```
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

### 第三步：安装 mysql

```
sudo apt-get install mysql-server
sudo apt-get install mysql-client
sudo apt-get install php5-mysql(安装php5-mysql 是将php和mysql连接起来 )
```

一旦安装完成，MySQL 服务器应该自动启动。您可以在终端提示符后运行以下命令来检查 MySQL 服务器是否正在运行：

```
sudo netstat -tap | grep mysql
```

当您运行该命令时，您可以看到类似下面的行：

```
tcp 0 0 localhost.localdomain:mysql *:* LISTEN -
```

如果服务器不能正常运行，您可以通过下列命令启动它：

```
sudo /etc/init.d/mysql restart
```

### 第四步：配置管理员密码

进入mysql

```
$mysql -uroot -p 管理员密码
```

配置 MySQL 的管理员密码：

```
sudo mysqladmin -u root password newpassword
```
