---
title: Vim编辑器
date: 2018-11-06 19:44:46
categories: 
    - Linux
tags: 
    - Linux
    - vim
---

### vim主要模式介绍，vim命令模式。
确保系统已经安装了VIM工具

    [root@panda ~]# rpm -qf `which vim`
    [root@panda ~]# rpm -qf `which vi`

扩展：
问：vi和vim是同一个软件包安装的吗?
答：NO，vim是vi的增加版，最明显的区别就是vim可以语法加亮，它完全兼容vi
#### 1.  vim编辑器模式
 ![](/images/2018/vim001.png)
 <!--more-->
首次进入文件    ---- 命令模式
出现 “Insert” ---- 编辑模式
输入:          ---- 命令行模式
A：从编辑模式到命令行模式怎样切换?	
	编辑模式->esc->命令模式->: ->命令行模式
B：字符操作(怎样进入编辑模式?)
	进入编辑模式 a i o  A I O
说明：
i 当前字符之前插入 (光标前)
I 行首插入  (行首)
a 当前字符之后插入 (光标后)
A 行尾插入(行尾)
o下一行插入 (另起一行)
O上一行插入(上一行插入)
x 向后删除一个字符		等同于delete
X 向前删除一个字符     	
u 撤销一步   每按一次就撤销一次
r 替换

#### 2.  在命令模式下做的操作：
##### 光标定位
hjkl  左下上右
0 和 home键表示切换到行首， $和end键表示切换到行尾
gg 快速定位到文档的首行 ,  G定位到未行
3gg 或者 3G  快速定位到第3行
/string(字符串)   -----找到或定位你要找的单词或内容，如果相符内容比较多，我们可以通过N、n来进行向上向下查找，并且vi会对查找到的内容进行高亮显示，取消用 :noh
/^d  ----^意思表示以什么开头 ，，查找以字母d开头的内容
/t$   -----$意思表示以什么结尾，，查找以字母t结尾的内容
vim + a.txt  打开文件后，光标会自动位于文件的最后一行


##### 如何对文本进行编辑
删除、复制、粘贴、撤销
y 复制（以字符为单位） :表示对单个字符进行复制，如果要复制整行，用yy（以行为单位）   
复制N行： Nyy  ，比如： 2yy ，表示复制2行
dd（删除，以行为单位，删除当前光标所在行）
删除N行： Ndd  ，比如： 2dd ，表示删除2行
p ： P粘贴
剪切： dd
x 删除光标所在位置的字符
D 从光标处删除到行尾
u  撤销操作 
ctrl+r  还原撤销过的操作，将做过的撤销操作再还原回去，也就是说撤销前是什么样，再还原成什么样
r 替换，或者说用来修改一个字符

##### 总结：vim如何进入其它模式
a A  o O  i I 都是可以进行插入，编辑模式
： 进入命令行模式
v 进入可视模式
ctrl+v 进入可视块模式
V 进入可视行模式
R 擦除、改写，进入替换模式
你进入以上模式后，想要退出 ，按esc
扩展：插入模式中的操作
ctrl+p可以进行补全操作,所需要的内容必须是在当前打开的文件内存在的，它只针对当前文件


#### 3.  V模式（列）
进入v模式 移动光标选择区域、
编程的时候需要进行多行注释：
	1)、ctrl+v 进入列编辑模式
	2)、向下或向上移动光标，把需要注释、编辑的行的开头选中起来
	4)、然后按大写的I
	5)、再插入注释符或者你需要插入的符号,比如"#"
	6)、再按Esc,就会全部注释或添加了

删除：再按ctrl+v 进入列编辑模式；向下或向上移动光标 ；选中注释部分,然后按d, 就会删除注释符号。

#### 4.  命令行模式操作
:w 保存 save
:w! 强制保存
:q 没有进行任何修改，退出 quit
:q! 修改了，不保存，强制退出
:wq 保存并退出 
:wq! 强制保存并退出
:x 保存退出
例: wq! 强制保存并退出

    [root@xuegod63 ~]# ll /etc/shadow
    ----------. 1 root root 1179 9月  19 12:57 /etc/shadow
    [root@xuegod63 ~]# vim /etc/shadow

##### 调用外部文件或命令
假设：我想要写入我的网卡MAC地址,,我要查看一下,当前在vim编辑文档，照着写。这样好麻烦。
在命令行模式下操作：
:!ifconfig	调用系统命令
!+命令
读取其他文件。（把其他文件中的内容追加到当前文档中）
:r /etc/hosts

