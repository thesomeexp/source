---
title: Pixel 1 sideload ota 更新
date: 2018-10-20 
updated: 2022-05-30
tags:
  - Phone
urlname: sideload-ota-updates-on-google-pixel
original: true
description: 记录一下使用 adb 工具 sideload ota 的过程.
---
记录一下使用 adb 工具 sideload ota 的过程. <!--more-->
## 注意:最好先备份手机的数据,8.1OTA到9.0后有些问题,所以最后我OTA完还重置了一下.
## 1.配置adb fastboot环境
以windows系统为例,手机连接电脑,打开手机的USB调试允许Debug,配置好环境后,在adb在的路径下按住shift再右键,点击在此处打开Powershell窗口. 
然后执行命令:
<pre>./adb devices</pre>
![powershell查看到连接的手机](/picture/20181020-0.jpg)
如果List of devices attached下出现一串东西那连接应该就没问题了.
## 2.下载官方OTA包
Pixel官方OTA包下载地址:[https://developers.google.cn/android/ota#sailfish](https://developers.google.cn/android/ota#sailfish)
下载完后放到和adb工具同一目录的地方,也就是和adb.exe同目录.
## 3.手机进入recovery模式
直接在命令行窗口输入:
<pre>./adb reboot recovery</pre>
然后手机可能出现一个倒地的机器人和No Command,这时候同时按 [音量上] + [电源键]就出现选择的画面了(如果是安卓4.4以下就三个实体键一起按)(好久都没捣鼓过Pixel了, 这次是按了电源键然后再按下音量上),然后选择从OTA升级Apply update from ADB
之后执行:
<pre>./adb sideload 文件名.zip</pre>
这里的'文件名'是指你下载的那个OTA包的名字,要是名字长你可以改成update.zip之类的直接写update.zip代替就好.然后等待刷完重启就好.
## 写在最后:
8.1再刷OTA降回7.0的话会报错.
