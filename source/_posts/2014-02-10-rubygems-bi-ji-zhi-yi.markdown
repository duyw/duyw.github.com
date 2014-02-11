---
layout: post
title: "RubyGems 笔记之一"
date: 2014-02-10 18:46
comments: true
categories: 
- Gem{gem}
- RubyGems{rubygems}
---



## 简介：
-----------------------

1. ruby的软件包称为 'gem', 类似与java的jar包。ruby的gem包用来拓展或修改ruby应用程序的函数，通常将一些可复用的函数打包成gem，作为库与方便与其他开发者共享。有的gem提供了命令行工具来实现 automate tasks ，提升你的工作效率。


2. RubyGems是用于管理gem工具，可以方便的下载、安装、使用和卸载gem。


3. `gem` 命令用于管理RubyGems。Ruby1.9 以及后来的版本都默认集成了RubyGmes，安装完ruby以后，就可以直接使用 gem 命令。



## 使用
-----------------------


### 一、 查找Gems：

`search` 命令用于查找远程的gem包。

```
╭─# ivan@ubuntu in ~/private/19wu ‹ruby-2.0.0@19wu›  
╰─$ gem searc\^rails  

*** REMOTE GEMS ***

rails (4.0.2)
rails-3-settings (0.1.1)
rails-action-args (0.1.1)
rails-admin (0.0.0)
...
```
`-d` 参数可以查看更加详细的信息：

```
╭─# ivan@ubuntu in ~/private/19wu ‹ruby-2.0.0@19wu›  
╰─$ gem search  \^rails\$ -d

*** REMOTE GEMS ***

rails (4.0.2)
    Author: David Heinemeier Hansson
    Homepage: http://www.rubyonrails.org

    Full-stack web application framework.
```

其他参数， 参考 `gem search --help`

### 二、 安装Gems

`install` 命令用于安装gems。

例如：

```
gem install rails -v= 4.0 --no-rdoc --no-ri
```

### 三、Requiring code

RubyGems 修改了Ruby的加载路径，并且控制如何查找require进来的ruby代码。当require一个gem的时候，会将这个gem的路径添加到全局变量`$LOAD_PATH`中。

在启动irb的时候，通过 `-r` 参数可以指定要加载的gem：

```
╭─# ivan@ubuntu in ~/private/19wu ‹ruby-2.0.0@19wu›  
╰─$ irb -rpp 
irb(main):001:0>  pp $LOAD_PATH
["/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/site_ruby/2.0.0",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/site_ruby/2.0.0/i686-linux",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/site_ruby",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/vendor_ruby/2.0.0",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/vendor_ruby/2.0.0/i686-linux",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/vendor_ruby",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/2.0.0",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/2.0.0/i686-linux"]
...
```
默认只加载几个必要的标准库，如果要加载指定的gem：

```
╭─# ivan@ubuntu in ~/private/19wu ‹ruby-2.0.0@19wu›  
╰─$ irb -rpp 
irb(main):001:0> require 'rails'
=> true
irb(main):002:0>  pp $LOAD_PATH
["/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/i18n-0.6.5/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/multi_json-1.7.9/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/tzinfo-0.3.37/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/atomic-1.1.12/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/thread_safe-0.1.2/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/activesupport-4.0.0/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/builder-3.1.4/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/rack-1.5.2/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/rack-test-0.6.2/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/erubis-2.7.0/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/actionpack-4.0.0/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/thor-0.18.1/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/railties-4.0.0/lib",
 "/home/ivan/.rvm/gems/ruby-2.0.0-p353@19wu/gems/json-1.8.0/lib",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/site_ruby/2.0.0",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/site_ruby/2.0.0/i686-linux",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/site_ruby",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/vendor_ruby/2.0.0",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/vendor_ruby/2.0.0/i686-linux",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/vendor_ruby",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/2.0.0",
 "/home/ivan/.rvm/rubies/ruby-2.0.0-p353/lib/ruby/2.0.0/i686-linux"]
...
```

注意： 如果您使用的是 ruby 1.8 版本，在引入任何gem之前，需要先引入 rubygems： `require 'rubygems'`，在某gem所在目录的lib目录下，必须存在一个rb文件和一个目录，要求名称和gem的名称一致。

```
% tree freewill/
freewill/
└── lib/
    ├── freewill/
    │   ├── user.rb
    │   ├── widget.rb
    │   └── ...
    └── freewill.rb
```

### 四、 列举出所有已经安装好的gem

`list` 命令可以列举出所有已经安装好个gem

```
╭─# ivan@ubuntu in ~/private/19wu ‹ruby-2.0.0›  
╰─$ gem list

*** LOCAL GEMS ***

bigdecimal (1.2.0)
gem-wrappers (1.2.4)
io-console (0.4.2)
json (1.7.7)
minitest (4.3.2)
psych (2.0.0)
rake (0.9.6)
rdoc (4.0.0)
test-unit (2.0.0.0)

```

### 五、 卸载Gems

`uninstall` 命令用于卸载已经安装的gem包。

```
$ gem uninstall drip
Successfully uninstalled drip-0.0.2
```

如果你要卸载一个被其他gem依赖的gem，会要求进行确认：

```
$ gem uninstall rbtree

You have requested to uninstall the gem:
	rbtree-0.4.1

drip-0.0.2 depends on rbtree (>= 0)
If you remove this gem, these dependencies will not be met.
Continue with Uninstall? [yN]  n
ERROR:  While executing gem ... (Gem::DependencyRemovalException)
    Uninstallation aborted due to dependent gem(s)
```
### 六、 查看gem文档

`ri` 命令可以用于查看gem的文档。或者运行 `gem server` 通过浏览器查看

### 七、 Fetching and Unpacking Gems

如果你仅仅想获取并查看一个gem的内容，可以通过`fetch`和`unpack`命令来实现：

```
╭─# ivan@ubuntu in ~/private/temp ‹ruby-1.8.7@rails304›  
╰─$ gem fetch json
Fetching: json-1.1.5-x86-linux.gem (100%)
Downloaded json-1.1.5-x86-linux

╭─# ivan@ubuntu in ~/private/temp ‹ruby-1.8.7@rails304›  
╰─$ ls   
json-1.1.5-x86-linux.gem

╭─# ivan@ubuntu in ~/private/temp ‹ruby-1.8.7@rails304›  
╰─$ gem unpack json-1.1.5-x86-linux.gem 
Unpacked gem: '/home/ivan/private/temp/json-1.1.5-x86-linux'

╭─# ivan@ubuntu in ~/private/temp ‹ruby-1.8.7@rails304›  
╰─$ ls
json-1.1.5-x86-linux  json-1.1.5-x86-linux.gem
```



以上内容参考：

[http://guides.rubygems.org/rubygems-basics/](http://guides.rubygems.org/rubygems-basics/)

