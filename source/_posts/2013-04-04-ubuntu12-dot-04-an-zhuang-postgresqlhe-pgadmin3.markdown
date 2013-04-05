---
layout: post
title: "Ubuntu12.04 安装 PostgreSQL 和 PgAdmin3"
date: 2013-04-04 19:33
comments: true
categories: 
- Ubuntu{ubuntu}
- PostgreSQL{postgresql}
- PgAdmin3{pgadmin3}
- 数据库{database}
---


### 1.安装

安装：`sudo apt-get install postgresql postgresql-client postgresql-server-dev-all`

或者指定版本号：

	sudo apt-get install -y postgresql-9.1 postgresql-client-9.1 postgresql-contrib-9.1 \
	postgresql-server-dev-9.1

启动：`sudo /etc/init.d/postgresql start`

停止：`sudo /etc/init.d/postgresql stop`

重启：`sudo /etc/init.d/postgresql restart`

察看状态：`sudo /etc/init.d/postgresql status`

另外，也可以使用命令：`sudo service postgresql  [start/stop/restart/status]`

以上命令将把PostgreSQL安装到以下目录：

	/usr/lib/postgresql/9.1/        存放postgresql相关的二进制文件
	/usr/lib/postgresql/9.1/bin/    可执行文件 
	/usr/lib/postgresql/9.1/lib/    共享库文件 
	/etc/postgres/9.1/main/         存放postgresql配置文文件 
	/var/lib/postgresql/            postgres用户的主文件夹

<!-- more -->

### 2.配置管理员“postgres”，使用psql

PostgreSQL数据默认会创建一个linux用户postgres，需要修改操作系统“postgres”用户的密码为“postgres”：

1. 删除密码：`sudo passwd -d postgres  //passwd -d 是清空指定用户密码的意思`

2. 设置密码：`sudo -u postgres passwd  //或者 sudo su postgres -c passwd`

用sudo权限以用户postgres身份登陆PostgreSQL:

	sudo -u postgres psql [ -U postgres -h 127.0.0.1]   
	// 运行psql,后面[]中的内容可选，用于登录服务器，默认会登录本机；

或者：

`sudo su postgres -c psql template1（PostgreSQL的默认数据库是template1）`

运行 PostgreSQL 交互的终端程序， 叫 psql，它允许你交互地输入，编辑，和执行 SQL 命令。

启动 psql：`psql mydb`（以用户postgres登陆psql：`sudo su postgres -c psql`）

在 psql 里输入help命令，你会看到下面的提示信息：

	You are using psql, the command-line interface to PostgreSQL.
	Type:  \copyright for distribution terms
	          \h for help with SQL commands
	          \? for help with psql commands
	          \g or terminate with semicolon to execute query
	          \q to quit

修改PostgreSQL的管理员用户postgres的密码为“postgres”（同操作系统用户postgres的密码一样）：

`postgres=# ALTER USER postgres WITH PASSWORD 'postgres';`

查询当前使用的postgresql版本：`SELECT version()；`

查询大概年前日期：            `SELECT current_date;`

现在，我们就可以在数据库服务器上用 postgres帐号通过psql或者pgAdmin等等客户端操作数据库了
（暂时还不能远程访问）。

### 3.管理PostgreSQL用户

1 创建新用户pgsql，但不给建数据库的权限，有两中创建方式：

**一种是在外部命令行直接创建：**

  命令：`createuser [-a] [-A] [-d] [-D] [-e] [-P] [-h 主机名] [-p port] 用户名`
    参数说明：
    [-a]：允许创建其他用户，相当于创建一个超级用户；
    [-A]：不允许此用户创建其他用户；
    [-d]：允许此用户创建数据库；
    [-D]：不允许此用户创建数据库；
    [-e]：将执行过程显示到Shell上；
    [-P]：创建用户时，同时设置密码；
    [-h 主机名]：为某个主机上的Postgres创建用户；
    [-p port]：与-h参数一同使用，指定主机的端口。
   
例如：

`ivan@ubuntu:~$ sudo -u postgres createuser -D -P pgsql `

