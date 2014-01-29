---
layout: post
title: "PostgreSQL 常用函数"
date: 2014-01-29 00:09
comments: true
categories: 
- 数据库{database}
- PostgreSQL{postgresql}
---


### 一 常用数学操作符

    %    模      5 % 4      1
    ^    幂      2.0 ^ 3.0  8
    |/   平方根  |/ 25.0     5
    ||/  立方根  ||/ 27.0    3
    !    阶乘    5 !        120
    !!   阶乘    !! 5       120
    @    绝对值   @ -5.0     5

### 二 常用数学函数

取绝对值： `abs()`
{% codeblock lang:sql %}
select abs(-5); -- 5
{% endcodeblock %}

求余数： `mod(x,y)`
{% codeblock lang:sql %}
select mod(5,4); --1
{% endcodeblock %}

平方根： `sqrt()`
{% codeblock lang:sql %}
select sqrt(4); -- 2
{% endcodeblock %}  

立方根： `cbrt()`
{% codeblock lang:sql %}
select cbrt(8); --2
{% endcodeblock %}

随机数： `random()`, 0.0 到 1.0 之间
{% codeblock lang:sql %}
select random(); -- 0.527410356327891
{% endcodeblock %}

不小于参数的最小的整数： `cell()`
{% codeblock lang:sql %}
select cell(-42.8); -- -42
{% endcodeblock %}

不大于参数的最大整数: `floor()`
{% codeblock lang:sql %}
select floor(-42.8); -- -43`
{% endcodeblock %}

### 三 字符串函数

字符串连接： `string || string`
{% codeblock lang:sql %}
select 'ruby' || 'china' ; -- rubychina
{% endcodeblock %}

<!-- more -->

字符串长度： `length()`
{% codeblock lang:sql %}
select length('i love you') ; -- 10
{% endcodeblock %}

字符串转小写： `lower()`
{% codeblock lang:sql %}
select lower('RUBY'); -- ruby
{% endcodeblock %}

字符串转大写： `upper()`
{% codeblock lang:sql %}
select upper('rails'); -- RAILS
{% endcodeblock %}

字符串每个单词的第一个子母转为大写： `initcap()`
{% codeblock lang:sql %}
select initcap('ruby on rails'); -- Ruby on Rails
{% endcodeblock %}

字符串替换： `overlay(string placing string from int [for int])` 和 

`replace(string text, from text, to text)`
{% codeblock lang:sql %}
select overlay('Ruby_______Rails' placing ' on ' from 5 for 7)；-- Ruby on Rails
select replace('Ruby_______Rails', '_______', ' on '); -- Ruby on Rails`
{% endcodeblock %}

