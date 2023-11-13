---
title: 快速操作笔记
original: false
date: 2020-05-29
updated: 2020-05-29
tags: 
  - Note
urlname: quick-operation-note
---
那些搜索过两次以上的解决办法. 
<!--more-->

# Linux

## CentOS7配置静态ip

~~~
/etc/sysconfig/network-scripts/ifcfg-ens33

BOOTPROTO=static
IPADDR=
NETMASK=
GATEWAY=
DNS1=
DNS2=
~~~

## CentOS7 ifconfig command not found

~~~
// 安装net-tools
sudo yum install net-tools
~~~

## CentOS7修改软件源: 
~~~
// 下面的操作结果还有点问题, 懒得搞了
cd /etc/yum.repos.d/
// 安装weget
yum install -y wget
// 下载CentOS 7的repo文件
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repop://mirrors.aliyun.com/repo/Centos-7.repo
// 或者
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
// 清除缓存
yum clean all
// 生成缓存
yum makecache
~~~

## CentOS7安装Docker

~~~
// 切换root用户
// 照着官网教程跑, 修改为阿里云的镜像
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
// 启动并加入开机启动
systemctl start docker
systemctl enable docker
~~~

## CentOS7防火墙

~~~
// 开启防火墙
systemctl start firewalld
// 关闭防火墙
systemctl stop firewalld.service
// 查看已开放的端口(默认不开放任何端口)
firewall-cmd --list-ports
// 开启80端口
// --zone(作用域), --add-port(端口和访问类型) , --permanent(永久生效) 记得重启
firewall-cmd --zone=public --add-port=80/tcp --permanent
// 重启防火墙
firewall-cmd --reload
// 禁止防火墙开机启动
systemctl disable firewalld.service
// 删除
firewall-cmd --zone= public --remove-port=80/tcp --permanent
~~~

## 进程

~~~
// 列出所有用户的
ps -aux
// 杀掉进程
kill -9 {pid}
// 后台运行
nohup ./frps -c frps.ini >/home/lighthouse/frp/frp_0.39.0_linux_amd64/log 2>&1 &
~~~

## CentOS7使用Docker

~~~
// 列出所有镜像
docker images
// 删除镜像(需要先停止容器)
docker rmi image_id
// 运行 --name(容器名) -i(交互模式) -t(tty打开终端) -d(后台运行)(可以合成-itd) -p 容器外:容器内(端口映射) image-name:tag(版本) 
docker run --name container-name -d image-name:tag
如:docker run --name myredis –d redis
// 查看运行中容器命令 -a(全部)
docker ps
// 停止容器
docker stop container_id
// 启动容器
docker start container_id
// 删除容器
docker rm container_id
// 进入容器
docker exec -it container_id /bin/bash
// 使用当前目录的 Dockerfile 创建镜像，镜像名字:版本号
docker build -t 名字:版本号 .
示例: docker build -t runoob/ubuntu:v1 .
// 启动时配置文件挂载 
示例: docker run -p 82:80 --name nginx1 -v /src/nginx/nginx.conf:/etc/nginx/nginx.conf -d nginx
// 拷贝文件进容器(覆盖)
示例: docker cp nginx.conf nginx1:/etc/nginx/nginx.conf

~~~
错误: 
WARNING: IPv4 forwarding is disabled. Networking will not work.
~~~
// 有些容器好像默认是ipv6, 需要指定ipv4, 或者修改系统配置:
 /usr/lib/sysctl.d/00-system.conf
// 中添加
net.ipv4.ip_forward=1
// 再重启
 systemctl restart network

 // mysql时区问题 
 # docker exec -it 5c02cf9db889 bash
# date
# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
退出重启docker
# exit
# docker restart 5c02cf9db889
~~~

## CentOS7网卡地址, 路径

~~~
/etc/sysconfig/network-scripts/
~~~


# Java

~~~
// 服务器运行jar定义输出
nohup java -jar getCimiss-surf.jar >consoleMsg.log 2>&1 &

tail -fn 50 nohup.out
~~~

# Docker

~~~
docker pull centos // 从镜像仓库下载镜像
docker images // 列出所有本地镜像
docker run -it {id}  // 运行某个容器 --name给它名字
docker run -it -p {外部暴露端口}:{容器内部端口} {name} // 小p是指定端口, 大P是随机端口

exit  // 关闭退出容器
Ctrl P Q  // 不关闭退出容器
docker attach {id} // 重新回到容器

docker start {id} // 启动容器

stop // 停止容器
kill // 强制停止

docker ps -l   // 上一次运行的容器
docker ps -n {n} // 上n次运行的

