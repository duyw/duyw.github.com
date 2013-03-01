---
layout: post
title: "markdown使用学习"
date: 2013-02-28 15:13
comments: true
categories: 其他
---

### Markdown简介
------------------

　　Markdown 是一种轻量级的标记语言，由John Gruber和Aaron Swartz创建，使其成为可读性最大并可再发行的可输入输出的格式。这种语言创建灵感来自于已经存在的带标记的电子邮件文本。Markdown 允许 HTML 语法，所以使用者如果需要可以直接用 HTML来表示是可以的。Markdown最初由Gruber应用在Perl语言中，但现在已经有多种编程语言应用了。它是开源项目，并以BSD-style许可证的许可方式以插件形式或内容管理系统形式发布。

<!-- more -->

### 快速上手
------------------


### 标题
```
# 这是 H1

## 这是 H2

###### 这是 H6
```

### 文字样式

粗体用“**”或者“__”包围，斜体用“*”或者“_”

粗体： **粗体**      写法：`**粗体**`或者`__粗体__`

斜体： _斜体_      写法： `_斜体_`或者`*斜体*`


### 分隔符

在一行中使用三个或三个以上的*、-或_可以添加分隔线，其中可以有空白，但是不能有其他字符。


### 段落

　　由一个或多个连续的文本行组成，它的前后要有一个以上的空行。


### 引用

```
> 这是一个引用，
> 这里木有换行，   
> 在这里换行了。
> > 内部嵌套
```

效果：

> 这是一个引用，
> 这里木有换行，   
> 在这里换行了。
> > 内部嵌套

### 列表

　　Markdown 支持有序列表和无序列表。

* 无序列表使用星号、加号或是减号作为列表标记：

```
*   Red
*   Green
*   Blue
```

等同于：
```
+   Red
+   Green
+   Blue
```

也等同于：
```
-   Red
-   Green
-   Blue
```
* 有序列表则使用数字（任意数字即可，相同数字也ok）接着一个英文句点：

```
1.  Bird
2.  McHale
3.  Parish
```

```
1.  Bird
1.  McHale
1.  Parish
```

### 内容区块
用一个缩进或者反单引号来将需要突出显示内容区块
例如：

```
	以热爱祖国为荣，以危害祖国为耻；
	以服务人民为荣，以背离人民为耻；
	以崇尚科学为荣，以愚昧无知为耻；
	以辛勤劳动为荣，以好逸恶劳为耻；
	以团结互助为荣，以损人利己为耻；
	以诚实守信为荣，以见利忘义为耻；
	以遵纪守法为荣，以违法乱纪为耻；
	以艰苦奋斗为荣，以骄奢淫逸为耻。
```

	以热爱祖国为荣，以危害祖国为耻；
	以服务人民为荣，以背离人民为耻；
	以崇尚科学为荣，以愚昧无知为耻；
	以辛勤劳动为荣，以好逸恶劳为耻；
	以团结互助为荣，以损人利己为耻；
	以诚实守信为荣，以见利忘义为耻；
	以遵纪守法为荣，以违法乱纪为耻；
	以艰苦奋斗为荣，以骄奢淫逸为耻。


### 代码区块


* 用三个 “`”符号包围代码：

```
```
def update
  respond_to do |format|
    if @comment.update_attributes(params[:comment])
      format.js
    else
      render :json => :error
    end
  end
end
`` `
```


Octopress中可以为代码着色，写法：

{% codeblock 代码说明： lang:ruby http://www.baidu.com 链接到百度 %}
def update
  respond_to do |format|
    if @comment.update_attributes(params[:comment])
      format.js
    else
      fromat.html
    end
  end
end
{% endcodeblock %}



* 单行代码

用反引号包围要显示的`单行`代码片段:
`<img src="/path/to/img.jpg" alt="alt text" title="Title" />`


### 图片

![Ruby China的logo](http://l.ruby-china.org/assets/text_logo-e616f2a05abc2e0ebea1f8c6cac685d7.png "Ruby China的logo")
写法：
```
![Ruby China的logo](http://l.ruby-china.org/assets/text_logo-e616f2a05abc2e0ebea1f8c6cac685d7.png "Ruby China的logo")
```
图片的另一种写法：
```
![第一张百度logo图][id1]
![第二张百度logo图][id2]

[id1]: http://www.baidu.com/img/shouye_b5486898c692066bd2cbaeda86d74448.gif  "Optional title attribute1"
[id2]: http://www.baidu.com/img/shouye_b5486898c692066bd2cbaeda86d74448.gif  "Optional title attribute2"
```
效果：

![第一张百度logo图][id1]
![第二张百度logo图][id2]
[id1]: http://www.baidu.com/img/shouye_b5486898c692066bd2cbaeda86d74448.gif  "Optional title attribute1"
[id2]: http://www.baidu.com/img/shouye_b5486898c692066bd2cbaeda86d74448.gif  "Optional title attribute2"


在octopress中图片写法：
	{% img [left|right] [class names] /path/to/image [width] [height] [title text [alt text]] %}


效果

{% img http://placekitten.com/890/280 %}
{% img left http://placekitten.com/320/250 Place Kitten #2 %}
{% img right http://placekitten.com/300/500 150 250 Place Kitten #3 %}
{% img right http://placekitten.com/300/500 150 250 'Place Kitten #4' 'An image of a very cute kitten' %}

### 链接

这是一个无title链接： [百度](http://www.baidu.com/)
写法：
```
[百度](http://www.baidu.com/)
```
这是一个有title链接： [百度](http://www.baidu.com/ "点击打开百度首页")
```
[百度](http://www.baidu.com/ "点击打开百度首页")
```

I get 10 times more traffic from [Google][aaaa] than from
[Yahoo][bbbb] or [MSN][cccc].
[aaaa]: http://google.com/ "Google"
[bbbb]: http://search.yahoo.com/ "Yahoo Search"
[cccc]: http://search.msn.com/ "MSN Search"
```
I get 10 times more traffic from [Google][aaaa] than from
[Yahoo][bbbb] or [MSN][cccc].
[aaaa]: http://google.com/ "Google"
[bbbb]: http://search.yahoo.com/ "Yahoo Search"
[cccc]: http://search.msn.com/ "MSN Search"
```




最后贴一张儿子的照片 ～～～

![儿子照片](http://p13.freep.cn/p.aspx?u=v20_p13_photo_1302281746571604_0.jpg "儿子照片")



