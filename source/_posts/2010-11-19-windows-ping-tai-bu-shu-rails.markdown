---
layout: post
title: "windows 平台部署rails"
date: 2010-11-19 18:33
comments: true
categories: [Rails,部署,Mongrel]
---

　　公司一个现有项目是用 ruby 1.8.6 + rails1.2.6 做的，之前的客户服务器都是linux系统，这次客户服务器用的是windows，所以研究了一下部署方案，从网上各方搜集资料，最后配置成功，整理在此：

### 1. mongrel 安装

由于默认的gem版本为0.9.4，版本太旧了，无法运行gem install mongrel 命令，所以要先升级gem版本，首先下载 rubygem_update ,地址：http://rubyforge.org/frs/download.php/70695/rubygems-update-1.3.7.gem

下载后在cmd下cd到rubygems-update-1.3.7.gem所在的目录

安装命令：`gem install rubygems-update-1.3.7.gem`

接下来升级gem，命令：`update_rubygems`

安装完成后 查看gem版本，输入 `gem -v` 会显示 “1.3.7”，至此，升级成功。

安装mongrel，运行命令：`gem install mongrel`

安装完成后，在rail项目下运行 `ruby script/server` 启动时，会使用mongrel而不是webrake。

<!-- more -->

### 2. 安装mongrel_service

命令：`gem install mongrel_service`


### 3. 将mongrel设置为系统服务

命令：`mongrel_rails service::install -N zc0 -c d:\svn\zc_hn\code\zc -p 3000 -e development`

（zc0为服务名称，d:\svn\zc_hn\code\zc 为项目路径，development为开发模式，3000为端口）

执行后在系统服务中会增加一项名为zc的服务。

启动：`net start zc0`

关闭：`net stop zc0`

删除服务：先关闭，然后命令行执行：`mongrel_rails service::remove -N zc0`

如果需要安装多个mongrel实例，那么可以这样：

```
mongrel_rails service::install -N zc0 -c d:\svn\zc_hn\code\zc -p 3000 –e production
mongrel_rails service::install -N zc1 -c d:\svn\zc_hn\code\zc -p 3001 –e production
……
```

###　4. 安装、配置 apache

从Apache网站下载Windows版本的Apache2.2，运行安装。

安装后进入安装目录下修改 httpd.conf文件,将以下三个模块的注释去掉

```
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule proxy_http_module modules/mod_proxy_http.so

```

如果你希望对页面输出使用压缩，也需要取消如下模块的注释：

`LoadModule deflate_module modules/mod_deflate.so`

然后按如下内容配置基于HTTP代理的负载均衡，在最后添加：

```
 ProxyRequests Off    
   
  BalancerMember http://localhost:3000    
  BalancerMember http://localhost:3001    
   
  ServerName www.xxx.com    
  DocumentRoot d:/svn/zc_hn/code/zc/    
  ProxyPass images !    
  ProxyPass stylesheets !    
  ProxyPass javascripts !    
  ProxyPass / balancer://myCluster/    
  ProxyPassReverse / balancer://myCluster/    
  ProxyPreserveHost on    
```  

(网上的资料中在images 前面有"/",访问的时候页面图片css都没了，只有数据，去掉后正常)

-------------------

myCluster定义了群集中的每个mongrel应用服务器节点。ProxyPass /images !指明该URL开始的请求不代理给Mongrel群集，而由Apache自己处理。重起Apache，然后打开浏览器访问www.xxx.com，检查配置是否正确。
 
至此，在Windows Server上面一个具备良好稳定性和性能的Ruby on rails生产环境就搭建好了。
 
对于页面输出，还可以使用mod_deflate进行输出内容压缩，以提高页面下载速度，这个就留给大家自己配置了。