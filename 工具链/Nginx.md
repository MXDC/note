---
categories:
  - 工具链
---
#### Nginx源码安装配置
```shell
# 安装make
yum -y install gcc automake autoconf libtool make
# 安装g++
yum install gcc gcc-c++
```
先装pcre, zlib，前者为了重写rewrite，后者为了gzip压缩。
```shell
wget p://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.43.tar.gz

tar -zxvf pcre-8.37.tar.gz
cd pcre-8.43

./configure
make
make install

```

```shell
cd /usr/local/src
 
wget https://nchc.dl.sourceforge.net/project/libpng/zlib/1.2.11/zlib-1.2.11.tar.gz
tar -zxvf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure
make
make install
```
安装Nginx
```shell
cd /usr/local/src
wget [root@master zlib-1.2.11]# wget http://nginx.org/download/nginx-1.14.2.tar.gz
tar -zxvf nginx-1.14.2.tar.gz
cd nginx-1.14.2

./configure --prefix=/usr/local/nginx --conf-path=/etc/nginx
make
make install

```
启动NGINX
```
cd /usr/local/nginx/sbin
./nginx
```

#### yum安装Nginx
参考NGINX cookbook
1. create a file named /etc/yum.repos.d/nginx.repo that contains thefollowing contents:
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/OS/OSRELEASE/$basearch/
gpgcheck=0
enabled=1
```
2. **Alter the file, replacing OS at the end of the URL with rhel or centos, depending on your distribution. Replace OSRELEASE with 6 or 7for version 6.x or 7.x, respectively.** eg:`baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/`

3. Then, run the followingcommands:
```
yum -y install nginx
systemctl enable nginx

systemctl start nginx
firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --reload
```

#### Nginx 文件结构

```
/etc/nginx --主配置文件目录
|-- conf.d
|   `-- default.conf
|-- fastcgi_params
|-- koi-utf
|-- koi-win
|-- mime.types
|-- modules -> ../../usr/lib64/nginx/modules
|-- nginx.conf -- 配置文件入口
|-- scgi_params
|-- uwsgi_params
`-- win-utf
```

*/etc/nginx/*

​		The */etc/nginx*  directory is the default  configuratio root forthe **NGINX** server

*/etc/nginx/nginx.conf*

​		The  */etc/nginx/nginx.conf* file is the default configuration entry point used by the  **NGINX**  service

*/etc/nginx/conf.d/* 
		The  */etc/nginx/conf.d/* directory contains the default HTTPserver configuration file.

*/var/log/nginx/*
The  */var/log/nginx/*  directory is the default log location for **NGINX**  . Within this directory you will find an access.log file and an error.log file.

#### Nginx 命令

`nginx -h `   查看帮助菜单

`nginx  -v `   查看版本

`nginx  -V  `  version, build information, and configuration arguments, which shows the modules built in to the NGINX binary. 

`nginx -t` 测试配置文件

`ngix -T`  测试并输出配置文件

`nginx -s signal ` 
The -s flag sends a signal to the NGINX master process. You can send signals such as `stop, quit, reload, and reopen`. The stop signal discontinues the NGINX process immediately. The quit signal stops the NGINX process after it finishes processing inflight requests.**The reload signal reloads the configuration**.The reopen signal instructs NGINX to reopen log files.

#### Nginx 静态资源目录

*/ect/nginx/conf.d/* 目录中的默认配置文件*default.conf* 

```nginx
server {
listen 80 default_server;
server_name www.example.com;
location / {
    root /usr/share/nginx/html;
    # alias /usr/share/nginx/html;
    index index.html index.htm;
	}
}
```

> note 如果配置没有将此上下文定义为default_server, NGINX将只在HTTP Host header与server_name指令提供的值匹配时才将请求定向到该服务器。location后面设置映射的路径，可以使用正则过滤URL。当查找被请求的文件时，**请求的URI被附加到root指令的值**。最后，index指令为NGINX提供了一个默认文件，或者一个要检查的文件列表，以防URI中没有提供进一步的路径。

