---
typora-copy-images-to: media
categories:
  - linux
---

#### bash shell命令

#### 程序管理

##### ps

报告单前系统的进程状态

###### **语法**

```
ps 选项
```

###### **选项**

```shell
-A : 显示所有程序
```

###### 实例

```shell
ps -ef | grep ssh # ps 与grep 常用组合用法，查找特定进程
ps -C nginx # 通过名字或命令搜索进程
```

#### 网络管理

##### ip

网络配置工具 

###### 语法

```shell
ip (选项)(参数)
```

###### 实例

```shell
ip link show # 显示网络接口信息
ip link set eth0 up #开启网卡
ip link set ehh0 down
ip addr show # 显示网卡ip信息
```

***

##### firewall-cmd  

Linux 上新用的防火墙软件，跟iptables差不多

###### 语法

```shell
firewall-cmd [选项 ]
```

###### 实例

```shell
# 安装firewalld
yum install firewalld firewall-config

# 启动,状态查看,停止,禁用
systemctl start|status|disable|stop firewalld 

```



###### 端口管理

```shell
# 打开443/TCP端口
firewall-cmd --add-port=443/tcp

# 永久打开3690/TCP端口
firewall-cmd --permanent --add-port=3690/tcp
# 使用 --permanent参数 需要reload一下，临时打开的端口erload后失效
firewall-cmd --reload

# 查看防火墙,添加的端口可以看到
firewall-cmd --list-all
```

 


