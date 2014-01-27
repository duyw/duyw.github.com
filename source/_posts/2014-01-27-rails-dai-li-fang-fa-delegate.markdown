---
layout: post
title: "Rails 代理方法 delegate"
date: 2014-01-27 18:58
comments: true
categories: 
- Rails{rails}
---


通过delegate方法，在Foo对象中可以直接引用Greeter对象的方法：

{% codeblock Ruby代码: lang:ruby%}
class Greeter < ActiveRecord::Base
  def hello
    "hello"
  end

  def goodbye
    "goodbye"
  end
end

class Foo < ActiveRecord::Base
  belongs_to :greeter
  delegate :hello, :to => :greeter
end

Foo.new.hello   # => "hello"
Foo.new.goodbye # => NoMethodError: undefined method `goodbye' for #<Foo:0x1af30c>
{% endcodeblock %}


Multiple delegates to the same target are allowed:

{% codeblock Ruby代码: lang:ruby%}
class Foo < ActiveRecord::Base
  belongs_to :greeter
  delegate :hello, :goodbye, :to => :greeter
end

Foo.new.hello   # => "hello"
Foo.new.goodbye # => "goodbye"
{% endcodeblock %}

delegate方法首先检查传入的参数，正确参数形式为:method1, :method2, ..., :methodN, :to => klass[, :prefix => prefix]

delegate要求参数的最后必须是一个Hash，
:to表示需要代理的类，:prefix表示代理的方法是否要加前缀，
如果:prefix => true，则代理的方法名为klass_method1, klass_method2, ..., klass_methodN，

{% codeblock Ruby代码: lang:ruby%}
class Foo < ActiveRecord::Base  
 delegate :hello, :goodbye, :to => :greeter, :prefix => true
end

Foo.new.greeter_hello   # => "hello"  
Foo.new.greeter_goodbye # => "goodbye"  
{% endcodeblock %}

如果:prefix => prefix (prefix为string)，则代理的方法名为prefix_method1, prefix_method2, ..., prefix_methodN。

{% codeblock Ruby代码: lang:ruby%}
class Foo < ActiveRecord::Base  
  delegate :hello, :goodbye, :to => :greeter, :prefix => :foo
end

Foo.new.foo_hello   # => "hello"  
Foo.new.foo_goodbye # => "goodbye"  
{% endcodeblock %}
