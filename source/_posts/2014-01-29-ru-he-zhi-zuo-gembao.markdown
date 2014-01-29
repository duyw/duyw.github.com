---
layout: post
title: "如何制作gem包"
date: 2014-01-29 19:45
comments: true
categories: 
- Ruby{ruby}
- Rails{rails}
- Gem{gem}
---

Rails 支持多种形式的拓展，像plugin、gem或者干脆直接将通用工具类代码放置到lib目录下。plugin已经不推荐使用了，目前来看，最好的方式就是写成gem，本文是我学习gem制作整理的笔记，文中以手动添加一个helper方法为例。

### 第一步：安装 bundler

首先通过 `gem list` 命令查看如果已经安装了bundler就可以跳过此步。

安装命令：

```
gem install bundler
```

### 第二步：使用 bundler 生成一个基础gem结构

```
bundle gem my_gem
```

执行以后会看到以下输出：

    create  my_gem/Gemfile
    create  my_gem/Rakefile
    create  my_gem/LICENSE.txt
    create  my_gem/README.md
    create  my_gem/.gitignore
    create  my_gem/my_gem.gemspec
    create  my_gem/lib/my_gem.rb
    create  my_gem/lib/my_gem/version.rb
    Initializating git repo in /home/ivan/private/my_gem

说明：

1. `my_gem.gemspec` 文件相当于gem的说明书（specification），其中定义了Gem的内容、作者、描述、主页等信息。将来打包的时候会依赖这个文件。

2. Gem的代码主要放置在`lib`目录下。

3. 整个项目加入了Git版本控制。

4. 如果gem本身需要引入其他依赖的包，需要在`.gemspec`文件中指明例如:
    
    `spec.add_development_dependency "rake"`  引入开发依赖

    `spec.add_development_dependency "rails"` 引入运行依赖

5. 项目中有Gemfile文件，可将其中的 source 替换成淘宝源 "http://ruby.taobao.org"。

6. 另外，在Gemfile有一行内容是"gemspec",表明该项目依赖的gem包在`.gemspec`文件中指出，这就解释了为什么会有上面第4条。

7. my_gem/lib/my_gem/version.rb 文件中可以填写gem的版本信息。


### 第三步：编写gem要实现的功能

这里我们实现一个helper方法，在页面输出一段Html代码。

1. 新建文件 helper.rb , 路径： lib/my_gem/helper.rb

```
# coding: utf-8
module MyGem
  module MyHelper
    def my_helper
      time = DateTime.parse(Time.now.to_s).strftime('%H:%M:%S').to_s
      date = DateTime.parse(Time.now.to_s).strftime('%Y-%m-%d').to_s
      html =  []
      html << '<div id="mydiv">'
      html << '<div id="time">' + time + '</div>'
      html << '<div id="date">' + date + '</div>'
      html << '</div>'
      raw html.join("\n")
    end
  end
end
```

2. 引入刚才写的helper

修改 lib/my_gem.rb 文件，首行添加：

```
require "my_gem/helper"
```

3. 将helper正真拓展到rails中去

修改 lib/my_gem.rb 文件，尾行添加：

```
ActionView::Base.send :include, MyGem::MyHelper
```

### 第四步：提交代码

将项目提交到Git仓库，用到的命令：


    git add *
    
    git commit -m "finish my gem"

这一步很重要，因为在`.gemspec`文件中有这么一行：

```
spec.files         = `git ls-files`.split($/)
```

它调用了 `git ls-files` 命令来获取需要打包的文件，而这一命令只会返回Git仓库中的所有的文件，如果我们不提交代码那么项目中新建的文件就不会被打包进来。

### 第五步：打包

在项目下执行命令 `gem build my_gem.gemspec` 

    WARNING:  no homepage specified
      Successfully built RubyGem
      Name: my_gem
      Version: 0.0.1
      File: my_gem-0.0.1.gem

这里有一个警告，提醒我们填写homepage，这里只是练习不做理会。

### 第六步：使用

使用方法和其他gem包一样,需要注意的是我们的gem包并没有发布到rubygems.org，所以 gem install 的时候在my_gem-0.0.1.gem 文件所在的目录执行就ok了。

1. 安装： `gem install my_gem-0.0.1.gem`

2. 在rails项目的Gemfile中添加 `gem "my_gem"`

3. 在rails项目下运行命令 'bundle'

4. 在erb页面直接调用： `<%= my_helper %>` 。



