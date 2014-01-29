---
layout: post
title: "ruby 的迭代器"
date: 2014-01-27 19:19
comments: true
categories: 
- Ruby{ruby}
---

 
ruby中针对集合（数组、哈希）的迭代器有 each map collect inject，下面将逐一介绍

### 1 each用法
  each方法是最简单也最常用的，它会连续访问集合的所有元素然后传值给bloc执行，比如：

{% codeblock lang:ruby%}
[1,2,3,4,5].each do |a|
  puts a
end
{% endcodeblock %}

运行结果： 

     1
     2
     3
     4
     5


### 2 reverse_each 用法：
 
 对比each，从字面上理解reverse_each就是逆序迭代，例如

{% codeblock lang:ruby%}
a = [1,2,3] 
a.reverse_each { |v| puts v }
{% endcodeblock %}

运行结果

    3
    2
    1

a.reverse_each 和 a.reverse.each 有什么区别呢？  功能是一样的，但是效率不同, reverse_each的效率更高

{% codeblock lang:ruby%}
irb(main):001:0> now =Time.now; Array.new(30000000) {rand}.reverse.each {|x|x*2}; Time.now-now
=> 15.705195
irb(main):002:0> now =Time.now; Array.new(30000000) {rand}.reverse_each {|x|x*2}; Time.now-now
=> 12.956333
{% endcodeblock %}


### 3 map用法
  
  map也很常用,它从集合中获得各个元素传递给block，block返回的结果生成新的集合，比如：
  
  `[1,2,3,4,5].map{ |a|  a + 1 }  ->  [2, 3, 4, 5, 6]`
  
  再比如：

{% codeblock lang:ruby%}
users = User.where(:age => 20)
users.map{|user| user.name } -> ['zhangsan','lisi','wangwu']
{% endcodeblock %}

  这个例子可以简写成： `users.map(&:name)`  ,其运行结果也是一样的

<!-- more -->

### 4 collect用法  

  同map一样。

### 5 inject用法

  inject是最有魅力的一个方法，字面意思是‘注入’，可以理解为是一个具有Combines（联合、合并）功能的迭代器，比如：
  
 `[1, 2, 3, 4].inject(0) { |result, element| result + element } # => 10`
  
  inject带有一个参数和block,参数是初始值，另外给block中传递了两个值，第一个result是Combines操作的值，第二个参数element是迭代传入集合的每个元素。reslut在inject第一次执行block时把inject带的参数付值给它，该例中inject一共执行4次block，每次执行block完后，最后语句的结果再付值给result，如此循环，直到遍历数组中所有元素。我们深入这个例子看每一步执行的结果。

  因为数组有4个元素，所以要执行4次block操作：

  第一次操作：result=0，既等于inject带的参数；element=1，既第一个元素；

  执行block后result + element =1，再把这个结果付值给result，所以在执行完第一次block后，result = 1。

  第二次操作:result=1，既上次运行block后的最后一条语句的结果；element=2，既第二个元素；执行block后付值result＝3。

  如此类推，直到最后一次执行完block，生产的值为10。就是inject的返回值了。

  inject可以不带参数，此时result的初始值为数组元素的第一个值,element从第二个元素开始传值。所以上例可改写成：

  `[2, 3, 4].inject{ |result, element| result*element } # => 24`

  注意：这时block执行的次数是3次！而不是4次。

  再例如：

{% codeblock lang:ruby%}
array = [1, 2, 3, 4, 5, 6].inject([]) do |result, element|
  result << element.to_s if element % 2 == 0
  result
end
array # => ["2", "4", "6"]
{% endcodeblock %}

  注意：这个例子中第三行的result不能省略，否则会报错。

  继续举例：

{% codeblock lang:ruby%}
a = [[1,2,3],[2,3,4],[3,4,5]]

# get the union:
a.inject(a.first) { |f,x| f = f | x } # => [1, 2, 3, 4, 5]
a.inject { |f,x| f | x }              # => [1, 2, 3, 4, 5]
a.inject(:|)                          # => [1, 2, 3, 4, 5]
a.inject(&:|)

# get the intersection:
a.inject(a.first) { |f,x| f & x } # => [3]
a.inject { |f,x| f & x }          # => [3]
a.inject(:&)                      # => [3]

a.inject(&:&)                     # => [3]
{% endcodeblock %}

最后一个例子：

{% codeblock lang:ruby%}
# find the longest word
longest = %w{ cat sheep bear }.inject do |memo,word|
  memo.length > word.length ? memo : word
end
longest    # => "sheep"
{% endcodeblock %}

### 其它： all? 和 any?

all?[{|obj| block } ] → true or false

`all?`方法遍历集合中的每一个元素并传值给block，

如果block返回结果为包含false或nil，all?方法返回false；

如果block返回结果都为true，all?方法返回true；

如果没有给定block，只要集合中有一个值为false或nil，那么最终结果就是false

例如：

{% codeblock lang:ruby%}
%w{ant bear cat}.all? {|word| word.length >= 3}   #=> true
%w{ant bear cat}.all? {|word| word.length >= 4}   #=> false
[ nil, true, 99 ].all?                            #=> false
{% endcodeblock  %}

any?[{|obj| block } ] → true or false

any?方法遍历集合中的每一个元素并传值给block，

如果block返回结果有一个不为false或nil，any?方法返回true；

如果没有给定block，只要集合中有一个值不为false或nil，那么最终结果就是tru

{% codeblock lang:ruby%}
%w{ant bear cat}.any? {|word| word.length >= 3}   #=> true
%w{ant bear cat}.any? {|word| word.length >= 4}   #=> true
[ nil, true, 99 ].any?                            #=> true
{% endcodeblock %}



