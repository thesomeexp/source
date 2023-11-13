---
title: Scoop 笔记
original: false
date: 2020-05-07
updated: 2022-11-16
tags: 
  - Win10
urlname: scoop-notes
---
Scoop 是一款非常好用的 Windows 包管理器. 简单记录下使用指南.
<!--more-->
可以使用 Scoop 来安装一些不需要 UAC 的软件. 当然你也可以使用微软官方的 WinGet.
首先Powershell这里要修改成允许远程脚本执行: 
~~~
set-executionpolicy remotesigned -scope currentuser
~~~
然后执行远程的脚本进行安装: 
~~~
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
~~~
安装完测试一次下: 
~~~
scoop help
~~~
添加一个软件比较多的bucket: 
~~~
scoop bucket add extras
~~~
查看所有bucket: 
~~~
scoop bucket known
~~~
添加一个大佬维护的bucket: 
~~~
scoop bucket add dorado https://github.com/h404bi/dorado
~~~
搜索微信: 
~~~
scoop search wechat
~~~
安装: 
~~~
scoop install dorado/wechat
~~~

**数据隔离**

可以使用 run as different user 方式运行, 利用 windows 的多用户机制来实现数据隔离.

参考: 
[少数派: 「一行代码」搞定软件安装卸载，用 Scoop 管理你的 Windows 软件(https://sspai.com/post/52710](https://sspai.com/post/52496)
[少数派: 给 Scoop 加上这些软件仓库，让它变成强大的 Windows 软件管理器](https://sspai.com/post/52710)
[Scoop官方rep](https://github.com/lukesampson/scoop)
[dorado](https://github.com/h404bi/dorado)
