---
title: 最小化安装Centos 7.5后配置 
date: 2018-11-01 17:44:46
categories: 
        - Linux
        - Centos
tags: 
    - Linux
    - Centos
---

1.配置网络
------

1.1 编辑网卡配置文件  

```
vi /etc/sysconfig/network-script/ifcfg-eth34
```


 1.2 修改下述配置  

```
ONBOOT = “yes”            #开机自启动  
BOOTPROTO = “static”           #static/dhcp  
IPADDR = “192.168.99.100”   #IP地址  
NETMASK = “255.255.255.0”  #子网掩码  
GATEWAY = “192.168.99.1”   #网关  
DNS1 = “202.103.44.150”       #DNS1  
DNS2 = “202.103.24.68”    #DNS2
```
<!--more-->

1.3 重启网络服务  
Systemcal restart network.service

2.修改主机名
-------

2.1查看主机名  
Hostname 或者 hostnamectl

2.2修改主机名  
hostnamectl set-hostname centos  
或者 vi  /etc/hostsname

3.配置yum源
--------

```
[root@zelvan ~]# vi /etc/yum.repo.d/Centos7.repo

```


4.安装常用软件
--------

 

```
[root@zelvan ~]# yum -y install net-tools vim gcc gcc-c++ wget bzip2 unzip
```
 

5.系统更新
------

```
[root@zelvan ~]# yum update
```