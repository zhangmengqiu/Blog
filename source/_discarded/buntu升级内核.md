title: Ubuntu升级内核
author: Feng Tao
tags:
  - Note
categories: []
date: 2017-03-03 15:40:00
---
### 简介
Linux的内核是会定期进行更新的，更新内核是为了对一些新的硬件进行支持，例如触屏、声卡、显卡等。在Ubuntu的使用过程中可能需要对Ubuntu的内核进行升级，可以选择下载内核源码自己编译，不过更便捷的是使用[Ubuntu官方](http://kernel.ubuntu.com/~kernel-ppa/mainline)编译好的deb包，直接安装。例如将内核升级到目前最新的[4.0.1](http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.10.1/)。
```
linux-headers-4.10.1-041001_4.10.1-041001.201702260735_all.deb
linux-headers-4.10.1-041001-generic_4.10.1-041001.201702260735_amd64.deb
linux-image-4.10.1-041001-generic_4.10.1-041001.201702260735_amd64.deb
```
安装命令：
```shell
sudo dpkg -i linux-headers-*.deb
sudo dpkg -i linux-image-*.deb
```
安装完之后进行重启即可。
```shell
reboot
```