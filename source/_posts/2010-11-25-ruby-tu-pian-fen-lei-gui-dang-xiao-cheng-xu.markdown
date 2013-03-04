---
layout: post
title: "ruby 图片分类归档小程序"
date: 2010-11-25 10:50
comments: true
categories: 
- Ruby{ruby}
---

需求简述：

1 处理某个目录下EMS快递单扫描图片，文件名格式：“20100702111538015-EF000000000CS.jpg”。

2 连接SQL Server 数据库，表中已有EMS编号，将匹配的图片移动到指定目录。重复扫描的图片只保留最新一张，其余错误文件移动到指定目录。

3 将匹配正确的图片名称存储到数据库。

4 程序定时执行，并记录日志。

程序代码如下：

<!-- more -->

{% codeblock Ruby代码: ems.rb lang:ruby%}
require "mssql.rb"
require "FileUtils"
require 'yaml'

puts "-----------------------------------------------------------------"
puts Time.now.strftime("%Y-%m-%d  %H:%M:%S") + "  程序运行"

count = 0
error_count = 0  #处理失败总数

config = YAML.load(File.open("config.yml"))  #加载配置文件 config.yml

#获取配置文件中的值
host = config["db"]["host"]
dbname = config["db"]["dbname"]
username = config["db"]["username"]
password = config["db"]["password"]

path = config["path"]

db = MssqlDb.new(host,dbname,username,password)   #数据库连接，四个参数分别为主机名、数据库名称、用户名、密码
picture_names = []
if db.open  #判断如果数据库连接成功才执行下面的操作
  FileUtils.mkdir_p "#{path}/pictures"  #在该目录下创建文件夹
  FileUtils.mkdir_p "#{path}/files"  #在该目录下创建文件夹
  FileUtils.mkdir_p "#{path}/error_files"  #在该目录下创建文件夹
  FileUtils.cd        "#{path}/pictures"
  #Dir.chdir("#{path}/pictures")#将当前目录改为 d:\ems\pictures

  picture_names1 = Dir.glob('*.jpg') #获取所有后缀为 .jpg 的文件的文件名
  count = picture_names1.length
  picture_names3 = []

  picture_names1.each do |p|
    if p.length != 35
      error_count +=1
      FileUtils.mv p,"#{path}\\error_files"   #将图片移到 path 目录下的 error_files 中
      puts "#{p} 名称错误"
    end
  end

  picture_names1 = Dir.glob('*.jpg') #获取所有后缀为 .jpg 的文件的文件名
  picture_names2 = Dir.glob('*.jpg') 

  picture_names1.each do |p1|
    picture_names2.each do |p2|
      if(p1 != p2 and p1[18,13] == p2[18,13])
        picture_names2.delete(p1)
      end
    end
  end

  picture_names3 = picture_names1 - picture_names2

  picture_names3.each do |p3|
    error_count +=1
    FileUtils.mv p3,"#{path}\\error_files"   #将图片移到 path 目录下的 error_files 中
    puts "#{p3} 重复"
  end

  picture_names = picture_names2

  if picture_names.length != 0 #如果names不为空
    picture_names.each do |n|
      db.query("select CONVERT(VARCHAR(6), SendDate, 112) AS senddate from tb_ems where emscode= '#{n[18,17].delete('.jpg')}' ")  
      rows = db.data  #获取查询结果

      if (rows.length == 0)  #如果未找到相应的记录
        FileUtils.mv n,"#{path}\\error_files"   #将图片移到 path 目录下的 error_files 中
        puts "#{n} 数据库中未找到相应记录"
        error_count += 1
      elsif( rows.length == 1 and rows[0] [0]!= nil )  #如果找到相应的记录，并且 senddate 字段不为空
        sendtime = rows[0][0]
        FileUtils.mkdir_p "#{path}\\files\\"+sendtime #创建目录
        FileUtils.mv n,"#{path}\\files\\#{sendtime}"  #将图片移到新创建的目录下 
        #puts "#{n} 已经移动到 #{path}\\files\\#{sendtime}"
        db.execute("update tb_ems set filename = '#{sendtime+"/"+n}' where emscode= '#{n[18,17].delete('.jpg')}' ")
      elsif( rows.length == 1 and rows[0] [0]== nil )  #如果找到相应的记录，但是 senddate 字段为空
        FileUtils.mv n,"#{path}\\error_files"   #将图片移到 path 目录下的 error_files 中
        puts "#{n} 数据库中SendDate（发送时间）字段为空"
        error_count += 1
      else
        FileUtils.mv n,"#{path}\\error_files"   #将图片移到 path 目录下的 error_files 中
        puts "#{n} 数据库中存在多条记录"
        error_count += 1
      end
    end
  end
end

db.close #关闭数据库连接

puts  "共有#{count}项任务...错误#{error_count}"

puts Time.now.strftime("%Y-%m-%d  %H:%M:%S") + "  程序结束"
puts "-----------------------------------------------------------------\n\n\n"
{% endcodeblock %}


{% codeblock Ruby代码:mssql.rb lang:ruby%}
require "win32ole"

class MssqlDb

  def initialize(host,mdb,user,pass)  
    @host= host
    @mdb=@database= mdb
    @username= user
    @password= pass
    @connection = nil
    @data = nil
    @fields = nil
  end

  def open
    connection_string = "Provider=SQLOLEDB.1;User ID=#@username;password=#@password;Data Source=#@host,1433;Initial Catalog=#@mdb"
    @connection = WIN32OLE.new('ADODB.Connection')
    begin
      @connection.Open(connection_string)
      @isconnected = 1
    rescue
      @isconnected = 0
      puts "数据库链接失败 !!!"
      nil
    end
  end

  def query(sql)
    recordset = WIN32OLE.new('ADODB.Recordset')
    recordset.open(sql, @connection)
    @fields = []

    recordset.Fields.each do |field|
      @fields << field.Name
    end

    begin
      @data = recordset.GetRows.transpose
    rescue
      @data = []
    end
    recordset.Close
  end

  def execute(sql)
    @connection.Execute(sql)
  end

  def close
    if @isconnected == 1
      @connection.Close
    else
      puts "请检查程序配置是否正确 数据库访问是否正常"
    end
  end

  attr_reader :data, :fields

end
{% endcodeblock %}



{% codeblock YAML代码:config.yml lang:yml %}
db:
  dbname: test        # 数据库名称
  username: sa        # 用户名
  password: sa        # 密码
  host: localhost     # 主机名

  path: D:\ems        #文件路径
{% endcodeblock %}
