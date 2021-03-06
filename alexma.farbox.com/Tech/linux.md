---
date: 2016-10-11 13:39
status: public
title: 'Linux 使用指南'
---

1. 如何进入root权限?

在终端的命令前加入 $sudo$ ,如
```shell
$ sudo apt-get install wine
```
当你需要进行很多次root操作时,可使用如下配置
```shell
$ sudo -s
```
2.如何打开任务管理器?
在linux下,任务管理器的名字为系统监视器,直接在启动器中寻找即可
3.如何打开压缩文件?
ubuntu下的归档管理器只能打开zip格式的文件
如需要打开rar格式,请安装unrar,解压命令为
```shell
$ unrar x xxx.rar
```
如需要打开7z格式的文件,请安装p7zip
4.如何使用bc
bc(Binary Calculator)是linux内置的终端任意精度算术语言解释器,使用方法
```shell
$ bc
```

bc中的^表示幂,如2^50为计算$2^{50}$

默认bc没有浮点数,如需要请输入
```bc
scale=num
```
num表示精度位数

bc中内置了几个常用数学函数,如需使用,请在进入bc时用-l命令开启数学库
```shell
$ bc -l
```
函数列表
* l(x) log函数(以2为底)
* s(x) 正弦函数
* c(x) 余弦函数
* a(x) 反正切函数
* e(x) e的指数函数

bc支持直接访问文件,如将算式写在1.txt后,只需
``` shell
$ bc 1.txt
```
5.如何安装程序?

一般情况下,我们可以采用如下方式

* 使用apt-get命令,一般情况下当你访问你需要的应用程序的官网时,会见到如下指示
```shell
$ sudo add-apt-repository 'ppa:wine/wine-builds'
$ sudo apt-get update
$ sudo apt-get install winehq-devel
```
在终端中输入上述指令即可

* 如果你的电脑有新立得软件包管理器,直接在其中寻找你需要的软件包即可
* 有时,官网只提供deb包,那么使用Ubuntu 软件或Gdebi软件包安装程序即可,或使用命令dpkg
``` shell
$ dpkg -i %s.deb
```

 _注意,上述软件包都含有排它锁,且运行时均需要root权限,请不要同时使用_


6.如何下载
在linux下我们一般用aria2下载,在sourceforge下载安装即可
``` shell
$ aria2c <url>
```
url代表下载链接,如果无法直接复制可以先在
浏览器下载获取链接