docker rmi {id} // 删除镜像
docker rm {id} // 删除已停止容器

docker run -d {name} // 运行在后台了, 如果它没有什么任务会自动关闭

docker logs -t时间 -f实时刷新 {id} // 查看容器日志

docker top {id} // 查看容器内进程

docker inspect {id} // 查看容器内部细节(盒子)

docker exec -t {id} {command} // 直接执行命令, 不需要进入容器

docker cp {id}:{容器文件路径} {宿主机路径} // 将文件拷贝到宿主机持久化

docker commit -a="作者名" -m="描述" {id} {命名空间/镜像名}:{version} // 就是保存为自己的镜像

// 容器数据卷(相当于外部硬盘, 用来持久化容器数据, 可以修改读写权限)
docker run -it -v {宿主机目录}:{容器内目录} {name}

docker inspect {id} // 查看容器详情

// DockerFile相当于描述Docker镜像的文件(添加卷或者命令等), 可以通过image+DockerFile生成新的image: 
docker build -f {DockerFile路径} -t {命名空间/镜像名}

// 容器继承, 继承的容器卷内容能实现共享. 容器之间配置信息的传递, 数据卷的生命周期一直持续到没有容器使用它为止. 

// 保留字指令: 
FROM 基础镜像, 当前镜像基于哪个镜像, 顶层是scratch
MAINTAINER 维护者的名字邮箱
RUN 容器构建时需要运行的命令
EXPOSE 当前容器对外暴露出的端口号
WORKDIR 登录容器后的默认路径
ENV 构建时设置环境变量
ADD 将宿主机目录下的文件拷贝+解压缩进镜像, ADD会自动解压tar
COPY 类似ADD但直接拷贝没有解压
VOLUME 容器数据卷, 用于数据保存和持久化工作
CMD 指定容器启动时要运行的命令, 可以有多个CMD命令, 但是只有最后一个命令会生效, CMD会被docker run之后的参数覆盖
ENTRYPOINT 和CMD一样, 都是在指定容器启动程序及参数(不会覆盖, 会被追加组合使用)
ONBUILD 父镜像在被子镜像继承后这个命令构建时会被触发

dockerfile示例: 
FROM centos // 父镜像
MAINTAINER xxxyyy<xxyyy@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim // 安装vim
RUN yum -y install net-tools // 安装网络工具

EXPOSE 80 // 暴露端口

CMD echo $MYPATH
CMD echo "success ===== ok"
CMD /bin/bash
构建示例: 
docker build -f /mydocker/Dockerfile -t mycentos:1.3 .

docker history {镜像id} // 列出镜像变更历史

docker配置代理示例: 
mkdir /etc/systemd/system/docker.service.d
vi /etc/systemd/system/docker.service.d/http-proxy.conf

[Service]
Environment="HTTP_PROXY=http://192.168.52.1:7890/" "HTTPS_PROXY=http://192.168.52.1:7890/"

systemctl daemon-reload
systemctl restart docker
~~~

## Docker使用Redis

~~~
// 进入容器
docker exec -it container_id /bin/bash

// 使用redis-cli, h-host, p-port, a-password
redis-cli -h 127.0.0.1 -p 6379 -a 123456

~~~

## Docker使用Mysql

~~~
// 忽略大小写敏感
docker run --name WinMysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7.30 --lower_case_table_names=1
~~~

# GIT

## 添加错文件

git reset HEAD XXX/XXX/XXX.java 就是对某个文件进行撤销了
然后再checkout就好了

# MySQL

清空表数据, id从1开始: 
~~~
truncate table TABLE_NAME
~~~

mysqldump 数据参数
~~~
-- --column-statistics=0 修复高版本 mysqldump 导出低版本数据
-- -d 表示不导出数据只导出表结构
"DATABASE_NAME" -d --column-statistics=0 --result-file="C:\\Users\\i\\Desktop\\{data_source}-{timestamp}-dump.sql"
~~~

# Navicat

## 导出数据字典

~~~
SELECT
TABLE_NAME '表名',
COLUMN_NAME '字段名',
COLUMN_TYPE '数据类型',
COLUMN_COMMENT '备注'
FROM
information_schema.COLUMNS
WHERE
TABLE_SCHEMA = 'admin' and table_name = 'sys_menu';
~~~

# Photoshop

对你没看错还有ps的. 

## 抠出照片的文字

选择 -> 色彩范围 -> 然后选择选'阴影', 下面选'灰度', 容易看些. 

# Windows 10

## 使用其他用户运行

~~~
runas /user:{USERNAME} /savecred {Full path of file}
~~~