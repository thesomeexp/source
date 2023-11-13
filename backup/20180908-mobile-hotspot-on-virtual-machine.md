---
title: 使用虚拟机开热点
date: 2018-09-08
updated: 2022-05-30
tags: 
  - Virtual Machine
urlname: mobile-hotspot-on-virtual-machine
original: true
---
其实这件事捣鼓了两三个月了, 在 Ubuntu, CentOS, TinyCore 都不会用的情况下考虑回旧版的 Windows. <!--more-->
先上效果图吧. 
![成功效果图](/picture/20180908-2.jpg)
上工具:win10笔记本电脑 腾达U6无线网卡
鉴于VMware不能调用笔记本自带的网卡, 所以只能够外接一个网卡设备了.
# 1.安装Windows Xp Professional和VMtools
主要是虚拟机的安装, 网上都有教程.内存推荐128M储存4GB就好了, 联网方式选择NAT.
# 2.安装网卡驱动程序
可考虑网上下载或者用VMtools的文件共享功能.
# 3.分配网卡到虚拟机.
这样就可以开热点了.
## 4.酸酸乳局域网代理(扩展)
选项设置 --> 本地代理 --> 勾选 允许来自局域网的连接.
控制面板 --> 安全中心 --> 防火墙 --> 例外 --> 勾选 对应的代理程序.
开始运行 --> CMD --> 输入 ipconfig  --> 记录ip.
手机连接wifi后修改代理为手动, 修改代理服务器主机名为对应的ip, 修改代理服务器端口为1080(默认端口)
# 过程图
可以参考一下.
![虚拟机配置](/picture/20180908-0.jpg)
![手机配置](/picture/20180908-1.jpg)
































