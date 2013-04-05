---
layout: post
title: "Ubuntu12.04 安装 JDK"
date: 2013-04-04 22:30
comments: true
categories: 
- Ubuntu{ubuntu}
- JDK{jdk}
- Java{java}
---

很多软件运行需要JRE环境，下面的方法用于在Ubuntu中安装JDK：

1. 首先下载JDK，点击[这里](http://www.oracle.com/technetwork/java/javase/downloads/jdk6downloads-1902814.html)下载（以“jdk-6u26-linux-i586.bin”为例）下载好以后放到安装位置（以`~/apps`目录为例）。

2. 设置安装文件权限： `chmod u+x ./jdk-6u26-linux-i586.bin`

3. 安装：`sudo -s ./jdk-6u26-linux-i586.bin`

4. 配置环境变量：

	`sudo gedit /etc/profile`

	在文件末尾写入以下内容：

		#set java environment    
		  
		JAVA_HOME=/home/ivan/apps/jdk1.6.0_26   
		  
		export JRE_HOME=/home/ivan/apps/jdk1.6.0_26/jre   
		  
		export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH   
		  
		export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH 

		#set java environment


5. 测试：

	重启terminal,执行： `java -version` ，输出版本信息，说明安装成功。

