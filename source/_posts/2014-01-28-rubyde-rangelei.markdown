---
layout: post
title: "Ruby的Range类"
date: 2014-01-28 12:33
comments: true
categories: 
- Ruby{ruby}
---

Range 是ruby范围对象的类。范围对象是由范围操作符 ..或... 生成的。

..  生成的范围对象包括终点值。

... 生成的范围对象不包括终点值。

{%codeblock lang:ruby%}
1..3  # 表示 1、2、3
1...3 # 表示 1、2
{%endcodeblock%}

Range的实例在内存中只存储了起点和终点，因此比数组更节省空间。

Range 的常用方法：

1.通过 to_a 方法可以将Range对象转换成数组：
{%codeblock lang:ruby%}
('a'..'c').to_a   =>  ["a", "b", "c"]
(1..3).to_a       =>  [1, 2, 3]
{%endcodeblock%}

2.迭代Range
{%codeblock lang:ruby%}
(1..3).each{ |i| puts i } #依次输出 1、2、3
{%endcodeblock%}

3.include?
{%codeblock lang:ruby%}
('a'..'c').include?('b')
{%endcodeblock%}

4.end 返回范围终点，与是否包含范围终点无关：
{%codeblock lang:ruby%}
('a'..'c').end    => "c"
('a'...'c').end   => "c"
{%endcodeblock%}

5.step(s)   以步长s进行迭代：
{%codeblock lang:ruby%}
(1..10).step(2) {|v| p v}     =>  1, 3, 5, 7, 9
{%endcodeblock%}

6.min 最小值
{%codeblock lang:ruby%}
(1..10).min    => 1 
{% endcodeblock %}

7.max 最大值
{%codeblock lang:ruby%}
(1..10).max    => 10 
{%endcodeblock%}