执行以后会看到如下提示：

	Enter password for new role: 
	Enter it again: 
	Shall the new role be a superuser? (y/n) n
	Shall the new role be allowed to create more new roles? (y/n) n
      
**另一种方式是登陆psql，通过SQL语句创建：**

首先登陆：

`ivan@ubuntu:~$ psql -U postgres -h 127.0.0.1`

然后创建：

	postgres=# create user “pgsql” with password ‘123456’ nocreatedb; 
	//用户名和数据库名称加上引号后才区分大小写，否则会自动转换成小写

2 删除用户：

外部：`sudo -u postgres dropuser pgsql`

内部：``


### 4.创建和删除数据库

同样有外部命令行和psql的SQL语句这两种创建方式。

创建数据库 mydb, 其拥有者是pgsql用户：

1  通过命令行创建（shell命令）：

创建数据库：`sudo -u postgres createdb mydb -O pgsql   // -O 设定所有者pgsql；`

删除数据库：`sudo -u postgres dropdb   mydb`

2  通过psql来创建(sql命令)：

首先利用psql登录PostgreSQL服务器:

	sudo -u postgres psql [ -U postgres -h 127.0.0.1]  
	//运行psql,后面[]中的内容可选，用于登录服务器，默认会登录本机；`

然后在psql程序中创建数据库（需要先创建好pgsql用户）: 

`create database “mydatabase” with owner=pgsql;`


### 5.数据库导入导出

**导出：**取得服务器 192.168.1.1 上 PostgreSQL中pgsql用户的mydb数据库，导出到当前目录下的 mydb.dump 文件：

`pg_dump -h 192.168.1.1 -U pgsql -Fc mydb > mydb.dump`

**导入：**将导出的文件 mydb.dump 导入到本地PostgreSQL数据库中pgsql用户下(需要提前创建pgsql用户），要求导入后的数据库名称为“mydb”

`pg_restore ./mydb.dump | psql -Upgsql -d mydb`

### 6.修改PostgresSQL数据库配置实现远程访问

`ivan@ubuntu:~$ sudo vim /etc/postgresql/9.1/main/postgresql.conf`

1.监听任何地址访问，修改连接权限

`#listen_addresses = ‘localhost’  改为 listen_addresses = ‘*’`

2.启用密码验证

`#password_encryption = on      改为   password_encryption = on`

3.可访问的用户ip段

`ivan@ubuntu:~$ sudo vim /etc/postgresql/9.1/main/pg_hba.conf`

并在文档末尾加上以下内容

	# to allow your client visiting postgresql server
	host all all 0.0.0.0 0.0.0.0 md5

解释一下最后一行：

	host表示允许的类型是主机； 
	第一个all是允许的数据库名字； 
	第二个all是允许的用户； 
	第一个0.0.0.0是允许访问的ip address； 
	第二个0.0.0.0是允许访问的subnet mask； 
	最后的md5表示密码的加密方式，如果将md5改成trust则可以让指定范围的主机数据库的时候不需要提供密码。
	关于 ip address 和 subnet mask ，你也可以修改为你的机器IP地址(如10.13.19.53)和子网掩码(如 255.255.255.255)，
	这样就只有你自己的主机可以远程访问数据库了。 如果要使用一个IP地址范围，只需要把子网掩码设置成合适的值，
	如果子网掩码设置成0.0.0.0，则所有主机均可以访问数据库（IP可以任意设定），
	如果将md5改成trust则可以让指定范围的主机访问指定的数据库的时候不需要提供密码。

4.重启PostgreSQL数据库

`ivan@ubuntu:~$ sudo /etc/init.d/postgresql restart`

### 7.安装postgresql数据库pgAdmin3客户端管理程序            

`ivan@ubuntu:~$ sudo apt-get install -y pgadmin3`

遇到问题：

1 安装过程中总是下载不了要安装的文件，更新源添加163源都无法解决，最后通过vpn翻墙以后可以正常安装。

2 安装以后pgAdmin3固定到启动器后不显示图标，解决方法，修改/usr/share/applications目录下的 pgAdmin III.desktop文件，将其中的 Icon值改为“/usr/share/pixmaps/pgadmin3.svg”，搞定。



