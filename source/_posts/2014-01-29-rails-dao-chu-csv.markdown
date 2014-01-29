---
layout: post
title: "Rails 导出CSV"
date: 2014-01-29 20:21
comments: true
categories: 
- Rails{rails}
- CSV{csv}
---

rails项目中经常有生成Excel文件需求，完成这一功能的可选插件也很多，比较常用的有 roo ，spreadsheet 等，如果遇到导出大量数据的场景，这些插件就捉襟见肘了，捉襟见肘 因为速度实在无法忍受。比较好的解决法案是生成csv文件，这样做的好处是文件体积小，生成速度快，不受行数限制，此外csv文件可以用excel打开。

注意点：

1 导出的UTF-8格式的csv直接用excle打开会出现乱码，需要进行转码操作，如果在导出之前转码，速度也很慢，比较好的做法是生成以后再转码（可以用windows的文本编辑器另存为ANSI编码）。

2 尽量避免将大量数据读取到ruby对象中，应该使用`find_each`方法,`:batch_size`参数默认是1000，这个值的设置对速度也有很大影响，我笔记本测试350较快，应该和机器配置User对象复杂程度等有关,需要测试。

{% codeblock lang:ruby %}
User.find_each(:start=>5000,:batch_size => 350) do |u|
  # do something
end
{% endcodeblock %}

3 生成的csv的时候注意设置分隔符，目的是方便用excel直接打开
{% codeblock lang:ruby %}
CSV.generate(file_path, clo_sep = "\t", row_sep = "\r\n") do |data|
  data << ["姓名","电话"]
  Users.find_by_sql(sql).each do |user|
    data << [user.name,user.phone]
  end
end
{% endcodeblock %}

下面是从网上找到一个现成例子，使用了 fastercsv 生成csv：

{% codeblock 在String类中增加一些转换字符集的方法, lang:ruby %}
class String
  def utf8_to_gb2321
    encode_convert(self, "gb2321", "UTF-8")
  end
 
  def gb2321_to_utf8
    encode_convert(self, "UTF-8", "gb2321")
  end
 
  def utf8_to_utf16
    encode_convert(self, "UTF-16LE", "UTF-8")
  end
 
  def utf8?
    begin
      utf8_arr = self.unpack('U*')
      true if utf8_arr && utf8_arr.size > 0
    rescue
      false
    end
  end
 
  private
  def encode_convert(s, to, from)
    require 'iconv'
    begin
      converter = Iconv.new(to, from)
      converter.iconv(s)
    rescue
      s
    end
  end
end
{% endcodeblock %}

{% codeblock 导出CVS, lang:ruby %}
def export_csv
  require 'fastercsv'
  records = Amodel.find(:all, :limit => 10)
  csv_string = FasterCSV.generate(:col_sep => "\t", :row_sep => "\r\n") do |csv|
    csv << ['field_name1', 'field_name2', 'field_name3']
    records.each do |r|
      csv << [r.field1, r.field2, r.field3]
    end
  end
 
  File.open("export.csv","w") do |file|
    file.syswrite "FFFE".gsub(/\s/,'').to_a.pack("H*") + csv_string.utf8_to_utf16
  end 
end
{% endcodeblock %}

railscasts中有两个视频可以供学习：

[http://railscasts.com/episodes/362-exporting-csv-and-excel?autoplay=true](http://railscasts.com/episodes/362-exporting-csv-and-excel?autoplay=true)

[http://railscasts.com/episodes/396-importing-csv-and-excel?autoplay=true](http://railscasts.com/episodes/396-importing-csv-and-excel?autoplay=true)

其他资料：

[http://hlee.iteye.com/blog/356507](http://hlee.iteye.com/blog/356507)
