+++
title = 'Linux - 桌面环境'
date = 2024-04-20T17:00:15+08:00
weight = 2

+++



## GRUB

> 小知识：GRUB 是 **GR**and **U**nified **B**ootloader（大一统引导程序） 的首字母缩写。

系统启动引导, 可以安装主题来美化系统启动引导页.

* **KDE** GRUB Theme: https://store.kde.org/browse?cat=109
* **GNOME** GRUB Theme: https://www.gnome-look.org/browse?cat=109

进入网站, 里面有安装教程.



## GNOME

### 安装桌面自定义相关的软件

**1. 安装gnome-tweaks 和 gnome-shell-extensions**

``` bash
# 其中tweaks用来设置美化选项, 需要安装插件来扩展tweaks的可选美化选项
sudo apt install gnome-tweaks gnome-shell-extensions -y
```

**2. 通过网页或extension manager安装扩展**

> 按照提示安装浏览器插件, 在网页上控制本地安装扩展, 但是ubuntu22有些问题, 无法通过这种方法安装扩展, 网页会提示不支持v6 api. 网址是: . 这里不通过网页安装, 使用另一种方法.

这里直接在本地安装 `extension manager`, 使用该软件安装 gnome 的扩展, 不通过网页安装.

1. 首先要下载 [flatpak](https://flatpak.org/setup/Ubuntu), 按照指引安装即可, flatpak是一个Linux应用商店.
2. 打开 [flatpak 的 Extension Manager](https://flathub.org/apps/details/com.mattjakeman.ExtensionManager) 网站, 按照下方提供的安装命令安装 Extension Manager
3. 我这里由于网络原因, 必须挂上全局代理翻墙才能正常安装成功, 否则会网络故障导致安装失败

打开 extension manager, 搜索安装 User Themes 插件并安装.

备注: extension manager github地址: https://github.com/mjakeman/extension-manager

在 extension manager  中搜索 dash 可以安装任务面板

### GNOME插件推荐

1. `AppIndicator and KStatusNotifierItem Support` : 在 topbar 显示系统托盘图标
2. `Blur my Shell` : topbar 透明化, 很漂亮
3. `Clipboard Indicator` : 剪切板历史记录, 并且在 topbar 提供图标
4. `Dash to Dock` : 支持自定义dock样式
5. `Just Perfection` : 提供细致的GNOME UI调整功能
6. `Order Gnome Shell extensions` : 支持调整 topbar 各元素的展示顺序
7. `Unite`: 可以隐藏双重顶栏, 需要自己去github下载最新版,  extension manager 中只能搜索到旧版
8. `Vitals` : 系统资源监控, 包括内存占用, 系统负载, 实施流量等.
9. `Workspace Indicator` : 在 topbar 展示工作空间. GNOME45及以上版本已经自带此功能所以无需此插件. 顺带一提debian12使用的是GNOME43.



### 安装字体

字体下载: www.zitijia.com

字体文件是以 `.ttf` 结尾的文件

``` bash
# 字体文件安装目录, 可以在这个目录下创建一个custom文件夹, 用来存放自己下载的字体
/usr/share/fonts

# 字体文件放到custom文件夹下后执行下边两个命令
sudo mkfontscale    #生成核心字体信息
sudo mkfontdir
# over
```

### 设置字体缩放并避免模糊

首先在系统设置里把缩放调整回100%, 并关闭 `fractional scale` 功能, 接着下载安装 `gnome-tweaks`

``` bash
sudo apt install gnome-tweaks gnome-shell-extensions
```

打开 `gnome-tweaks`, 到字体选项页, 调整字体缩放因子即可. 可使用命令 `gnome-tweaks` 来打开此软件.

## KDE

### 遇到的问题

KDE没有wifi设置(也没有网络设置)

> sudo apt install plasma-nm
>
> 安装plasma网络管理器(network manager)
>
> 重新打开系统设置, 现在应该有网络设置了.
