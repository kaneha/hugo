+++
title = 'Hugo - 问题汇总'
date = 2024-05-08T15:00:09+08:00
weight = 4
+++





## 遇到的问题

### hugo构建静态文件时提示没有写入权限

**报错内容**:

> ERROR mkdir /var/www: read-only file system
> ERROR render: failed to render pages: mkdir /var/www: read-only file system
> Total in 52 ms
> Error: error copying static files: mkdir /var/www: read-only file system

**错误原因**: 

执行 `hugo -d <目标路径>` 生成静态文件时报错. 原因是我的hugo使用snap安装的......

> The Hugo snap package is strictly confined. Strictly confined snaps run in complete isolation, up to a minimal access level that’s deemed always safe. The sites you create and build must be located within your home directory, or on removable media.
>
> Hugo 的 snap 软件包受到严格限制。受到严格限制的软件包在完全隔离的环境中运行，直到达到被认定为始终安全的最低访问级别。您创建和构建的网站必须位于您的主目录中，或位于可移动媒体上。

**解决办法**: 

* 第一个办法: 在当前目录生成并复制到目标目录
* 第二个办法: 不要用snap安装hugo

### hugo启动后页面报404

**错误原因**:

hugo必须有模板文件才能构建网站代码, 没有自己写模板文件, 或是没有引入主题就会无法构建导致404

**解决办法**:
两种办法:

1. 自己写模板文件(不现实, 我是新手)
2. 使用主题(主题中都自带有模板文件)