##### 文本替换
格式 : 范围（其中%所有内容）   s分隔符 旧的内容 分隔符 新的内容  （分隔符可以自定义）
默认是每一行的第一个符合要求的词   (/g全部)

    :1,3 s/bin/xuegod    替换第1到3行中出现的第一个bin进行替换为xuegod
    :1,3 s/bin/xuegod/g  替换第1到3行中查找到所有的bin进行替换为xuegod
    :3 s/xue/aaaaa     #只把第3行中内容替换了
    :% s/do/xuegod/g  	将文本中所有的do替换成xuegod
    :% s/do/xuegod/gi	将文本中所有的do替换成xuegod, 并且忽略do的大小写
    :% s@a@b@g	   将文本中所有的a替换成b

#### 5. 自定义vim使用环境
##### 临时设置
:set nu  设置行号
:set nonu 取消设置行号
:noh   取消高亮显示
##### 永久设置环境
vim /etc/vimrc  设置后会影响到系统所有的用户
~/.vimrc   #在用户的家目录下，创建一个.vimrc。这样只影响到某一个用户，没有自己建一个
例：

    [root@xuegod63 ~]# cat  /root/.vimrc  
    set nu
    [root@xuegod63 ~]# vim  /root/.vimrc

 ![](/images/2018/vim002.png)


#### 6. vim 其他常用命令
##### vim打开多个文件
方法1：以上下形势，打开两个文档

    [root@xuegod63 ~]# vim -o /etc/passwd /etc/hosts
 ![](/images/2018/vim003.png)
 
方法2：以左右方式打开两个文档

    [root@xuegod63 ~]# vim -O /etc/passwd /etc/hosts

注：ctrl+ww  在两文档之间进行切换编辑。大写O左右分屏，小写的o上下分屏

##### 比较两个文件内容

    [root@xuegod63 ~]# cp /etc/passwd mima.txt
    [root@xuegod63 ~]# echo aaa >> mima.txt 
    [root@xuegod63 ~]# diff /etc/passwd mima.txt 
    40a41
    > aaa
    [root@xuegod63 ~]# vimdiff /etc/passwd mima.txt


#### 7.  实战1：在windows中编辑好的汉字文本文档，上传到Linux下打开乱码。
实验环境：centos7.4 现在系统默认使用的语言是汉语。（系统中必须安装好中文包）。
将同目录下“a此文件在windows下打开正常-到linux下vim打开是乱码.txt”上传到Linux服务器上。使用ssh远程连接到Linux上，使用vim打开显示乱码。
原因：编码的问题
通过iconv命令转码   没有使用过：1
参数：
-f, --from-code=名称 原始文本编码
-t, --to-code=输出编码
-o, --output=FILE 输出文件名

    [root@xuegod63 ~]# mkdir test  #创建一个测试目录
    [root@xuegod63 ~]# cd test/

将测试的文件上传到Linux服务器上：
 ![](/images/2018/vim004.png)


    [root@xuegod63 ~]# iconv -f gb2312  -t utf8 a此文件在windows下打开正常-到linux下vim打开是乱码.txt  -o aa.txt
    [root@xuegod63 ~]# cat aa.txt 
    #!/bin/bash
    echo "学神IT"
    信息：
    -l, --list 列举所有已知的字符集

#### 8.  实战2：解决将公司Linux服务器上脚本导到windows上打开串行的问题
原因：因为windows和linux处理回车方法不同。
上传” b在Linux编辑的文档到windows下没有换行.sh” 到Linux上，打开后正常显示

    [root@localhost test]# sz b在Linux编辑的文档到windows下没有换行.sh   #发送到本地

在window 上打开显示：
 ![](/images/2018/vim005.png)
 
解决方法：

    [root@xuegod63 ~]# rpm -ivh /mnt/Packages/dos2unix-6.0.3-7.el7.x86_64.rpm
    注： 在centos7上，unix2dos这个命令已经被集成到dos2unix-6.0.3-7.el7.x86_64.rpm包中。在centos6下需要安装unix2dos.xxx.rpm。
    [root@localhost test]# unix2dos b在Linux编辑的文档到windows下没有换行.sh
    [root@localhost test]# sz b在Linux编辑的文档到windows下没有换行.sh   #发送到windows本地  显示正常。

 ![](/images/2018/vim006.png)
注：dos2unix 这个命令是把windows下的回车转成linux类型。

