---
layout: post
title: "rails 连接 Oracle"
date: 2010-12-29 14:06
comments: true
categories:
- Rails{rails}
- Oracle{oracle}
- 数据库{database}

---


我的环境如下：

    ruby 1.8.7

    rails 2.3.8 

    本机安装 Oracle 11g客户端

    另一台电脑安装 Oracle 11g的服务器端


需要用到的gem包：

    ruby-oci8

    activerecord-oracle-adapter

### 操作步骤：

 activerecord-oracle-adapter插件的使用说明：[跳转](http://github.com/rsim/oracle-enhanced/wiki/Usage)


#### 1.首先将oracle客户端添加到系统环境变量中。

    注意：

    * Oracle 的密码不能为纯数字，否则报错。

    * 安装好客户端后要确保安装无误，可以访问到服务器。

#### 2.安装需要的gem包

```
gem install ruby-oci8-2.0.4-x86-mingw32.gem
gem install activerecord-oracle-adapter --source http://gems.rubyonrails.org
```

注意：安装好 oci8 后要测试 是否安装正确 方法是打开 irb 输入 require ‘oci8’ 如果不报错说明安装成功。

另外可以用oci8测试 查询：

{% codeblock Ruby代码: lang:ruby%}
OCI8.new('dyw1','123456','192.168.23.162/orcl').exec('select * from journals') do |r|
  puts r.join(',') 
end
{% endcodeblock %}

<!-- more -->

#### 3.在rails项目中，进入config目录，修改 environment.rb 文件，添加如下内容：

{% codeblock Ruby代码: lang:ruby%}
config.gem "activerecord-oracle_enhanced-adapter", :lib => "active_record/connection_adapters/oracle_enhanced_adapter"
{% endcodeblock %}

#### 4.修改config目录下的 database.yml 文件，配置数据库的格式：

{% codeblock YAML代码: lang:yaml %}
development:
  adapter: oracle_enhanced
  host: 192.168.23.162/ORCL
  username: OPAC
  password: opac
{% endcodeblock %}


#### 5.在config/initializers 目录下创建文件 oracle.rb，当rails启动会自动执行，内容：


{% codeblock Ruby代码: lang:ruby%}
ActiveRecord::ConnectionAdapters::OracleEnhancedAdapter.class_eval do
  self.emulate_integers_by_column_name = true
  self.emulate_dates_by_column_name = true
  self.emulate_booleans_from_strings = true
  self.string_to_date_format = "%d.%m.%Y"
  self.string_to_time_format = "%d.%m.%Y %H:%M:%S"
end
{% endcodeblock %}

<br>

------------------------------------------------------------------------------

##### 下面是网上找的一些资料：

rails开发中的Oracle问题

1.关联查询时的条件字段要明确指定

碰到的问题为mysql下order by id正常, Oracle报错：OCIError: ORA-00918: column ambiguously defined

2.\`表名\`问题

mysql会给表名外加 \`\` (注意不是单引号), 手工加条件(如排序: order by \`departments\`.id)时,mysql正常，Oracle报错： OCIError: ORA-00911: invalid character

3.级联更新问题

has_one、has_many如未设置:dependent=>:delete/:delete_all，解除子项关联时，默认会去更新子项的外键为NULL，mysql下没问题，Oracle报错：OCIError: ORA-01407: cannot update

4.日期问题

2个短格式(yyyy-mm-dd)日期(一个从数据库取)直接相减求时间差得到相差天数, mysql下正常，oracle下出错, 可以采取date.to_s(:db).to_date的办法解决

5.修改字段

如果字段值不为空，oracle下不可修改字段类型，mysql下可以，可以用增加临时字段的方式解决。