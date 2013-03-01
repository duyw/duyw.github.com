---
layout: post
title: "virtualbox中虚拟硬盘扩容"
date: 2011-04-04 17:17
comments: true
categories: 其他
---


VirtualBox中虚拟硬盘扩容（vdi或者vmdk文件）扩容：

　　今天在使用虚拟机的时候发现分配的硬盘空间不足，需要扩大容量，最简单的办法就是重新创建一个虚拟硬盘，然后挂载到虚拟系统中即可，考虑到虚拟系统的备份迁移等问题，我想直接将vmdk格式的虚拟硬盘扩容，最后操作成功，记录在此。

　　其实创建和安装虚拟系统的时候可以将虚拟容量设置的大一些，比如可以设置50G，这50G动态扩大的，虚拟操作系统真正使用了多少，文件在外面系统中就占用多大的空间，这个文件大小最大限度是50G，所以不必担心会浪费的硬盘资源。

　　如果你使用的是VMWare，将已有虚拟系统扩容则直接一条命令就可以搞定，方法google便知。

　　但是VirtualBox就没那么方便了，因为VirtualBox不能改变已经创建的虚拟硬盘的大小，这一点很不人性化，但总是有办法的，操作的思路是先新建一个容量较大的vdi虚拟硬盘，然后将现有vdi虚拟硬盘的数据复制过去，然后把新建的虚拟硬盘挂载到虚拟系统中将原来的虚拟硬盘删除即可，复制的时候使用VBoxManage的 clonevdi 命令即可。

　　需要提醒的是启动虚拟系统后查看硬盘大小并没有改变，需要在虚拟系统中创建分区分配盘符。

　　VirtualBox与VMWare创建的虚拟系统都是vmdk格式的， 此外VirtualBox还支持vdi格式，如果你现有虚拟系统是vmdk格式的，那么要转换成vdi，然后将内容复制到新的vdi虚拟硬盘中，我找到两种方法可以实现转换：

<!-- more -->

### 方法一：vmdk —> bin —> vdi

从vmdk转换到bin需要下载 QemuManager安装工具，操作示例：

vmdk —> bin

```
 "d:\Program Files\QemuManager\qemu\qemu-img.exe" convert "E:\virtualOS\server2003\win2003.vmdk" -O raw "E:\virtualOS\server2003\win2003.bin"
```

bin —> vdi

```
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe" convertfromraw "E:\virtualOS\server2003\win2003.bin" "E:\virtualOS\server2003\win2003.vdi"
```

### 方法二：直接转换

但是我在操作的过程中没有成功，不知道什么原因，你可以尝试一下：

```
VBoxManage clonehd source.vmdk target.vdi --format VDI
```

另外如果将vdi转换成vmdk：

```
VBoxManage clonehd source.vdi target.vmdk --format VMDK
```

通过前面的操作，接下来将原来的vid虚拟硬盘数据复制到新的vid硬盘，示例：

旧vdi —> 新vdi

```
"D:\Program Files\Oracle\VirtualBox\VBoxManage.exe"  clonevdi "E:\virtualOS\server2003\win2003.vdi"  "E:\virtualOS\server2003\server2003.vdi" --existing
```