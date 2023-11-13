---
title: 使用 VHD 建立 Win10 多重系统
date: 2019-11-03 
updated: 2022-05-30
tags:
  - Win10
  - Virtual Machine
urlname: install-dual-boot-windows-using-vhd
original: true
---
我只是想跑个鲁大师跑分, 又不想让鲁大师得到我使用系统的UAC.
<!--more-->
# Cues

VHD, 分区, 释放镜像
# Notes
运行diskpart工具(在cmd里直接输入diskpart)
![cmd里输入diskpart](/picture/20191103-0.png)

这里以UEFI+GPT引导方式的win10为例子, 执行list disk查看你的磁盘情况, 先选择你使用的磁盘, 查看磁盘分区情况, 我只有一个磁盘所以这里直接
~~~
select disk 0
~~~
![查看并选择磁盘](/picture/20191103-1.png)

然后查看该磁盘分区情况
~~~
list partition
~~~
![查看当前选择的磁盘分区情况](/picture/20191103-2.png)

正常情况下会显示四个分区,一个恢复分区(可以理解是存放windows官方系统修复工具的地方),一个99-200M左右的ESP分区(也叫EFI分区,因为系统启动时要执行里面的.efi程序,一般建议分区格式为fat32),一个保留分区(存放引导代码,引导配置数据库,还有BitLocker相关的东西),还有一个主分区.然后我们可以新建一个VHD文件了(可以键入HELP CREATE VDISK获取帮助):
~~~
create vdisk file=c:\VHDwindows10.vhd type=fixed maximum=25600
~~~
![创建虚拟磁盘文件](/picture/20191103-3.png)

我们把它放在我的C盘下(主分区), 文件类型为固定大小, 我就拿来跑个鲁大师所以我就设置25G就行了, 因为diskpart默认是以MB为单位, 所以这里我填了25*1024=25600.如果你需要文件动态扩展, 那type改成expandable就行. 

成功新建文件之后, 我们再将它附加到我们的电脑:
~~~
select vdisk file=c:\VHDwindows10.vhd
attach vdisk
~~~
![选择虚拟磁盘文件](/picture/20191103-4.png)
![连接虚拟磁盘文件](/picture/20191103-5.png)

用list disk命令就能看到我们附加的VHD了
![查看我们的虚拟磁盘文件](/picture/20191103-6.png)

然后选择该虚拟磁盘
~~~
select disk 1
~~~
转化为GPT格式
~~~
convert gpt
~~~
![选择虚拟磁盘, 并转换为GPT格式](/picture/20191103-7.png)

这时候使用list partition查看该磁盘分区情况, 可以看到它只有一个保留分区.
![查看虚拟磁盘分区情况](/picture/20191103-8.png)
接下来我们
为它新建两个分区, 分别是EFI分区和主分区.
~~~
create partition efi size=100
create partition primary
~~~
当我们再次查看分区情况时, 多了efi分区2和主分区分区3. 
![创建并查看分区](/picture/20191103-9.png)
如果使用list vol命令查看所有卷情况时, 新建的两个分区文件系统为RAW, 也就是没有给它分配文件系统. 
![查看卷情况](/picture/20191103-10.png)
这时候我们分别给新建的两个分区格式化文件系统. 这里建议EFI格式化为FAT32的. 具体原因可以看之前的博文.这里我的EFI分区是2, 主分区是3, 所以:
~~~
select partition 2
format quick fs=fat32

select partition 3
format quick fs=ntfs
~~~
![分配文件系统](/picture/20191103-11.png)

这时候我们再用list vol查看卷情况, 已经搞好文件系统了. 
![分配文件系统成功](/picture/20191103-12.png)

到此我们已经完成了一大半.
接下来我们挂载主分区到D-Z其中一个没有使用的盘符, 我这里用Y来做演示:
~~~
select partition 3
assign letter=y
~~~
![挂载磁盘](/picture/20191103-13.png)

然后就能在你的文件管理器看到挂载的卷了:
![在文件管理器查看磁盘](/picture/20191103-14.png)
删除装载点的话使用remove letter=y

接下来解压你下载的win10iso映像文件, 我下载的是LTSC并解压到c盘的cn_windows_10_enterprise_ltsc_2019_x64_dvd_2efc9ac2文件夹, 然后我们右键开始菜单, 使用管理员权限开启powershell, 然后释放镜像到VHD:
命令是这样的:
~~~
Expand-WindowsImage -ImagePath install.wim的路径 -ApplyPath VHD的盘符 -Index 1
~~~
然后我执行的命令:
~~~
Expand-WindowsImage -ImagePath "C:\cn_windows_10_enterprise_ltsc_2019_x64_dvd_2efc9ac2\sources\install.wim" -ApplyPath "y:\" -Index 1
~~~
![powershell释放镜像](/picture/20191103-15.png)

然后我们就得到这么一个文件夹了:
![释放完的磁盘文件](/picture/20191103-16.png)

接下来我们使用管理员权限打开CMD命令窗口, 使用bcdedit工具完成启动项的添加:
方便起见我们直接复制当前系统启动信息作为VHD的启动信息:
~~~
bcdedit /copy {current} /d "Windows-VHD"
~~~
![复制当前系统启动信息](/picture/20191103-17.png)

然后我们得到一串长长的标识符{2d9bf36e-fdff-11e9-af65-502b73c09826}, 当然我的标识符和你的标识符不一样, 我们通过这个标识符来修改启动信息, 使它指向我们的VHD文件:
~~~
bcdedit /set {2d9bf36e-fdff-11e9-af65-502b73c09826} device vhd=[c:]\VHDwindows10.vhd
bcdedit /set {2d9bf36e-fdff-11e9-af65-502b73c09826} osdevice vhd=[c:]\VHDwindows10.vhd
~~~
![根据标识符修改bcd设置](/picture/20191103-18.png)

然后我们输入bcdedit就能看到我们的启动信息了. 
![查看目前系统的bcd](/picture/20191103-19.png)

然后重启我们就能进入我们新建的VHD-win10了.
![选择操作系统](/picture/20191103-20.png)
# Summary
操作比较繁琐. 

参考资料:
[Expand-WindowsImage](https://docs.microsoft.com/en-us/powershell/module/dism/expand-windowsimage?view=win10-ps)
[Windows 7使用VHD建立Windows 10的多重開機系統 - 電腦王阿達](https://www.kocpc.com.tw/archives/95456)
[win10 bcdedit添加vhdx启动](https://blog.csdn.net/qq_20480611/article/details/47955063)
[bcdedit 设置 VHD文件启动系统](http://blog.sina.com.cn/s/blog_6b6e6bad0100zpke.html)
