+++
title = '文件同步 - Syncthing'
date = 2024-04-12T11:00:45+08:00
draft = false
weight = 2
+++


使用`Syncthing` 软件可以将机器上的指定目录同步到其他机器上, 实现文件备份与实时同步的功能.



## 安装Syncting

### windows安装

去github上下载压缩包, 解压运行`syncthing.exe`即可.



### ubuntu安装

我采用的是apt安装方式, 步骤如下

**安装**

``` bash
# 1. 使用安装syncthing
apt update
apt install syncthing

# 2. 开放端口
# 8384 是gui和api的端口
# 21027 用于发现其他syncthing设备, 可以在配置文件中修改(~/.config/syncthing/config.xml)
# 22000 是syncthing的默认监听端口, 用于在不同设备之间建立同步连接
ufw allow to 0.0.0.0/0 port 8384
ufw allow to 0.0.0.0/0 port 21027
ufw allow to 0.0.0.0/0 port 22000

# 3. 首次运行syncthing, 运行时会生成配置文件(~/.config/syncthing), 启动完毕后直接ctrl+c关闭即可.
syncthing

# 4. 启用syncthing服务(开机自启)
cd /lib/systemd/system
mv syncthing@.service syncthing@root.service # 我这里使用root启用服务(syncthing官方不推荐使用root)
systemctl enable syncthing@root
systemctl start syncthing@root
```

至此安装完毕, 接下来配置syncthing

**配置登录**

打开 `~/.config/syncthing/config.xml` , 修改配置项 `<gui>` 下的 `<address>`, 把 ``127.0.0.1:8384` 改为 `0.0.0.0:8384`, 允许所有ip访问gui

![Snipaste_2024-04-07_13-47-53.jpg](https://pic.kaneha.online:1234/i/2024/04/07/661233968b085.jpg)



使用ip+端口的方式登入syncthing的gui即可, 首次登入会提示给gui设置账号密码.



## 同步文件

将文件单向同步到另一台机器上用作备份.

> 注意!
>
> ​	windows文件系统大小写不敏感, 而Linux文件系统大小写敏感. 从Linux向windows同步文件时, 如果同一目录下有同名但大小写不一样的文件(比如M和m), 在同步时会报错无法同步! win10增加了单独给文件夹开启文件大小写敏感的功能(**必须是空文件夹才能开启**), 如果要从Linux向windows同步文件, 记得先在windows的文件夹上开启大小写敏感再同步文件, 开启方法看文章最后的 其他问题 章节.



这里用Linux同步文件到windows作为例子, 要求linux和windows都安装并运行syncthing.

首先进入syncthing的gui页面(你的机器ip:8384).

![Snipaste_2024-04-07_14-06-26.jpg](https://pic.kaneha.online:1234/i/2024/04/07/661237ef5d374.jpg)

1. 在 `设置->连接` 中勾选`本地发现`, 并确保防火墙开启21027端口(用于发现其他本地syncthing设备)

2. 在linux的syncthing界面点击`添加远程设备`, 添加完毕后被添加方的gui界面会弹出提示, 需要被添加方接受连接, 则远程设备添加完毕, 双方建立连接.

3. 在linux的syncthing界面点击`添加文件夹`, 将文件夹类型设置为`仅发送`, 且共享给windows的syncthing设备, 点击确定
4. 此时windows的syncthing界面会收到linux设备发来的提示让`添加文件夹`, 将文件夹类型设置为`仅接收`, 且共享给linux的syncthing设备, 修改本地保存路径, 点击确定
5. 到此设置完毕, linux的指定文件夹内容会自动同步给windows下的指定文件夹.



## 其他问题

### windows系统的大小写不敏感问题

linux系统是大小写敏感的, 从linux同步文件到windows时, 有可能出现同一文件夹下同名但大小写不同的文件无法同步的问题, 因为windows文件系统大小写不敏感导致无法区分这两个文件. 解决办法是将windows的指定目录设置为区分大小写模式, 要注意以下几点

* 要开启大小写敏感模式的文件夹必须是空文件夹
* 此功能是win10新增的, 所以win10以前的windows系统不支持设置目录大小写敏感.
* 将一个文件夹设置为大小写敏感以后, 它后续的子文件都会继承大小写敏感属性.



操作步骤如下:

在控制面板的 `启用或关闭Windows功能` 中, 勾选 `适用于Linux的Windows子系统`. 然后重启电脑.

![Snipaste_2024-04-07_14-34-22.jpg](https://pic.kaneha.online:1234/i/2024/04/07/66123e79eec8c.jpg)



以管理员权限启动cmd或powershell, 执行以下命令

``` bash
# <path>是你要开启大小写敏感的文件夹路径
fsutil.exe file setCaseSensitiveInfo <path> enable
```



设置完毕, 可以正常同步同名但大小写不同的文件了.



参考1: [区分大小写 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/case-sensitivity)

参考2: [fsutil 不支持该请求_fsutil file setcasesensitiveinfo 不支持-CSDN博客](https://blog.csdn.net/qq_16812035/article/details/89182920)