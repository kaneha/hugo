+++
title = 'Linux - 桌面软件'
date = 2024-04-20T14:54:55+08:00
tags = ["Linux"]
series = ["Linux"]
series_order = 1

+++

注意: 默认使用Debian系操作系统

这里只介绍安装使用稍麻烦的软件, 一些安装使用很简单的就不在这里介绍了.

### fcitx5

**安装**

``` bash
# 安装
sudo apt install fcitx5 fcitx5-chinese-addons;
# 设置为默认输入法(注意只有debian系有这个软件, 红帽系没有)
im-config
# # 打开fcitx5配置， 输入法顺序改为
# 1. 键盘 - 英语 (美国)
# 2. 拼音(如果用双拼，就选shuangping)

# 设置完毕后记得重启下输入法
```

**fcitx5主题**

直接去github搜, 有十来个主题. 都用使用说明

* [candlelight - 烛光](https://github.com/thep0y/fcitx5-themes-candlelight)

**遇见的问题**

> * fcitx5的v键不管用

在键盘设置里的模糊音(fuzzy pinyin settings) 中勾选 `u <-> v`即可.

> * gnome下修改主题不生效

检查gnome扩展管理器里是否安装了**Input Method Panel** 这个插件， 有的话禁用或卸载此插件即可.

> * fedora系统下fcitx5不能自动启动

需要额外安装一个软件: `sudo dnf install fcitx5-autostart`

参考: https://yanqiyu.info/2020/11/06/fcitx5-fedora-updated/

### 腾讯会议

腾讯会议不支持wayland， 需要关闭wayland才能使用腾讯会议

``` bash
sudo vim /etc/gdm3/custom.conf
#WaylandEnable=false 的注释井号去掉
sudo service gdm3 restart
```
### synaptic(新立得软件包管理器)

可以看作是apt的图形界面.

`sudo apt install synaptic`

### snipaste

2023年12月snipaste推出了linux版的AppImage软件包, 目前软件还不太稳定, 后续要多注意版本更新.

#### 遇到的问题

1. 右键托盘图标打不开设置页面

贴出图片后按快捷键`ctrl + shift + p` 打开设置界面. 或者使用 GNOME 插件`AppIndicator and KStatusNotifierItem Support` 将托盘图标位置改为居中.

2. 快捷键在部分页面不生效

解决方法: 创建全局的自定义快捷键, 通过snipaste命令来执行截图和贴图. 

详情查看snipaste wiki 的命令行选项相关内容: [snipaste wiki](https://github.com/Snipaste/feedback/wiki)

``` bash
# 常用命令
# 截图
./snipaste.AppImage snip
# 贴图
./snipaste.AppImage paste
```

### Flameshot

截图软件, 官网文档: https://flameshot.org/docs/

安装直接去官网看教程即可, 也可以使用apt: `apt install flameshot`

**命令**

只说一个最常用命令, 选区区域截图: `flameshot gui`. 其他子命令和参数自行 `--help`查阅.

**遇见的问题**

> * 使用flatpak安装时, 找不到flameshot可执行文件

flatpak安装的应用都在隔离的环境中, 要使用flatpak安装的软件的可执行程序, 需要借助 `flatpak run` 命令. 下面是使用方法

``` bash
# org.flameshot.Flameshot 是flameshot在flatpak中的id
# 使用 `flatpak list` 命令查询已安装的应用列表.
flatpak run org.flameshot.Flameshot <子命令> [参数]
```

> * 设置快捷键截图报错

提示内容: screenshot capture failed.  

在终端可以使用, 但绑定快捷键就不行了, 解决办法: https://github.com/flameshot-org/flameshot/issues/3340#issuecomment-1869798556

思路是快捷键执行自己写的脚本, 用此脚本调用flameshot命令.

步骤是:

1. 建一个脚本, 比如`flameshot-workaround.sh`, 脚本内容是 `flameshot gui`
2. 在 `usr/bin` 建一个可执行文件, 比如`flameshot-workaround`, 内容是指向上一步创建的脚本地址
3. 记得两个文件都要添加可执行权限
4. 快捷键指向自己创建的可执行文件.



### 盒装微信

2024年3月, 微信官方已经发布了Linux版, 以后可以不用这个了, 这个只留作备用

**通过脚本执行docker命令来启动DoChat**

``` bash
#!/bin/bash
# 这3项是指定 fcitx(小企鹅输入法) 作为输入法, 指定为fcitx后就可以在微信中使用 fcitx 或者搜狗输入法了. 指定为ibus就可以在微信中使用ibus 
# 	  -e XMODIFIERS=@im=fcitx \
#	  -e GTK_IM_MODULE=fcitx \
#	  -e QT_IM_MODULE=fcitx \

containerName=DoChat
var0=`docker ps | grep ${containerName}`
echo "获取内容是: ${var0}"
if [[ ${#var0} > 0 ]]
then
	echo "微信正在运行中...无需重新打开"
else
	echo "准备打开微信..."
	docker run \
	  --name ${containerName} \
	  --rm \
	  -i \
	  \
	  -v "$HOME/Documents/docker_mount/DoChat/WeChat Files/":'/home/user/WeChat Files/' \
	  -v "$HOME/Documents/docker_mount/DoChat/Applcation Data":'/home/user/.wine/drive_c/users/user/Application Data/' \
	  -v /tmp/.X11-unix:/tmp/.X11-unix \
	  \
	  -e DISPLAY \
	  \
	  -e XMODIFIERS=@im=fcitx \
	  -e GTK_IM_MODULE=fcitx \
	  -e QT_IM_MODULE=fcitx \
	  -e GID="$(id -g)" \
	  -e UID="$(id -u)" \
	  \
	  --ipc=host \
	  --privileged \
	  \
	  zixia/wechat
fi
```

