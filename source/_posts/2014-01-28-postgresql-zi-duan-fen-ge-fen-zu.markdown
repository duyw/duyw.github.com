---
layout: post
title: "PostgreSQL 字段分割分组"
date: 2014-01-28 20:43
comments: true
categories: 
- 数据库{database}
- PostgreSQL{postgresql}
---

原始： 在import_products中的location_prices字段存储内容：

     id  | location_prices  
    -----|---------------------
      1  | 北京:200.0;河南:100.0
      2  | 河南;100.0

{% codeblock lang:sql %}
select id ,
  split_part(unnest(string_to_array(location_prices,';')),':',1) area_name,
  split_part(unnest(string_to_array(location_prices,';')),':',2) price
from import_products
where id in (542796,542797)
{% endcodeblock %}

结果： 

     id  | area_name | price  
    -----|-----------|-------
      1  |    北京    | 200.0
      1  |    河南    | 100.0
      2  |    河南    | 100.0


知识点：

### 1. string_to_array 

string_to_array函数的作用是将string类型的字符串分割成数组。
{% codeblock lang:sql %}
select string_to_array('i|love|you','|') ;  -- {i,love,you}
{% endcodeblock %}

### 2. unnest

unnest 函数的作用是将数组转换成行。

{% codeblock lang:sql%}
select unnest(array['i','love','you']);
{% endcodeblock %}

    unnest  
    -------
      i
     love
      you

### 3. split_part

split_part 函数作用是将字符串按照另一个字符串进行分割，并返回分隔以后的
{% codeblock lang:sql %}
select split_part('i||love||you', '||', 2);` -- love
{% endcodeblock %}

