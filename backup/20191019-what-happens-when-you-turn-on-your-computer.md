---
title: 打开电脑的时候你电脑发生了什么
date: 2019-10-19
updated: 2020-04-30
tags:
  - Note
  - Win10
urlname: what-happens-when-you-turn-on-your-computer
original: true
---
记录一下电脑的大致启动过程.
<!--more-->
# Cues

BIOS/UEFI, MBR扇区, EFI系统分区, bootloader
# Notes
## 1.按下电源

按下你的电源按钮,你的计算机电源(也就是你接通交流电的那个小电源箱子)就会给你电脑的其他组件供电. CPU初始化自己,然后加载BIOS,如果是现代的电脑会加载UEFI,你可以把UEFI当做BIOS的升级版本.
## 2.加载BIOS/UEFI

然后BIOS/UEFI一般从主板上加载配置文件(这些一般存在CMOS里,内容一般是BIOS/UEFI的配置和时间,台式机里默认有纽扣电池给它供电,笔记本很多用nvram,台式机还保留传统,听说是为了翻车初始化方便.),然后BIOS/UEFI检查和初始化你的硬件(硬件自检Power On Self Test,如果硬件自检出现问题,主板就会发出不同含义的的蜂鸣声或是显示错误代码),然后显示品牌LOGO,在这里你可以按按键进入BIOS/UEFI的设置页面.
## 3.选择可引导设备

然后BIOS/UEFI去找引导设备,如果有多个可引导设备它会根据顺序来开始.这时候BIOS/UEFI的工作就结束了,他把剩下的工作交给下面的引导程序.
如果是使用BIOS引导启动: 

BIOS会读取这个可引导设备的MBR扇区(也就是磁盘低级地址的一些数据),这些数据包含加载其余操作系统的代码,这个也叫引导加载程序(bootloader),BIOS执行这个引导加载程序,然后开始引导实际的操作系统.
如果是使用UEFI引导启动: 

UEFI仍然可以从旧的MBR分区里引导操作系统,但是一般使用存在EFI系统分区的EFI可执行文件来引导操作系统,所以数据不一定只能存在低地址.UEFI默认开启Secure Boot功能,用来检查bootloader是否被恶意软件篡改.
## 4.引导操作系统

bootloader是一个小程序用来引导剩余的操作系统,Windows下使用名为Bootmgr.exe的引导程序,Linux下则是GRUB,Mac下叫boot.efi

然而,bootloader无法靠自己加载整个操作系统,在Windows的bootloader里,他先去找和启动Windows系统加载器(Windows OS Loader),这个加载器会加载一些启动操作系统核心的必要硬件驱动,然后该核心就会加载系统注册表(存放Windows和很多程序的配置设定的数据库)进内存和加载一个标记了“BOOT_START”的格外硬件驱动,这意味着这些内容应该在引导时加载.然后核心再启动会话和加载格外格外驱动的管理程序(Smss.exe),这个过程继续进行,Windows加载后台服务和欢迎屏幕,然后你就可以通过这个屏幕登录.

比如:Windows10的bootloader会先读取ESP里的BCD文件,BCD文件是Windows注册表格式,包含了操作系统列表,然后再查找BCD文件里写的目前活跃的操作系统,启动它(也就是winload),winload在加载完各种驱动和初始化完各种设备以后会再读一遍BCD看有没有别的系统,如果有别的系统就会显示操作系统选单,没有别的系统,如果连续启动失败两次就会开始尝试自动恢复,然后再去执行smss,smss会以nt authority\system的身份运行logonui,也就是登录屏幕.

所谓的开机启动程序,事实上是在你登录进你的账户后才加载的.
# Summary

启动Windows
BIOS：bootsect→bootmgr文件→winload.exe文件
UEFI：bootx64.efi文件→bootmgr.efi文件→winload.efi文件

参考资料:
[howtogeek:what-exactly-happens-when-you-turn-on-your-computer](https://www.howtogeek.com/398493/what-exactly-happens-when-you-turn-on-your-computer/)
[Microsoft:boot-and-uefi](https://docs.microsoft.com/zh-cn/windows-hardware/drivers/bringup/boot-and-uefi)
[【科技百年】电脑开机的时候都发生了些什么？](https://www.bilibili.com/video/av12811563/)