截取字符串： `substring(string [from int] [for int])` 和 `substr(string, from [, count])`
{% codeblock lang:sql %}
select substring('Ruby on Rails' from 1 for 4); -- Ruby
select substring('Ruby on Rails', 1, 4);        -- Ruby
select substr('Ruby on Rails', 1, 4);           -- Ruby
select substr('Ruby on Rails', 6);              -- on Rails
select substring('foobar' from '%#"o_b#"%' FOR '#'); 
-- oob  (这里#是转义符，双引号内的模式是返回部分)
{% endcodeblock %}


子字符串位置 `position(substring in string)`
{% codeblock lang:sql %}
select position('ruby-china','-'); 
-- 5 (利用这个特性可以用于判断字符串中是否包含某个子字符串)
{% endcodeblock %}

去除首位空格：`trim(string)`
{% codeblock lang:sql %}
select trim('    ruby    ');  -- 'ruby'
{% endcodeblock %}

去除左边空格/字符： `ltrim(string text [, characters text])`
{% codeblock lang:sql %}
select ltrim('    ruby') -- ruby
select ltrim('__ruby','_') -- ruby
{% endcodeblock %}

去除右边空格/字符： `rtrim(string text [, characters text])`
{% codeblock lang:sql %}
select rtrim('ruby    ')    -- ruby
select rtrim('ruby__','_')  -- ruby
{% endcodeblock %}

获取字符串的 MD5 值：`md5()`
{% codeblock lang:sql %}
select md5('ruby'); -- 58e53d1324eef6265fdb97b08ed9aadf
{% endcodeblock %}

字符串分割： `split_part(string text, delimiter text, field int)`
{% codeblock lang:sql %}
select split_part('i||love||you', '||', 2); -- love
{% endcodeblock %}

### 四 模式匹配

#### Like

用法举例

    'abc' LIKE 'abc'    true
    'abc' LIKE 'a%'     true
    'abc' LIKE '_b_'    true
    'abc' LIKE 'c'      false  

下划线(_)代表匹配任何单个字符，而一个百分号(%)匹配任何零或更多字符，如：

#### SIMILAR TO正则表达式

SIMILAR TO根据模式是否匹配给定的字符串而返回真或者假。
{% codeblock lang:sql %}
string SIMILAR TO pattern [ESCAPE escape-character]
string NOT SIMILAR TO pattern [ESCAPE escape-character]
{% endcodeblock %}

它和LIKE非常类似，支持LIKE的通配符('_'和'%')且保持其原意。除此之外，SIMILAR TO还支持一些自己独有的元字符，如：    

1. | 标识选择(两个候选之一)。

2. * 表示重复前面的项零次或更多次。

3. + 表示重复前面的项一次或更多次。

4. 可以使用圆括弧()把项组合成一个逻辑项。

5. 一个方括弧表达式[...]声明一个字符表，就像POSIX正则表达式一样。

见如下示例：

    'abc' SIMILAR TO 'abc'            -- true
    'abc' SIMILAR TO 'a'              -- false
    'abc' SIMILAR TO '%(b|d)%'        -- true
    'abc' SIMILAR TO '(b|c)%'         -- false

带三个参数的substring，substring(string from pattern for escape-character)，提供了一个从字串中抽取一个匹配SQL正则表达式模式的子字串的函数。和SIMILAR TO一样，声明的模式必须匹配整个数据串，否则函数失效并返回NULL。为了标识在成功的时候应该返回的模式部分，模式必须出现后跟双引号(")的两个转 义字符。匹配这两个标记之间的模式的字串将被返回，如：
{% codeblock lang:sql %}
SELECT substring('foobar' from '%#"o_b#"%' FOR '#'); -- oob
{% endcodeblock %}

####  用于日期/时间格式化的模式

    模式    描述
    HH     一天的小时数(01-12)
    HH12   一天的小时数(01-12)
    HH24   一天的小时数(00-23)
    MI     分钟(00-59)
    SS     秒(00-59)
    MS     毫秒(000-999)
    US     微秒(000000-999999)
    AM     正午标识(大写)
    Y,YYY  带逗号的年(4和更多位)
    YYYY   年(4和更多位)
    YYY    年的后三位
    YY     年的后两位
    Y      年的最后一位
    MONTH  全长大写月份名(空白填充为9字符)
    Month  全长混合大小写月份名(空白填充为9字符)
    month  全长小写月份名(空白填充为9字符)
    MON    大写缩写月份名(3字符)
    Mon    缩写混合大小写月份名(3字符)
    mon    小写缩写月份名(3字符)
    MM     月份号(01-12)
    DAY    全长大写日期名(空白填充为9字符)
    Day    全长混合大小写日期名(空白填充为9字符)
    day    全长小写日期名(空白填充为9字符)
    DY     缩写大写日期名(3字符)
    Dy     缩写混合大小写日期名(3字符)
    dy     缩写小写日期名(3字符)
    DDD    一年里的日子(001-366)
    DD     一个月里的日子(01-31)
    D      一周里的日子(1-7；周日是1)
    W      一个月里的周数(1-5)(第一周从该月第一天开始)
    WW     一年里的周数(1-53)(第一周从该年的第一天开始)


### 五 日期/时间函数

当前时间： `localtime`, `localtimestamp`, `now()`, `current_time`
{% codeblock lang:sql %}
select localtime, localtimestamp, now(), current_time; 
--00:06:12.109832 | 2014-01-29 00:06:12.109832 | 2014-01-29 00:06:12.109832+08 | 00:06:12.109832+08
{% endcodeblock %}

当前日期： `current_date`
{% codeblock lang:sql %}
select current_date; -- 2014-01-28
{% endcodeblock %}

当前日期和时间： `current_timestamp`
{% codeblock lang:sql %}
select current_timestamp; -- 2014-01-28 23:40:14.761216+08
{% endcodeblock %}

获取子域： `date_part(text, timestamp)`, 等同于 `extract(field from timestamp)`
{% codeblock lang:sql %}
select date_part('year',   timestamp'2014-01-28 23:40:14.761216+08'); -- 2014
select date_part('month',  timestamp'2014-01-28 23:40:14.761216+08'); -- 1
select date_part('day',    timestamp'2014-01-28 23:40:14.761216+08'); -- 28
select date_part('week',   timestamp'2014-01-28 23:40:14.761216+08'); -- 5
select date_part('hour',   timestamp'2014-01-28 23:40:14.761216+08'); -- 23
select date_part('minute', timestamp'2014-01-28 23:40:14.761216+08'); -- 40
select date_part('second', timestamp'2014-01-28 23:40:14.761216+08'); -- 14.761216

select extract(year from timestamp '2014-01-28 23:40:14.761216+08');  -- 2014
{% endcodeblock %}

截断成指定的精度: `date_trunc(text, timestamp)`
{% codeblock lang:sql %}
select date_trunc('hour', timestamp '2014-01-28 23:40:14.761216+08');
-- 2014-01-28 23:00:00
{% endcodeblock %}



### Tips
--------------------

1 BETWEEN

`a BETWEEN x AND y` 等效于 `a >= x AND a <= y`

`a NOT BETWEEN x AND y` 等效于 `a < x OR a > y`

2 拼接多条记录的值

`select array_to_string( array(select nickname from users where id < 100), ';')`:
