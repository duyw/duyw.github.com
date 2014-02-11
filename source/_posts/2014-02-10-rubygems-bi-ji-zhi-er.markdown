---
layout: post
title: "RubyGems 笔记之二"
date: 2014-02-10 20:13
comments: true
categories: 
- RubyGems{rubygems}
- Gem{gem}
---


### gem的结构

1  每一个gem都要有名称、版本、运行环境。以rake 0.8.7 为例，其名称'rake',版本'1.8.7',运行平台是ruby，意味着可以运行在任何ruby平台上。

2 运行环境是基于CPU架构、操作系统之上，有时候还会受限于操作系统的版本。


3 在gem中包含以下组件
  代码：（包括测试和辅助程序）
  文档
  gemspec（gem的自述文件）

每一个gem包的代码结构组织都遵循统一的标准
```
% tree freewill
freewill/
├── bin/
│   └── freewill
├── lib/
│   └── freewill.rb
├── test/
│   └── test_freewill.rb
├── README
├── Rakefile
└── freewill.gemspec
```

如上所示，一个gem包含的主要组件有：

`lib`目录：包含gem的代码

`test`目录或`spec`目录，包含测试代码，取决于开发者用的是哪个测试框架。

`Rakefile`文件：rake程序使用该文件进行自动化测试，生成代码或者执行其他任务。

`bin`目录：当gem安装好以后，bin目录将添加到用户的环境变量中去。

文档通常在`README`中或者在代码的注释里面。当gem安装完成的时候，文档也会为你自动生成好，大部分gem包含RDoc文档，也有用YARD的。

`gemspec`文件，该文件的内容包含了文档相关的信息。
  
### The Gemspec

gemspec文件包含了文档的名称、作者、版本等相关的信息，例如：

```
% cat freewill.gemspec
Gem::Specification.new do |s|
  s.name        = 'freewill'
  s.version     = '1.0.0'
  s.summary     = "Freewill!"
  s.description = "I will choose Freewill!"
  s.authors     = ["Nick Quaranto"]
  s.email       = 'nick@quaran.to'
  s.homepage    = 'http://example.com/freewill'
  s.files       = ["lib/freewill.rb", ...]
end
```

更多gemspec相关内容参看[http://guides.rubygems.org/specification-reference/](http://guides.rubygems.org/specification-reference/)




