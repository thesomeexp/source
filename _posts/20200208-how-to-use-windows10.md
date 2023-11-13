---
title: 如何使用 windows10
date: 2020-02-08
updated: 2022-05-30
tags:
  - Win10
urlname: how-to-use-windows10
original: true
---
使用 Windows 10 的指南. 正确使用姿势是什么? 如何解决和避免 Windows 的一些问题? 
<!--more-->
很多人因为windows的问题找我帮忙, 其实我哪里忙得过来慢慢回答这个庞大的话题, 只好写一篇, 希望你们能从这里找到解决办法. 如有错误请各位大佬轻点指出. 
# 关于系统
首先是关于安装系统, 强烈建议各位从官网或者itellyou获取相应的系统镜像, 我也写过有关[如何安装win10LTSC](/post/install-win10-ltsc/)的文章, 我还是推荐你们使用微软推荐的安装方法, 请选择适合自己的系统镜像下载并安装. 这里推荐安装较新版本的win10, 旧版本系统在失去微软支持后系统漏洞很容易被恶意软件以及黑客利用. (ref: [2016/11/2微软宣布全面停售OEM版Win7/8.1系统](https://www.ithome.com/html/win7/269040.htm))
## 为什么不用PE, ghost之类的方式安装? 其他地方下载的镜像可以用吗? 
我本人不了解PE, ghost的安装机制, 不确定这种方式安装的系统安全性. 同样, 来自非官方的镜像的安全性是未知的. 
## 如何分区?
系统盘一般建议放在比较快的硬盘, 如何分区看个人, 善用搜索. 
## 如何激活我的windows?
经济实力允许的范围内请支持正版(去微软官网购买), 像我这样的贫穷屌丝也可以选择不激活. 你也可以选择冒着电脑被病毒感染的风险在网上下载各种激活工具. 

# 关于软件
个人建议所有软件都在微软商店下载, 在安装和使用软件时请谨慎选择在UAC(User Account Control)面前点击'是', 也就是win10在安装一些软件时弹出来的这样的窗口:
![UAC窗口](/picture/20200208-0.png)
如果点了'是', 该程序就能在你的电脑为所欲为. 很多网上下载的程序都会这样, 给电脑装一大堆无用软件甚至修改系统相关配置信息.如果你确实需要安装这个软件, 那就承担相应的后果. 如果你和我一样装软件有UAC强迫症, 那么可以[使用Scoop](/post/how-to-get-started-with-scoop-quickly)来安装软件. 除了安装好, 如果对软件不信任, 那可以新建一个账户来运行, 或者丢虚拟机中运行.
~~~
runas /user:"USERNAME" /savecred "Full path of file"
~~~
## 我下载的软件默认安装在哪里?
一般自己下载的软件都可以选择安装路径, 而一位[不愿意透露的大佬](https://www.v2ex.com/member/lm902)跟我说默认他们的路径是: 
- 大量数据放在~/AppData/Local
- 用户程序放在~/AppData/Local/Programs
- 临时文件放在~/AppData/Local/Temp
- 用户数据放在~/AppData/Roaming
- 所有用户共享的数据放在C:/ProgramData
而\~指的是系统的用户路径, 如果你按照上面的方式安装了系统, 那么你的路径就是: C:\Users\\{你的用户名}\我的话一般都会自己新建文件夹放在~/program/下, 因为上面那些目录存了很多东西非常乱. 
如果要清理垃圾可以翻一下上面的目录.
如果程序有管理员权限, 他们还可以放在C:\Program Files\下. 
(ref: [为什么安卓移动的应用缓存被扔的到处都是，而不是像Windows那样放到Program Files里？](https://www.zhihu.com/question/318988385/answer/651195330)

# 关于卡顿
在明显感觉到电脑卡顿的时候, 可以提前打开电脑的 任务管理器 , 点击性能面板, 你就能了解到当前电脑硬件的一些情况, 下面是我电脑的情况:
![我的电脑情况](/picture/20200208-1.png)
因为我运行了一些软件, 可以看到内存占用率比较高, 而我在这种使用情况下CPU的占用率不是很高, 所以对于我的日常使用而言我的电脑配置是足够的. 如果你的电脑某项占用率超过90%那么可以考虑升级硬件或者修改软件配置. 
## 我是否需要修改注册表或者关闭系统服务来提高系统的运行速度?
我个人不建议这么做, 修改注册表信息和关闭一些系统服务可能是危险的, 比如导致系统崩溃. 

# 关于组件缺失
经常玩游戏的朋友或者安装大型软件的时候可能会遇到一些xx组件缺失的错误提示, 个人建议在搜索相关信息的时候, 在关键词后面加上 site:microsoft.com 优先查询微软官网提供的信息. 

# 我的习惯
通过上面你可以看到我的电脑配置比你们的还低, 多么瞩目的2代i3. 
我日常使用觉得不错的软件: 
解压: 7-zip
浏览器: firefox(别下载到某智版本)[https://www.mozilla.org/en-US/firefox/all.html](https://www.mozilla.org/en-US/firefox/all.html), brave, chrome, 新版Edge, Tor
截屏: Snipaste
录屏: OBS Studio
视频播放器: VLC
文本编辑器: Notepad++, vscode
pdf查看器: SumatraPDF
安全防护: win10自带的Windows Defender
虚拟机: Hyper-V, (自己用Vmware感觉方便点)
密码管理: KeePass
磁盘加密: win10自带的BitLocker, Veracrypt
输入法: RIME
其他: 7+ Taskbar Tweaker, DocFetcher, WinMerge, Rufus
以上软件大多可绿色安装, 如果你有更好的软件推荐给我, 那么请联系我, 谢谢. 

# 最后
我写得不是很详细, 很多细节都没有加, 我希望各位能够善于利用搜索引擎, 解决你们遇到的
各种问题. 

我还写了[如何使用 Android](/post/how-to-use-android), 感兴趣就来看看呗.