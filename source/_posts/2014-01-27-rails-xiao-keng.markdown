---
layout: post
title: "Rails 小坑"
date: 2014-01-27 12:03
comments: true
categories: 
- Rails{rails}
---

我在一个controller中定义了一个action，在这个action的最后：

{% codeblock Ruby代码: lang:ruby%}
redirect_to :action => 'show', :id => params[:term_id] , :data_type => params[:data_type], :specialty => params[:specialty],
                :norm_series => params[:norm_series], :range_from => params[:range_from], :range_to => params[:range_to],
                :top_type => params[:top_type], :sub_type => params[:sub_type], :review_type => params[:review_type],
                :status => params[:status], :page_size => params[:page_size] || 20, :page => params[:page] || 1
{% endcodeblock %}

当这个页面执行完以后，没有转到‘show’页面，却进入了一个空白的页面，这个页面只有一句话：

You are being redirected.

点击 'redirected' 以后，页面才跳转到'show' 页面，百思不得其姐。

最后通过查资料和自己思考终于知道原因了，关键在于后面的堆参数，其中有个 status 变量。哈哈~ 

说到这里你应该能猜到原因了吧。
