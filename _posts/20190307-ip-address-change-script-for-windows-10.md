---
title: win10 改变 ip 地址脚本
date: 2019-03-07 
updated: 2022-05-30
tags: 
  - Win10
urlname: ip-address-change-script-for-windows-10
original: true
---
使用 PowerShell 脚本更改 Windows 10 的 IP 地址.
<!--more-->
平常带电脑去教室上课,然后要修改电脑的ip之类的挺麻烦的,在一些大佬的帮助下然后算是写的第一个脚本吧.
本教程是小白教程,初入门难免有很多错误.主要参考[微软官方的文档](https://docs.microsoft.com/zh-cn/previous-versions/windows/powershell-scripting/hh826150%28v%3dwps.620%29)来写的脚本.

首先要查一下需要修改网卡的ifIndex值,powershell(管理员)下执行:
~~~
Get-NetIPInterface -AddressFamily ipv4 | Sort-Object -Property InterfaceIndex 
~~~
![查看ifIndex](/picture/20190307-0.jpg)
比如我要修改的那个叫"以太网",也就是InterfaceAlias列里有个叫"以太网"的,左边ifIndex是19,
然后得记下来.

二是开始写脚本啦,新建一个lalala.ps1的文件,比如我需要把"以太网"的TCP/IPV4属性下的
- ip地址改成:111.111.111.111
- 子网掩码改为255.255.255.128
- 默认网关为:222.222.222.222
- 首选DNS为:333.333.333.333
- 备用DNS为:444.444.444.444
- 脚本根据官方文档说明就应该这么写:

~~~
New-NetIPAddress -InterfaceIndex 19 -IPAddress 111.111.111.111 -PrefixLength 25 -DefaultGateway 222.222.222.222
Set-DnsClientServerAddress -InterfaceIndex 19 -ServerAddresses ("333.333.333.333","444.444.444.444")
~~~
然后测试一下能否执行该脚本,若无法执行查询一下脚本执行权限:
~~~
Get-ExecutionPolicy
~~~
然后修改权限:
~~~
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
~~~
然后应该就可以执行了吧,再不行我也没办法了.
最后每次去教室,只需要执行一下脚本就行了噢~想使用得更轻松点的话把脚本丢到Path路径下.
嘿嘿嘿.对了,上面只是开启的脚本,关闭的还不会写,所以回到宿舍还是得手动设置一下动态获取ip和dns.

参考资料:[微软官方文档](https://docs.microsoft.com/zh-cn/previous-versions/windows/powershell-scripting/hh826150%28v%3dwps.620%29)








