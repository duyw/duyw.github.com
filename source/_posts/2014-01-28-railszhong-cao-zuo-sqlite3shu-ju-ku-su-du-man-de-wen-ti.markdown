---
layout: post
title: "Rails中操作Sqlite3数据库速度慢的问题"
date: 2014-01-28 12:58
comments: true
categories: 
- Rails {rails}
- 数据库{database}
- Sqlite3{sqlite3}
---

项目中要将数据导出到sqlite数据库中，但是速度太慢了，无法接受：

```
   (0.1ms)  begin transaction
/home/ivan/work/temp/packagetest/app/models/material_type.rb:7: warning: multiple values for a block parameter (0 for 1)
from /home/ivan/.rvm/gems/ruby-1.8.7-p371@rails304/gems/activerecord-3.2.13/lib/active_record/connection_adapters/abstract/database_statements.rb:192
   (0.4ms)  insert into material_types(id,name,code,parent_id,created_at,updated_at) values ('40', '40_name','code', 40, 'Fri Nov 29 11:08:54 +0800 2013', 'Fri Nov 29 11:08:54 +0800 2013')
   (317.6ms)  commit transaction

   (0.1ms)  begin transaction
/home/ivan/work/temp/packagetest/app/models/material_type.rb:7: warning: multiple values for a block parameter (0 for 1)
from /home/ivan/.rvm/gems/ruby-1.8.7-p371@rails304/gems/activerecord-3.2.13/lib/active_record/connection_adapters/abstract/database_statements.rb:192
   (0.4ms)  insert into material_types(id,name,code,parent_id,created_at,updated_at) values ('41', '41_name','code', 41, 'Fri Nov 29 11:08:54 +0800 2013', 'Fri Nov 29 11:08:54 +0800 2013')
   (307.8ms)  commit transaction
```

每次最耗时的就是这个 commit transaction 了 居然要300ms左右。



网上查找原因：

SQLite 缺省为每个操作启动一个事务，所以成批插入的时候，每个插入操作都会在一个事务中执行：

这样的花1秒钟才能执行6个插入。如果把所有操作都放在一个事务中，速度能达到1秒几万条，完全不在一个数量级，于是寻求解决方法。

主要思路就是让多个插入操作在一个事务中进行，最后的解决方法是直接使用 SQLite3这个gem，而不通过active_record

方法介绍：

1 连接数据库：
{%codeblock lang:ruby%}
@db = SQLite3::Database.open(@db_path) # 连接sqlite3数据库
{%endcodeblock%}

2 配置
{%codeblock lang:ruby%}
@db.results_as_hash = true  
{%endcodeblock%}

该项默认为false， 设置成true以后返回的结果集是hash结构的，以字段名称作为key，方便读取数据，否则返回一个数组。

3 执行SQL 语句：
{%codeblock lang:ruby%}
sql = "insert into option (release_time, package_type, id, db_name, db_id) ..."
@db.execute sql
sql = "select * from posts where title=? and name=? "
@db.execute(sql, '标题','名称')
{%endcodeblock%}

4 遍历结果集
{%codeblock lang:ruby%}
@db.execute(sql, '标题','名称').each do |post|
  #...
end
{%endcodeblock%}

注意：如果没有找到数据会抛出异常，需要自行处理一下。

5 在单个事务中批量操作
{%codeblock lang:ruby%}
@db.transaction
100.times do |i|
  @db.execute("insert into post where name = 'name_#{i}' ")
end
@db.commit
{%endcodeblock%}
或者
{%codeblock lang:ruby%}
@db.transaction do
  100.times do |i|
    @db.execute("insert into post where name = 'name_#{i}' ")
  end
end
{%endcodeblock%}