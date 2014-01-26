---
layout: post
title: "Install gem without ri and rdoc"
date: 2013-05-03 09:23
comments: true
categories:
- Gem{gem}
---


用 `gem install XXX` 安装 Gem 包的同时会安装 ri 和 rdoc，但这两个东西一般用不到，另外还严重影响安装速度，介于这两个原因我们一般都会这么做：

`gem install rails --no-ri --no-rdoc`

但是每个 Gem 包安装都这么做就太麻烦了，我们可以直接修改配置让 Gem 默认不安装 ri 和 rdoc：

打开 ~/.gemrc   （如果没有这个文件可以自己手动创建）

在文件最后加入 `gem: --no-ri --no-rdoc` ：

    ---
    :backtrace: false
    :benchmark: false
    :bulk_threshold: 1000
    :sources:
    - http://ruby.taobao.org
    :update_sources: true
    :verbose: true
    gem: --no-ri --no-rdoc

大功告成~