---
layout: post
title: "Controller 中使用 helper"
date: 2014-01-27 18:54
comments: true
categories: 
- Rails{rails}
---

helper方法默认只能在view层调用（比如strip_tags方法）。如果想要在controller或model中使用，有两中方法：


### 方法一：

{% codeblock Ruby代码: lang:ruby%}
class PostsController < ApplicationController
  def create
    ActionController::Base.helpers.strip_tags('string')
  end
end
{% endcodeblock  %}

### 方法二：

用代理delegate

{% codeblock Ruby代码: lang:ruby%}
class PostsController < ApplicationController
  delegate "strip_tags", :to => "ActionController::Base.helpers"
  def create
    strip_tags("html...")
    ...
  end
end
{% endcodeblock  %}
