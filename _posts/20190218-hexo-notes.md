---
title: Hexo 笔记
date: 2019-02-18 16:47:58
updated: 2022-05-30
tags:
  - Hexo
  - Note
urlname: hexo-notes
original: false
description: Hexo 常用的一些命令与模板.
---
Hexo 常用的一些命令与模板.
<!--more-->

请以官网为主.

# Cues

## Hexo
列出之前的文章:
~~~
hexo list post
~~~
新建文章:
~~~
hexo new "article_title"
~~~
生成静态文件:
~~~
hexo g
~~~
启动本地服务器:
~~~
hexo s
~~~
部署远程:
~~~
npm install hexo-deployer-git --save
hexo d
~~~
## 文章编辑
标题:
~~~
# 一级标题
## 二级标题
...
###### 六级标题
~~~
引用:
~~~
> 
~~~
无序列表(-/*):
~~~
- list1
- list2
~~~
有序列表:
~~~
1. list1
2. list2
~~~
层级列表:
~~~
- list1
[tab]- list2
[tab][tab]- list3
~~~
*斜体*:
~~~
*Italic*
~~~
**粗体**:
~~~
**Bold**
~~~
[超链接](/2019/hexo-notes/):
~~~
[content](link_address)
<a href="目标文件路径及全称" target="_blank">链接文本</a>
~~~
图片:
~~~
![alt](link_address)
<img src="link_address" width=450 alt="alt" />
~~~
空格:
~~~
&nbsp;
~~~
查看更多:
~~~
<!--more-->
~~~
代码块:
```
~~~
your_code
~~~
```
# Summary

参考资料:
[标记语言Markdown的基本语法](https://www.jianshu.com/p/4b22ac88810c)