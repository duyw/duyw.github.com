---
layout: post
title: "Ubuntu12.04 安装 Sublime Text 2 "
date: 2013-04-04 22:41
comments: true
categories: 
- Ubuntu{ubuntu}
- Sublime Text 2{sublime_text_2}
---

Sublime Text 2 是一款非常优秀的编辑器，可以媲美苹果系统下的TextMate，用Sublime Text 2 进行RoR开发是一件很惬意的事，在Ubuntu12.04系统下安装步骤如下：

1. 到[官网](http://www.sublimetext.com/2)下载软件包： “Sublime Text 2.0.1.tar.bz2”。

2. 复制到安装目录。

3. 解压缩。

4. 进入目录，创建desktop文件（sublime-text.desktop），并赋予可执行权限，启动sublime text 2 就直接运行该文件，这样就可以锁定在启动器，内容如下：


		#!/usr/bin/env xdg-open

		[Desktop Entry]
		Name=Sublime Text 2
		Comment=Sublime Text 2
		Exec=/home/ivan/apps/"Sublime Text 2"/sublime_text
		Icon=/home/ivan/apps/Sublime Text 2/Icon/256x256/sublime_text.png
		Terminal=false
		Type=Application
		Categories=Application;Development;
		StartupNotify=true
		Name[zh_CN]=sublime-text
	如果将文件复制到`/usr/share/applications/`目录下，就可以在Dash中被搜索到。

<!-- more -->

5. 破解：

	首先备份安装目录下的sublime_text文件`cp sublime_text sublime_text.bak`

	使用vim打开：`vim sublime_text` (如果没有安装vim，可以通过命令`sudo apt-get install vim`安装。)

	将文件转化成16进制,在vim中输入：`:%!xxd`

	查找要修改的地方,在vim中输入： `/4333 3342` 

	将3342修改为3242

	将文件转换回去,在vim中输入： `:%!xxd -r`

	保存文件、退出,在vim中输入： `:wq`

	打开sublime text 2 编辑器，将下面的Licence填入即可大功告成：

		—–BEGIN LICENSE—–
		China
		Unlimited User License
		EA7E-2861
		BE67D2175D3569FDAB9EB5340FAD2822
		E7B56B3397A76AA9FBE8AC3D3C65918B
		DFC28F2EA158140D9E07853D594818EB
		3A237B2E8E98ED257C269548F50EDA34
		EF0C7F72D8917DB538A0245E46BFD6B1
		85F4EDE331F253530ED67A5C19E92399
		04C5F4A1AF4AF3DB5EC49C1FEE17CA76
		7E369F8AAE4AC6C6E756B5882E1608B9
		—–END LICENSE—–
   

6. 创建链接，这样就可以直接在命令行中通过 sub 命令启动

	`sudo ln -s /home/ivan/apps/Sublime\ Text\ 2/sublime_text /usr/bin/sub`

7. 安装插件安装管理工具“package control”

	打开 Sublime Text 2，按下 Control + \` 调出 Console，输入以下内容，回车执行：

		import urllib2,os;pf='Package Control.sublime-package';ipp=sublime.installed_packages_path();os.makedirs(ipp) if not os.path.exists(ipp) else None;open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read())

8. 解决ubuntu12.04系统下中文输入问题：

	需要通过安装InputHelper插件来实现中文输入：

	`cd ~/.config/sublime-text-2/Packages/`

	`git clone https://github.com/xgenvn/InputHelper.git`

	也可以通过 package control 来安装.

	使用方法：重启 Sublime Text 2 ,通过快捷键 Shift ＋ Ctrl ＋ Z 来呼出输入窗口，可在其中输入汉字，输入完成直接回车即可。

9. 相关插件介绍

	待续...

