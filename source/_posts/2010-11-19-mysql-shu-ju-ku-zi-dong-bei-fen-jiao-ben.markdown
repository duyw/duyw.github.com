---
layout: post
title: "MySQL 数据库自动备份脚本"
date: 2010-11-19 00:19
comments: true
categories: [数据库,备份,MySQL]
---



功能及说明：

1. 利用Linux的计划任务功能实现mysql数据库的定时备份，也可以手动执行 mysql_backup.sh

2. 备份文件名称按照日期存储

3. 可以删除旧的备份文件，只保留最近n天的备份数据源。

4. 备份过程都将记录到日志文件中，便于查看。

5. 提供三种备份方式，

   * mysqldump：将数据导出为sql文件

   * mysqlhotcopy：参看最后面的介绍

   * tar：先停止mysql服务然后将数据库文件打包

6. 如果已经做了当天的备份，重复执行不会覆盖原有备份，需要手动删除旧备份才可以重新生成备份文件

<!-- more -->
<br>

操作步骤：


### 第1步  编写定时备份脚本mysql_backup.sh，内容如下：

{% codeblock shell脚本: mysql_backup.sh lang:sh %}
#!/bin/bash 
#This is a ShellScript For Auto DB Backup 
#2010 -11 

#Setting 
#设置数据库名，数据库登录名，密码，备份路径，日志路径，数据文件位置， 
#以及备份方式 
#默认情况下备份方式是tar，还可以是mysqldump,mysqldotcopy 
#默认情况下，用root(空)登录mysql数据库，备份至/root/namexxxxx.tgz 
DBName = zc 
DBUser = root 
DBPasswd = root 
BackupPath = /root/zc_db_back/ 
LogFile = /root/zc_db_back/db.log 
DBPath = /var/lib/mysql/ 

#BackupMethod=mysqldump 
#BackupMethod=mysqlhotcopy 
BackupMethod = tar 
#Setting End 


NewFile = "$BackupPath""$DBName" _ $( date +%Y%m%d ).tgz 
DumpFile = "$BackupPath""$DBName" _ $( date +%Y%m%d )
OldFile = "$BackupPath""$DBName" _ $( date +%Y%m%d --date = '3 days ago' ).tgz 

echo "-------------------------------------------" >> $LogFile 
echo $( date + "%Y-%m-%d %H:%M:%S" ) >> $LogFile 
echo "--------------------------" >> $LogFile 
#Delete Old File 
if [ -f $OldFile ] 
then 
   rm -f $OldFile >> $LogFile 2>&1 
   echo "[$OldFile]Delete Old File Success!" >> $LogFile 
else 
   echo "[$OldFile]No Old Backup File!" >> $LogFile 
fi 

if [ -f $NewFile ] 
then 
   echo "[$NewFile]The Backup File is exists,Can't Backup!" >> $LogFile 
else 
   case $BackupMethod in 
   mysqldump )
      if [ -z $DBPasswd ] 
      then 
         mysqldump -u $DBUser --opt $DBName > $DumpFile 
      else 
         mysqldump -u $DBUser -p $DBPasswd --opt $DBName > $DumpFile 
      fi 
      tar czvf $NewFile $DumpFile >> $LogFile 2>&1 
      echo "[$NewFile]Backup Success!" >> $LogFile 
      rm -rf $DumpFile 
      ;;
   mysqlhotcopy )
      rm -rf $DumpFile 
      mkdir $DumpFile 
      if [ -z $DBPasswd ] 
      then 
         mysqlhotcopy -u $DBUser $DBName $DumpFile >> $LogFile 2>&1 
      else 
         mysqlhotcopy -u $DBUser -p $DBPasswd $DBName $DumpFile >> $LogFile 2>&1 
      fi 
      tar czvf $NewFile $DumpFile >> $LogFile 2>&1 
      echo "[$NewFile]Backup Success!" >> $LogFile 
      rm -rf $DumpFile 
      ;;
   * )
      service mysql stop >> $LogFile 2>&1 
      tar czvf $NewFile $DBPath$DBName >> $LogFile 2>&1 
      service mysql start >> $LogFile 2>&1 
      echo "[$NewFile]Backup Success!" >> $LogFile 
      ;;
   esac 
fi 

echo "-------------------------------------------" >> $LogFile 

{% endcodeblock %}


### 第2步 修改参数 

参数包括：“设置数据库名，数据库登录名，密码，备份路径，日志路径，数据文件位置，备份方式”

> 注意：备份路径必须是存在的路径

### 第3步  将脚本 mysql_backup.sh 设置为可执行

`chmod 755 mysql_backup.sh`

### 第4步 编辑定时任务，在root的crontab中，设置脚本执行周期(每天凌晨3点)

关闭服务：`service crond stop`

编辑任务列表： `crontab -e`

添加内容： `0 3 * * * /root/Desktop/mysql_backup.sh `

启动服务： `service crond start`

重建加载： `service crond reload`
  
### 5 数据恢复：

备份文件均为tar包，需要用tar解包，如果要恢复备份的数据，请先关闭mysql服务，

打包 ： `tar cvf + 打包后文件名 + 需打包文件夹名`

解包 ： `tar xvf + 包名`

将解出来的文件复制到mysql 数据库位置然后重新启动mysql服务即可。
    
<br>
-----------------------------------------------------------------------------
<br>

　　mysqlhotcopy是一个Perl脚本，最初由Tim Bunce编写并提供。它使用LOCK TABLES、FLUSH TABLES和cp或scp来快速备份数据库。它是备份数据库或单个表的最快的途径，但它只能运行在数据库目录所在的机器上。mysqlhotcopy只用于备份MyISAM。它运行在Unix和NetWare中。 

mysqlhotcopy与mysqldump比较：

1. 前者是一个快速文件意义上的COPY，后者是一个数据库端的SQL语句集合。

2. 前者只能运行在数据库目录所在的机器上，后者可以用在远程客户端，不过备份的文件还是保存在服务器上。

3. 相同的地方都是在线执行 LOCK TABLES 以及 UNLOCK TABLES

4. 前者恢复只需要COPY备份文件到源目录覆盖即可，后者需要导入SQL文件到原库中。(source 或 mysql < bakfile.sql)

5. 前者只适用于 MyISAM 引擎，而后则则可同时使用于MyISAM引擎和InodDB引擎.

6. 前者在使用前必须安装perl-DBD-mysql包,而后者则不需要.