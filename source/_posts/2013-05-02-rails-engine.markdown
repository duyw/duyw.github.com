---
layout: post
title: "Rails Engine"
date: 2013-05-02 15:55
comments: true
categories: 
- Rails{rails}
- Engine{engine}
- Rails Engine{rails engine}
---


### 1. Engine是什么？

Engine 可以被看作是（一个）微型的rails应用程序。其提供（一些额外的）功能给它们的宿主程序（host applications）。

一个 Rails 应用程序实际上就是一个 “加强版”（supercharged） engine，因为 Rails::Application 继承了来自 Rails::Engine 的更多习惯。

Engine 也可以与他们的宿主程序相互隔离。controllers， models 和 table也是名称空间化的。

可以简单的理解为 Engine 是对宿主程序的一个拓展、加强。典型的Engine案例有[Devise](https://github.com/plataformatec/devise)、[Forem](https://github.com/radar/forem)、[Refinerycms](https://github.com/resolve/refinerycms)等。



### 2. 创建一个Engine

本文以创建一个基于 rails3.2 的 Engine 为例，命令：

```
rails plugin new my_engine --full --mountable
```

`--full`:  创建一个完整的，等同rails项目的目录结构。

`--mountable`: 选项告诉创建器去挂载 engine 到在 engine 中的 test/dummy 的模拟（dummy）测试程序。在dummy的 config/routes.rb 加入内容：`mount MyEngine::Engine => "/my_engine"`










