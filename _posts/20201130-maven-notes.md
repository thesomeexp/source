---
title: Maven 笔记
date: 2020-11-30
updated: 2022-05-30
tags:
  - Java
  - Maven
urlname: maven-notes
original: false
description: Maven 笔记. 
---
Maven 笔记. 
<!--more-->
# Maven

## 1. 核心概念

### 1.1 约定

Maven其实就是约定了项目的一些结构: 
~~~
├─src
│  ├─main
│  │  ├─java  主程序的java文件
│  │  └─resources  配置文件
│  ├─test
│  │  ├─java  测试程序代码
│  │  └─resources  测试使用的配置
└─pom.xml
~~~

### 1.2 坐标(gav)

用于唯一定义一个Maven项目的, 主要由: 
~~~
<groupId></groupId>  组织名, 通常是公司或组织域名倒序+项目名
<artifactId></artifactId>  模块名, 通常是工程名
<version></version>  版本号
~~~

三部分组成. 

### 1.3 依赖(dependency)

想要导入相关的依赖包, 在pom.xml添加对应依赖的坐标: 
比如导入gson的依赖: 
~~~
<dependency>
  <groupId>com.google.code.gson</groupId>
  <artifactId>gson</artifactId>
  <version>2.8.6</version>
</dependency>
~~~

### 1.4 仓库(Repository)

存放jar包, Maven插件(其实也就是jar包)等的地方. 有本地仓库, 中央仓库, 私服等区分. 

### 1.5 命令

通过命令完成项目的构建. 
- mvn clean: 清理target目录
- mvn compile: 编译程序后放到target目录中
- mvn test: 单元测试, 使用junit测试src/main/java目录中的程序是否符合要求
- mvn package: 打包, 一般打包成jar或者war
- mvn install: 把jar, war安装到本机仓库中

### 1.6 生命周期

一个典型的Maven构建(build)生命周期的阶段: 
validate -> compile -> test -> package -> verify -> install -> deploy
一般直接执行后面的插件, 前面的生命周期也会被执行. 


### 1.7 依赖范围

dependency可以设置scope标签, 为其依赖范围: 
- compile: 默认范围, 也就是程序每个生命周期都需要. 
- test: 只在测试过程(test)中起作用的依赖. 
- provided: 提供者, 在编译测试时需要, 但是打包(package)安装(install)时不需要(比如说我要部署到tomcat, 这时servlet相关依赖可以不用, 因为环境提供了). 

### 1.8 常用属性

当一些依赖版本号类似时, 可以提取出来作为全局的属性. 
~~~
<properties>
  <spring.version>5.2.0</spring.version>
</properties>

<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-core</artifactId>
  <version>${spring.version}</version>
</dependency>
~~~

### 1.9 资源插件

默认情况下mvn compile命令会把src/main/resources下的文件拷贝到target/classes目录中, 对于src/main/java目录下的非.java文件不做处理. 如果需要处理就需要使用相关的插件. 