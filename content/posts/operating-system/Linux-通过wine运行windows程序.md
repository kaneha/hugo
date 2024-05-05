+++
title = 'Linux 通过wine运行windows程序'
date = 2024-05-05T17:50:43+08:00
tags = ["Linux","wine"]
series = ["Linux"]
series_order = 1

+++



wine官网: https://www.winehq.org/

通过wine可以使linux运行windows软件(exe格式软件).



## 快速开始

### 安装

借助清华大学开源软件镜像服务解决网络问题: [清华大学wine镜像服务](https://mirrors.tuna.tsinghua.edu.cn/help/wine-builds/)

wine最好使用最新的稳定版, 安装步骤分3步

1. 启用32位架构

   ``` bash
   dpkg --add-architecture i386
   ```

2. 信任来自https://dl.winehq.org/ 的公钥

   ``` bash
   wget -nc -O /usr/share/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key
   ```

3. 新增apt源, 内容如下

   ```
   deb [arch=amd64,i386 signed-by=/usr/share/keyrings/winehq-archive.key] https://mirrors.tuna.tsinghua.edu.cn/wine-builds/debian/ bookworm main
   ```

4. 安装wine

   ``` bash
   apt update
   apt install --install-recommends winehq-stable
   ```

### 使用

安装wine完毕后, 会在用户目录下生成 `.wine` 文件夹. 此文件夹下的 `drive_c` 文件夹是映射的 `windows c盘` . 通过wine安装的软件都会安装到 `drive_c` 目录下.

如何执行exe文件? 

​	右键打开方式, 选择 `Wine Windows Program Loader` .

## 施工中...后续遇到问题再补充
