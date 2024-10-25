+++
title = 'Linux - 桌面软件'
date = 2024-04-20T14:54:55+08:00
weight = 2

+++

注意: 默认使用Debian系操作系统

这里只介绍安装使用稍麻烦的软件, 一些安装使用很简单的就不在这里介绍了.

## Linux桌面软件

### fcitx5

fcitx5只是一个输入法框架, 仅支持英文输入, 如果要输入其他语言则必须安装对应语言的fcitx5输入法引擎

**安装**

```bash
# ubuntu安装
sudo apt install fcitx5 fcitx5-chinese-addons;
# 设置为默认输入法(注意只有debian系有这个软件, 红帽系没有)
im-config
# # 打开fcitx5配置， 输入法顺序改为
# 1. 键盘 - 英语 (美国)
# 2. 拼音(如果用双拼，就选shuangping)
```

**Arch & Gnome 安装fcitx5**

1. 通过pacman卸载本地所有fcitx5相关软件 --> sudo pacman -Rns $(pacman -Qsq fcitx5)
2. 安装fcitx5, fcitx5-qt, fcitx5-gtk, fcitx5-configtool,   fcitx5-chinese-addons
3. 通过fcitx5 configuration 软件配置中文输入法即可.
4. 按照 arch fcitx5 wiki 所说, 兼容XDG的桌面环境(例如KDE,GNOME,Xfce,LXDE等), 无需设置即可随桌面环境自启动, 事实的确如此, 至少我的 arch gnome 可以正常自启动.

其他说明

1. 全程不用管 gnome 系统设置里的键盘输入法设置, 安装fcitx5后这个设置项应该是不起作用了
2. 我目前没有配置任何fcitx5相关的环境变量, 暂时没有碰到有软件不支持中文输入的问题, 以后碰到了再按照wiki进行设置
   1. jetbrain系软件无法输入中文, 解决办法, 去家目录的 `.bash_profile` 文件中导出环境变量即可. 这里有一个巨坑: **在 `.bashrc` 文件下配置环境变量并且`env`命令能查到配置的环境变量但idea依然无法输入中文, 但是配置到 `.bash_profile` 文件就没问题了.** arch中文wiki没有相关介绍, 但是在arch 英文wiki上看到有提说不要把fcitx的相关环境变量配置到`.bashrc`, 试了一下果然是这个原因. 这里附上arch英文wiki链接 [ArchWiki-fcitx]([Fcitx - ArchWiki](https://wiki.archlinux.org/title/Fcitx#Set_environment_variables_for_IM_modules)). 进去后搜索关键字 `bashrc` 即可找到.

环境变量配置内容:

```bash
# 配置内容
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
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

### AppImageLauncher

苦于 AppImage 软件创建桌面图标无法设置icon的人有福了.

github项目页面: [github - AppImageLauncher](https://github.com/TheAssassin/AppImageLauncher?tab=readme-ov-file)

1. 按官方教程安装
2. 安装完后打开 appimagelauncher 设置界面, 设置appimages文件所在目录(默认~/Applications)
3. 把所有的appimage软件放到该目录下, 然后双击打开即可运行, 首次运行时会自动创建图标, 以后就可以从图标启动appimage了

### wps

直接去官网下载安装包即可.

解决字体问题: https://github.com/dv-anomaly/ttf-wps-fonts

### 腾讯会议

腾讯会议不支持wayland， 需要关闭wayland才能使用腾讯会议

```bash
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

```bash
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

```bash
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

```bash
#!/bin/bash
# 这3项是指定 fcitx(小企鹅输入法) 作为输入法, 指定为fcitx后就可以在微信中使用 fcitx 或者搜狗输入法了. 指定为ibus就可以在微信中使用ibus 
#       -e XMODIFIERS=@im=fcitx \
#      -e GTK_IM_MODULE=fcitx \
#      -e QT_IM_MODULE=fcitx \

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
