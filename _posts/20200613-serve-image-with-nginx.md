---
title: Nginx 搭建图片服务器
original: false
date: 2020-06-13
updated: 2022-05-30
tags: 
  - Nginx
  - Docker
  - Note
urlname: serve-image-with-nginx
---
在电脑虚拟机中使用 Docker 和 Nginx 搭建图片服务器的示例.

<!--more-->

// docker挂载
~~~
mkdir -p /home/nginx/www /home/nginx/logs /home/nginx/conf /home/nginx/www/image /home/nginx/www/image/ /home/nginx/www/image/info_detail
touch /home/nginx/conf/nginx.conf
~~~

修改配置文件nginx.conf: 
~~~
user  root root;
worker_processes  1;
 
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
 
 
events {
    worker_connections  1024;
}
 
 
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
 
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
 
    access_log  /var/log/nginx/access.log  main;
 
    sendfile        on;
    #tcp_nopush     on;
 
    keepalive_timeout  65;
 
    #gzip  on;
 
    include /etc/nginx/conf.d/*.conf;
 
    server {
        listen 80;
        server_name localhost;
        
        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }
        
        location /images/ {
            root   /usr/share/nginx/html/;
            autoindex on;
        }
    }
}        
~~~
创建并运行Nginx容器, 443为https端口. 但这里没有用到https 
~~~
docker run -d -p 80:80 --name nginx-image-server \
-v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /home/nginx/www:/usr/share/nginx/html \
-v /home/nginx/logs:/var/log/nginx \
nginx
~~~
这时候nginx服务器就已经搭建好了, 可以在/home/nginx/www下创建index.html, 然后通过ip可以直接访问这时候就成功了. 

安装vsftpd
~~~
yum -y install vsftpd
~~~

添加用户
~~~
useradd infoimage // 创建用户
passwd infoimage // 设置用户密码为下面这段
45g2rVaZ8R9GGka8
~~~

修改配置文件
~~~
vi /etc/vsftpd/vsftpd.conf
~~~

# 配置文件默认为YES，即支持匿名访问，设置为不支持
~~~
anonymous_enable=NO
~~~

# 在最后添加这3行
~~~
pasv_enable=YES
pasv_min_port=30000
pasv_max_port=30009
~~~

保存退出后设置用户访问权限
~~~
chown infoimage /home/nginx/www/image
chmod 777 -R /home/nginx/www/image
~~~

启动vsftp服务
~~~
systemctl start vsftpd.service
~~~

然后关闭防火墙或者开启一些端口(开启的话要开挺多的...测试就关闭防火墙来测试了)

~~~
systemctl status vsftpd.service
systemctl stop vsftpd.service
~~~

关闭用户鉴权
~~~
vi /etc/pam.d/vsftpd
~~~
注释掉/etc/pam.d/vsftpd文件里这一行：
~~~
#auth required pam_shells.so
~~~
重启
~~~
systemctl restart vsftpd.service
~~~

头像路径
~~~
chown infoimage /home/nginx/www/head
chmod 777 -R /home/nginx/www/head
~~~

ftp连接(账户密码)
~~~
192.168.76.56:21
infoimage
45g2rVaZ8R9GGka8
~~~

如果连接上后不显示文件夹
解决办法：
~~~
1、vi /etc/selinux/config
2、 将SELINUX=enforcing 改为 SELINUX=disable
3、使改变的内容生效  sudo setenforce 0
4、重启vsftpd
~~~

上传图片到image后, 图片示例地址(虚拟机ip): 
http://虚拟机ip/image/20200505_14-36-29.jpg

# 服务出问题的终极解决办法

1.查看防火墙状态并关闭: 
~~~
// 查看防火墙状态
systemctl status firewalld.service
// 查看已开放的端口(默认不开放任何端口)
firewall-cmd --list-ports
// 关闭防火墙
systemctl stop firewalld.service
~~~
2.查看docker里的nginx容器运行状态, 重启nginx容器
~~~
// 查看运行中的容器
docker ps
// 停止运行中的容器
docker stop containner_id
// 启动停止的容器
docker start containner_id
// 或者一条命令重新启动容器
docker restart containner_id
~~~
3.重启整个docker进程, 再继续运行相应容器
~~~
// 重启docker进程
systemctl restart docker
// 查看所有容器(包括未运行)
docker ps -a
// 启动相应容器
docker start containner_id
~~~
4.删除创建的容器, 重新从镜像生成一个容器并运行
~~~
// 删除之前停止的容器
docker rm containner_id
// 重新从镜像生成一个容器并运行
docker run -d -p 80:80 --name nginx-image-server \
-v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /home/nginx/www:/usr/share/nginx/html \
-v /home/nginx/logs:/var/log/nginx \
nginx
~~~

有时候上面的配置都没问题, 就是ping不通, 可以禁用对应的网卡再启用试试. 

参考: 
https://www.cnblogs.com/lyuzt/p/12575390.html
