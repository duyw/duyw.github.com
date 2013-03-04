---
layout: post
title: "Octopress simple guide"
date: 2013-02-28 18:15
comments: true
categories: 
- 其他{others}
---

Octopress的搭建过程如下：

### 1 配置本地Octopress

```
git clone git://github.com/imathis/octopress.git davidpan.github.com
cd davidpan.github.com 
gem install bundler 
bundle install 
rake install
```

### 2 关联到 GitHub

```
rake setup_github_pages

# 输入git源：git://github.com/duyw/duyw.github.com.git
# 命令执行之后 会生成一个 _deploy 目录，该目录本身也是一个git的仓库
```

<!-- more -->

### 3 修改配置文件

修改OctoPress根目录 _config.yml

```
url:  http://username.github.com     # 博客地址
title:                               # 博客标题 
subtitle:                            # 副标题 
author:                              # 作者 
simple_search:  http://www.google.com.hk/search     # 搜索引擎 
description:                                        # 关于博客的描述 
subscribe_rss:  /atom.xml            # Rss订阅地址, 默认是  /atom.xml 
subscribe_email:                     # 提供Email订阅的地址 
email:                               # Rss订阅的Email地址
root:  /               # 博客路径，默认是“/“，如果你打算在子目录中，记得修改这个路径 
permalink: /blog/:year/:month/:day/:title/           # 文章的固定链接形式
```


### 4 跟换主题

主题位于octopress根目录的 .theme 文件夹下，以安装'slash'主题为例：

```
cd .theme
git clone git://github.com/tommy351/Octopress-Theme-Slash.git slash
rake install['slash']
rake generate
```
> 提示： 更改网站样式相关
>
> [https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes](https://github.com/imathis/octopress/wiki/Octopress-Sites)
>
> [https://github.com/imathis/octopress/wiki/Octopress-Sites](https://github.com/imathis/octopress/wiki/Octopress-Sites)

### 5 添加文章

执行 `rake new_post["my first blog"]` ,

这样会在octopress的/source/_post下生成一个markdown文件，可以用文本软件进行编辑

### 6 新建页面

命令：`rake new_page["page_name"]`

> 提示：修改source/_include/custom 文件，可将页面添加到导航栏。


### 7 生成静态页面，预览，发布

完成编辑后执行`rake generate`，生成静态页面（将source和sass下的源码生成到public目录）。
执行`rake preview`，启动本地预览，可以在浏览器内以这个地址进行预览。
最后执行rake deploy，将日志提交到github上（将生成到public中的内容复制到_deploy目录，并提交到github的master分支）。

> 提示：generate 和 deploy 可以用一条命令搞定： rake gen_deploy

### 8 将修改备份到github的source分支上

```
git add .
git commit -m 'your message'
git push origin source
```

### 9 补充

* 查询所有rake参数 ： `rake -T`

* 添加附加功能

  1 使用google的 Analytics 监控网站流量，需要先申请账号。

    先将需要添加的代码添加到 source/_includes 文件中 

    然后配置 google_analytics_tracking_id 项 （位于_config.yml文件中）。

  2 添加评论，使用 DISQUS 实现。同样需要修改 _config.yml 文件


<br /><br /><br />
  ----------------------------------
<br />


### 为github上已有的oct opress配置本地环境 方法一

1. 直接将远程仓库签出到本地

`git clone git@github.com:duyw/duyw.github.com.git duyw.github.com`

2. 签出source分支的源码

```
cd duyw.github.com`
git checkout sources`
# 这样当前项目就有了两个分支
# 查看分支命令： git branch
```

3. 安装依赖

`bundle install`

4. 配置和github的关联

`rake setup_github_pages`


5. 生成预览部署

rake generate, rake preview, rake deploy