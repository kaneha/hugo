+++
title = 'Linux - 系统安装'
date = 2024-05-06T13:45:54+08:00
weight = 2
+++

## 安装流程

### 制作启动引导盘

方法1: 使用ventoy (推荐)

ventoy全平台可用, 而且使用超简单. 运行软件, 然后一键把ventoy安装到u盘上, 之后只需要把ios文件拷贝到u盘中即可. 此u盘可以正常作为普通u盘使用. 把安装有ventoy的u盘作为启动引导盘时电脑会自动进入ventoy的界面, ventoy会自动遍历u盘中的ios镜像文件, 在此界面可以选择要使用的ios镜像文件即可.



方法2: 使用rufus (rufus只支持windows系统)



### 安装Linux

1. 进入bios设置, 设置启动顺序, 把u盘调整到第一位, 保存并退出
2. 按照指引安装操作系统即可.



## 需要注意的点

### 分区与挂载点

安装Linux系统时, 常用的挂载点有

* `/` : 挂载根目录
* `/boot` : 它的作用是安装grub、存放Linux kernel以及initrd映像。这些都是启动时必需的东西，单独存在boot区便于管理。可以分配 500M-1G 空间。
* `/home` : (可选)挂载用户目录. 单独挂载此目录时, 将来换其它版本的Linux也不会影响你自己的数据

为了正确安装Linux, 通常最少需要创建3个分区

* 挂载 `/boot` 的分区: 大约500M-1G空间
* 挂载 `/` 的分区: 容量不宜过大, 系统分区只能扩容不能缩容, 如果确定不会安装双系统则无所谓.
* swap分区: 可选分区, 不创建此分区也行. 容量通常是内存容量的1倍, 当然也可以更小或更大.

## Arch Linux 安装

安装教程: https://github.com/nakanomikuorg/arch-guide

教程讲的十分全面, 非常好哇.

### 安装Arch时遇到的问题

#### 主板启动项没有Arch

1. (微星主板) 启动项设置最下边有个 `UEFI 硬盘BBS启动优先权`, 点开把Arch放到第一即可.
原因是我把windows和Arch安装到同一块硬盘上了.

#### grub启动项没有windows

1. 编辑 `/etc/default/grub`，取消注释或新增一行 `GRUB_DISABLE_OS_PROBER=false`
2. 执行 `sudo os-prober` 看能否检测到windows操作系统. 能检测到则继续下一步
3. 重新生成 grub.cfg `sudo grub-mkconfig -o /boot/grub/grub.cfg`


## GRUB

### grub配置文件介绍
* `/etc/default/grub` : grub的配置文件
* `/etc/grub.d/` : 该文件夹下包含了一系列的shell脚本, 这些脚本被 grub-mkconfig 调用来生成最终的 GRUB 配置文件(默认: /boot/grub/grub.cfg)
* `/boot/grub/grub.cfg` : 由`grub-mkconfig` 或 `update-grub` 命令生成的grub配置脚本, 应当每次都由程序生成而不是手动修改它. 文件的作用是定义 GRUB 启动菜单的布局和行为。当计算机启动时，GRUB 会读取这个文件来决定如何呈现启动菜单，并根据用户的输入选择加载哪个操作系统或内核。

### grub命令介绍

* `grub-mkconfig` : 读取 `/etc/default/grub` 和 `/etc/grub.d/` 下的配置, 并生成配置文件, 默认生成的配置文件为: `/boot/grub/grub.cfg`. 
* `update-grub` : debian系使用 `update-grub` 而不是 `grub-mkconfig` 来生成 `grub.cfg` 文件

### 修改 grub 配置: 

1. 编辑 `/etc/default/grub` 文件
2. 执行 `sudo grub-mkconfig -o /boot/grub/grub.cfg` 以重新生成最终的GRUB配置文件(grub.cfg). debian系则使用 `update-grub` 命令

