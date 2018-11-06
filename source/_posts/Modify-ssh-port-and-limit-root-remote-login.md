---
title: Centos 7 下修改ssh端口和限制root远程登录 
date: 2018-11-03 15:44:46
categories: 
        - Linux
        - Centos
tags: 
    - Linux
    - Centos
---

一、修改ssh端口
---------

第一步：修改SSH配置文件     （注意是sshd_config而不是ssh_config，多了个d）

    vim /etc/ssh/sshd_config

找到“#Port 22”，这一行直接键入“yyp”复制该行到下一行，然后把两行的“#”号即注释去掉，修改成：

    Port 22
    Port 17569

SSH默认监听端口是22，如果你不强制说明别的端口，”Port 22”注不注释都是开放22访问端口。上面我保留了22端口，防止之后因为各种权限和配置问题，导致连22端口都不能访问了，那就尴尬了。等一切都ok了，再关闭22端口。
Ok，继续，我增加了17569端口，大家修改端口时候最好挑10000~65535之间的端口号，10000以下容易被系统或一些特殊软件占用，或是以后新应用准备占用该端口的时候，却被你先占用了，导致软件无法运行。

<!--more-->
第二步：如果你关闭了SELinux，可以忽略第二步。
先查看SELinux开放给ssh使用的端口

    [root@zelvan ~]# semanage port -l|grep ssh

我的系统打印如下：

    ssh_port_t                    tcp      22

可知，SELinux没有给SSH开放10086端口，那么我们来添加该端口：

    [root@zelvan ~]# semanage port -a -t ssh_port_t -p tcp 17569

完成后，再次查看

    [root@zelvan ~]# semanage port -l|grep ssh
        ssh_port_t                    tcp      22，17569

第三步：如果你关闭了防火墙，
可以忽略第三步，话说防火墙不开启太危险了，建议开启。先查看防火墙是否开启了17569端口：

    [root@zelvan ~]# firewall-cmd --permanent --query-port=17569/tcp

打印结果如下：   no
表示没有开放17569端口，那么添加下该端口：

    [root@zelvan ~]# firewall-cmd --permanent --add-port=17569/tcp

打印结果如下：       success
重新加载防火墙策略：

    [root@zelvan ~]# firewall-cmd --reload

执行成功后，查看17569端口是否被开启：

    [root@zelvan ~]# firewall-cmd --permanent --query-port=17569/tcp

打印结果如下：       Yes
第四步：重启SSH服务和防火墙，最好也重启下服务器

    [root@zelvan ~]# systemctl restart sshd
    [root@zelvan ~]# systemctl restart firewalld.service
    [root@zelvan ~]# shutdown -r now

第五步：尝试通过17569端口登录SSH，或者进入该服务器直接本地访问SSH如下：

    [root@zelvan ~]# ssh root@localhost -p 17569

如果成功，说明17569已经完全可以使用了，接下来你就可以根据上述步骤把sshd_config的Port22注释掉，SELinux和防火墙（Firewalld）关闭22端口就OK，大工造成！

二、禁止root远程登录
------------

2.1 添加和root权限一样的用户
  

    [root@zelvan ~]# adduser admin
    [root@zelvan ~]# passwd  admin （修改密码）

 然后输入密码   （密码简单了通不过）
系统提示输入确认密码后再输入一次。OK添加成功。

 
2.1.2、修改 /etc/sudoers 文件，找到下面一行，在root下面添加一行，如下所示：

    [root@zelvan ~]# vim /etc/sudoers 
    ## Allow root to run any commands anywhere
    root    ALL=(ALL)     ALL
    admin   ALL=(ALL)     ALL

 这个文件只读是一种保护机制,如果你使用vi编辑器的话,只要保存时使用:wq!就可以保存了。 或者使用visudo命令来进入sudoers文件的编辑，就可以正常保存

2.2禁止root远程登录
需要编辑/etc/ssh/sshd_config。

        [root@zelvan ~]#vim /etc/ssh/sshd_config   
    找到 PermitRootLogin
    改为 PermitRootLogin no

重启 

[root@zelvan ~]# systemctl restart sshd 

附：semanage命令的安装
---------------

实验环境:CentOS 7 Minimal Installation 64bit(1511) semanage命令是用来查询与修改SELinux默认目录的安全上下文。命令介绍这里推荐最为完整的在线中文版man手册http://man.linuxde.net/semanage CentOS系统自带的chcon工具只能修改文件、目录等的文件类型和策略,无法对端口、消息接口和网络接口等进行管理,semanage能有效胜任SELinux的相关配置工作。

    [ aliyunzixun@xxx.com ~]# semanage 
    -bash: semanage: command not found 
    [ aliyunzixun@xxx.com ~]# yum install semanage 
    Loaded plugins: fastestmirror 
    Loading mirror speeds from cached hostfile 
    * base: mirror.bit.edu.cn 
    * extras: mirrors.btte.net 
    * updates: mirrors.btte.net 
    No package semanage available. 
    Error: Nothing to do 
    [ aliyunzixun@xxx.com ~]# yum provides semanage 
    Loaded plugins: fastestmirror 
    Loading mirror speeds from cached hostfile 
    * base: mirror.bit.edu.cn 
    * extras: mirrors.btte.net 
    * updates: mirrors.btte.net 
    policycoreutils-python-2.2.5-20.el7.x86_64 : SELinux policy core python utilities 
    Repo : base 
    Matched from: 
    Filename : /usr/sbin/semanage 

安装,这里直接使用tab键补全功能可以方便的找到要安装的包。tab补全功能参考http://blog.csdn.net/capricorn90/article/details/52558280

    [ aliyunzixun@xxx.com ~]# yum -y install policycoreutils-python.x86_64 

命令常用格式

管理登录linux的用户和SELinux局限的用户之间的映射

    semanage login [-S store] -{a|d|m|l|n|D} [-sr] login_name | %groupname 

管理策略模块

    semanage module [-S store] -{a|d|l} [-m [–enable | –disable] ] module_name 

管理网络端口类型定义

    semanage port [-S store] -{a|d|m|l|n|D} [-tr] [-p proto] port | port_range 


