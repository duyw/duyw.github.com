---
layout: post
title: "正确使用label标签"
date: 2014-01-29 20:34
comments: true
categories: 
- HTML {html}
---

表单中使用 checkbox 的时候，经常见到这样这样的写法：

{% codeblock lang:html %}
<input type="checkbox"> Click Me!
{% endcodeblock %}

这种写法选择的时候只能点击复选框，如果要实现点击文字“Click Me!” 也能进行选择操作，应该这么写：

{% codeblock lang:html %}
<label><input type="checkbox"> Click Me!</label>
{% endcodeblock %}

或者

{% codeblock lang:html %}
<input type="checkbox" id="ckbx">
<label for=ckbx">Click Me!</label>
{% endcodeblock %}

我有强迫症，看到项目中不规范的写法就忍不住会给改过来，在一次内部分享中我专门分享了这点，但是还有人这么写，抓狂！

偶然一次发现京东商城的登陆页面有个“自动登录”的checkbox，点击文字的时候就没有选中，每次上京东都会去点一下看改过来没有，即使自动登录了我也会退出重新登陆，持续了快一年了......
