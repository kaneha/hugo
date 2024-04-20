+++
title = 'Linux - 整理遇到的问题'
date = 2024-04-20T16:38:41+08:00
tags = ["Linux"]
series = ["Linux"]
series_order = 1

+++

### 创建快捷方式

把 `名字.desktop` 放在 `/usr/share/applications`  或者 `/home/kaneha/.local/share/applications` 就会在启动器中显示, 也可指直接放在桌面上. 文件内容示例:

``` ini
[Desktop Entry]
Name=WindTerm
Comment=
# 关键字,查询时使用
Keywords=windterm
# 可执行程序的位置
Exec=/home/kaneha/portable-app/WindTerm_2.5.0/WindTerm
# 图标位置
Icon=/home/kaneha/portable-app/WindTerm_2.5.0/windterm.png
Type=Application
Categories=Development
```

### 修改默认编辑器

仅限debian系

* `sudo update-alternatives --config editor`



### root用户不能ssh登陆

1. 确保已经安装ssh  (apt install openssh-server)
2. 打开/etc/ssh/sshd_config, 找到配置项并修改为如下内容
     PermitRootLogin yes
       StrictModes yes 
3. 结束

### 如何安装Linux系统

1. 下载`sufus` , 用它制作Linux U盘启动引导盘.

2. 将引导盘插到要安装的电脑上, 设置BIOS启动项顺序, 把U盘启动顺序调到第一, 按照指引安装即可.



### 选择图形界面或命令行界面

``` bash
# 以下命令都需要重启电脑才能生效
# 更改默认启动到命令行下：
sudo systemctl set-default multi-user.target
# 要恢复默认启动到桌面，执行：
systemctl set-default graphical.target
```

### 鼠标键盘不能用

注意: 仅限debian系

``` text
1. 重启系统后按“ESC”进入grub引导界面：
这里看个人电脑情况，楼主按一下“ESC”就可以进入了。

2. 在引导界面选择 Advanced Options
3. 选择 带有（Recovery mode ）的选项
4. 接着选择 Network 并点 yes
5. 继续选择 Drop to root shell prompt，并点“Enter”
6. 输入：mount -o rw,remount /
7. 再输入：apt install xserver-xorg-input-all (这里确保电脑联网)
等待安装配置完毕，搞定！！
最后按 “Ctrl+D” 退出， 输入 “sudo reboot” 重启就完事了！
```

### 操作系统中文乱码

``` bash
cat /usr/share/i18n/SPPORTED
# 找出以下4行
# zh_CN.GB18030 GB18030
# zh_CN.GBK GBK
# zh_CN.UTF-8 UTF-8
# zh_CN GB2312

# 注: 我的电脑里文件是 zh-hans 而不是 local , 修改zh-hans即可.
# 将支持的中文字符集添加进该文件，格式如SUPPORTED文件中所得内容。
sudo vim /var/lib/locales/supported.d/local
# 更新, 然后ubuntu就可以支持GBK, GB2312, GB18030了.
sudo locale-gen
```

