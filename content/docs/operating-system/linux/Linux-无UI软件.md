+++
title = 'Linux - 无UI软件'
date = 2024-04-20T15:42:09+08:00
weight = 2
+++

没有用户图形界面的软件, 一般使用命令操作.

### apt & dpkg

apt和dpkg的区别:

* apt: 从远程仓库下载并安装软件, 且安装时会自动安装依赖包, 解决依赖问题.
* dpkg: 主要用于 安装/删除 本地的软件包, 安装时不会安装依赖包, 不解决依赖问题.

相关命令自己`--help`查询, 这里不赘述

**修改软件源**

软件源目录: `/etc/apt/sources.list` 和 `/etc/apt/sources.list.d`

去各大镜像站找镜像源, 比如清华开源镜像站: https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/

**遇到的问题**

> dpkg安装deb包时提示依赖xxx包但没有安装

如果安装deb包的时候提示依赖xxx包但没有安装, 可以随后执行`apt --fix-broken install` 来自动安装上对应的依赖包即可.

### crontab

cron文件位置: 

* /var/spool/cron/crontabs : 用户级文件
* /etc/crontab : 系统级文件

cron表达式格式: `* * * * * <命令>`, 分别是 分, 时, 日, 月, 星期

用法:

* `*`  ==>  表示所有可能的值
* `,`  ==>  用','隔开一个列表范围,比如 [* * * * 1,2 expression] 每到周一周二,每分钟执行一次
* `-`  ==>  指定一个范围的值,比如 [* * * * 1-5 expression] 在周一到周五期间,每分钟执行一次
* `/`  ==>  指定时间间隔,比如 [*/5 * * * * expression] 每5分钟执行一次

### firewalld

红帽系的防火墙, 写在这里万一哪天用上了呢?

``` text
firewalld是一款防火墙管理工具,有基于CLI(命令行界面)和基于GUI(图形用户界面)的两种管理方式.
命令模板: firewall-cmd <参数> [--permanent]
    ***先介绍几个重要参数***
        --permanent: 持久化策略配置.不加这个参数就更改的策略配置,更改会在firewalld重启后失效
                                   加上这个参数后更改配置,更改需要在重启firewalld后才生效
        --reload: 重新加载策略配置.

一.firewalld加入了区域的概念(zone),简单来说,区域就是firewalld防火墙预先准备的几套防火墙策略模板,可以根据不同场景选择合适的策略
    1.关于区域的参数
        --get-default-zone: 获取默认的区域名
        --set-default-zone: 设置默认区域
        --get-active-zones: 显示当前正在使用的区域和网卡名称
        --get-zones: 显示所有可用的区域

    2.其他参数
        --state: 查看firewall的状态
        --list-all: 列出当前区域的配置信息,例如:网卡,资源,端口以及服务等信息
        --query-port=3306/tcp: 查询端口是否开放
        --add-port=3306/tcp: 添加端口
        --remove-port=3306/tcp: 移除端口
        --add-forward-port=port=80:proto=tcp:toport=21:toaddr=192.168.1.1: 设置端口映射,多个参数用冒号分隔,分别是 "端口:协议:映射端口:映射地址"
        --remove-forward-port=port=80:proto=tcp:toport=21:toaddr=192.168.1.1: 移除端口映射,参数同上
        --query-forward-port=port=80:proto=tcp:toport=21:toaddr=192.168.1.1: 查询该端口转发是否被添加(可以直接用--list-all查看所有端口转发配置)

```



### frp

由go语言编写的高性能反向代理应用. 下载地址: https://github.com/fatedier/frp/releases

文件介绍:

* frps 和 frps.ini  ==> 服务端的启动文件和配置文件
* frpc 和 frpc.ini  ==> 客户端的启动文件和配置文件

#### 安装与使用

``` bash
sudo mkdir -p /etc/frp      #新建目录/etc/frp
cd /etc/frp                 #进入该目录
#下载frp压缩包(x86_64位linux版)到目录内
```

**1. 服务端配置 (frps.ini)**

``` ini
[common]
bind_port = 7000            #frp服务端监听的端口
token = 12345678            #口令,客户端连接该服务器时需要提供对应的token(即此处的12345678)
dashboard_port = 7500       #dashboard监听的端口
dashboard_user = admin      #dashboard账户
dashboard_pwd = admin       #dashboard密码
vhost_http_port = 10080     #反向代理http主机时使用,此行可不填
vhost_https_port = 10443    #反向代理http主机时使用,此行可不填
```

配置完毕后启动frps: `./frps -c ./frps.ini`

**2. 客户端配置**

``` ini
[common]                    #通用配置
server_addr = x.x.x.x       #服务器的(公网)ip
server_port = 7000          #服务器监听的端口
token = 12345678            #服务器设置的口令
[mangos]                    #以下为自定义配置,可设置多个,名字自己起,此处我起名mangos
type = tcp                  #使用的通信协议
local_ip = 127.0.0.1        #本机ip (如果是路由器的话,此处可以写路由器下的主机的ip)
local_port = 8085           #客户端(本机)被代理的端口
remote_port = 7001          #服务端监听的通信端口
[realmd]
......
local_port = 3724
remote_port = 7002
```

配置完毕后启动frpc: `./frpc -c ./frpc.ini`

最后记得放行防火墙对应端口

#### 设置开机自启动

创建系统服务文件(服务端客户端都要创建, 分别是 frps 和 frpc): 

`vim /lib/systemd/system/frpc.service`

``` ini
[Unit]
# 描述
Description=frpc daemon
# 在这些服务之后启动 (target是服务组)
After=network.target syslog.target
# 需要的服务 (wants是弱相关,弱相关时依赖的服务关掉也不会关闭当前服务, 强相关是Requires)
Wants=network.target

[Service]
Type=simple
# 服务端
# ExecStart=/etc/frp/frps -c /etc/frp/frps.ini
# 执行的命令 (客户端)
ExecStart=/etc/frp/frpc -c /etc/frp/frpc.ini
# 可能会碰到网络模块还没有加载就启动frp, 导致连接不到frps服务器, 这里设置失败后重启可以避免
Restart=on-failure
RestartSec=10

[Install]
# 被什么服务/服务组依赖
WantedBy=multi-user.target
```

记得最后enable一下.



### screen

screen还有一个功能更强大的替代品 `tmux`, 以后有时间可以尝试下

linux命令直接挂到后台会停止, screen可以为命令创建一个专用的窗口, 此窗口可以在后台执行.

``` text
screen状态:
    detached:
        无会话,可以用-r正常进入该窗口,注意,detached不是窗口关闭的意思,而是会话关闭,窗口仍在运行
    attached:
        有会话,需要先用-d参数关闭掉之前会话(窗口状态变为detached),才能连接

screen常用命令:
    screen -S <窗口名> -> 新建一个窗口并自定义窗口名
    screen -ls         -> 列出当前所有的session
    screen -r <窗口名> -> 回到<窗口名>这个会话
    screen -dr <窗口名> -> 如果其他窗口状态为attached,则需要-d先踢掉前一位用户,再-r回复窗口
    screen -mdS <窗口名> -> -m的作用是,即使当前已经在一个窗口中,仍强制建立新的窗口作业

在screen内部使用的快捷键:
    ctrl + a + z: 相当于ctrl+z, 把当前程序挂起到后台.(zombie),挂起后输入fg即可恢复程序
    ctrl + a 双击: 切换最近的两个窗口
    ctrl + a + n: 切换到下一个窗口
    ctrl + a + p: 切换到前一个窗口
    ctrl + a + d: 离线(detached)当前窗口
    ctrl + a + k: 关闭当前窗口(真的关闭,不是detached)
    ctrl + a + c: 新建一个窗口

```



### ufw

只介绍几个需要额外注意的命令, 其他自己查`--help`

``` bash
# 新建规则
ufw allow 20
ufw allow 20:21/tcp
# 删除规则
ufw delete allow 20
ufw delete allow wow # 删除profile规则
# 端口只开放ipv4协议
ufw allow to 0.0.0.0/0 port 3306
ufw allow to 0.0.0.0/0 app Nextcloud
```

**ufw app**

ufw app 可以自己创建文件来保存防火墙配置, 配置文件放在 `/etc/ufw/applications.d`目录下,写法如下

``` ini
# profile, 起个名字
[wow]
# 标题,没啥用
title=wow
# 描述,没啥用
description=wow (3724:realmd, 8085:mangosd)
# 要开放的端口
ports=3724|8085

# ufw app 要注意的点:
# 1. <profile>建议首字母大写 --> <profile>和系统内的service名冲突时会报错, 首字母大写避免冲突
# 2. 文件名尽量不要和<profile>相同, 因为一个文件可以定义多个 profile.
# 3. ufw app 命令操作的对象是profile, 而不是文件名! 尤其是一个文件内定义多个 profile 时要注意这点!
```

### vsftpd

懒得写直接粘贴过来, 不太用得到

``` text
vsftpd - (very secure ftp daemon) - 非常安全的ftp守护进程
是一款运行在linux上的一款ftp服务程序

安装vsftpd: yum install vsftpd
安装ftp: yum install ftp  (以命令行界面的方式来管理ftp传输服务的客户端工具)

一.首先看vsftpd的目录结构
    1.根目录: /etc/vsftpd
    2.文件结构:
        ftpusers: 黑名单,写在里边的user无法访问ftp服务.会一直生效.
        user_list: 根据配置不同,可以作为黑名单或白名单使用
        vsftpd.conf: 配置文件
        vsftpd_conf_migrate.sh: vsftpd配置迁徙脚本?不知道是啥

二.简单介绍一下端口
    端口取值范围: 0-65535
    公认端口: 0-1023. 它们紧密绑定（binding）于一些服务。通常这些端口的通讯明确表明了某种服务的协议。例如：80端口实际上总是HTTP通讯。
    注册端口: 1024-49151. 它们松散地绑定于一些服务。也就是说有许多服务绑定于这些端口，这些端口同样用于许多其它目的。例如：许多系统处理动态端口从1024左右开始
    动态和/或私有端口：49152-65535。理论上，不应为服务分配这些端口。***实际上，机器通常从1024起分配动态端口***。但也有例外：SUN的RPC端口从32768开始

    vsftpd监听的端口:
        20: 数据端口,负责数据传输
        21: 命令端口,负责建立连接等

三.FTP的两种传输模式: 主动模式(FTP Port)和被动模式(FTP Passive)
    参考:https://www.cnblogs.com/ajianbeyourself/p/7655464.html
    主动模式流程:
        完全版:
            1.客户端打开两个任意的非特权本地端口(N 和 N+1)
            2.N端口连接服务器的21端口
            3.然后客户端开始监听端口(N+1)，并发送FTP命令“port N+1”到FTP服务器
            4.接着服务器会从它自己的数据端口（20）连接到客户端指定的数据端口（N+1）(服务器主动)
        简化版: (->表示建立连接)
            1.客户端N端口 -> 服务器21端口
            2.服务器20端口 -> 客户端N+1端口
        所以防火墙必须允许以下端口的通讯
            FTP服务器命令（21）端口接受客户端任意端口（客户端初始连接）
            FTP服务器命令（21）端口到客户端端口（>1023）（服务器响应客户端命令）
            FTP服务器数据（20）端口到客户端端口（>1023）（服务器初始化数据连接到客户端数据端口）
            FTP服务器数据（20）端口接受客户端端口（>1023）（客户端发送ACK包到服务器的数据端口）

    被动模式流程:
        完全版:
            1.客户端打开两个任意的非特权本地端口(N 和 N+1)
            2.N端口连接服务器的21端口, 并发送PASV命令到服务器
            3.服务器开启任意非特权端口(P)作为数据端口,并发送port P命令给客户端
            4.客户端发起从本地端口N+1到服务器的端口P的连接用来传送数据 (服务器被动)
        简化版: (->表示建立连接)
            1.客户端N端口 -> 服务器21端口
            2.客户端N+1端口 -> 服务器P端口
        所以防火墙必须允许以下端口的通讯
            FTP服务器命令（21）端口接受客户端任意端口（客户端初始连接）
            FTP服务器命令（21）端口到客户端端口（>1023）（服务器响应客户端命令）
            FTP服务器数据端口（>1023）接受客户端端口（>1023）（客户端初始化数据连接到服务器指定的任意端口）
            FTP服务器数据端口（>1023）到客户端端口（>1023）（服务器发送ACK响应和数据到客户端的数据端口）
    非特权端口: (1024-65535)
    区别: 
        连接方式:
            主动模式: 客户端发送 "port <数据端口>" 命令,服务器连接到客户端的数据端口
            被动模式: 服务器发送 "port <数据端口>" 命令,客户端连接到服务器的数据端口
        使用端口:
            主动模式:20, 21, N, N+1
            被动模式:P,  21, N, N+1  (被动模式中FTP不使用20端口)
        缺点:
            主动模式: FTP服务器企图与客户端的高位随机端口建立连接，而这个端口很有可能被客户端的防火墙阻塞掉
            被动模式: 客户端要与服务器端建立两个连接，其中一个连到一个高位随机端口，而这个端口很有可能被服务器端的防火墙阻塞掉

四.防火墙放开端口
    主动模式: 放开20,21端口
    被动模式: 放开21端口和高位端口

五.如何配置vsftpd
    vim /etc/vsftpd/vsftpd.conf 编辑vsftpd的配置文件

    配置示例:
        #注意: 1.每个变量值后边不能有空格!!!否则会无法启动

        #是否允许匿名登录
        anonymous_enable=NO
        #是否允许本地用户登录FTP,(是否允许本地用户模式, 其他两种模式是 匿名访问模式, 虚拟用户模式)
        local_enable=YES
        write_enable=YES
        local_umask=022

        #指定本地用户的FTP根目录
        local_root=/home/ftp_jinyu59/data
        #将用户权限禁锢在FTP目录(必须配合allow_writeable_chroot=YES使用,否则会导致无法登录)
        chroot_local_user=YES
        #允许对FTP根目录执行写入操作
        allow_writeable_chroot=YES
        dirmessage_enable=YES
        xferlog_enable=YES
        connect_from_port_20=YES
        xferlog_std_format=YES

        #是否以独立运行的方式监听服务
        listen=YES
        #设置FTP服务的监控端口
        listen_port=21
        listen_ipv6=NO

        pam_service_name=vsftpd
        #是否启用user_list文件
        userlist_enable=YES
        #设置user_list是白名单还是黑名单(YES为黑名单)
        userlist_deny=NO
        tcp_wrappers=YES

        #设置被动模式下服务器端的数据端口范围
        pasv_min_port=39000
        pasv_max_port=40000

六.vsftpd的3种认证模式
    匿名开放模式
    本地用户模式
    虚拟用户模式
    先不写......

六.使用ftp
    1.确认防火墙启用用到的端口.主动模式(20 和 21),被动模式(21 和 配置文件中规定的端口范围)
    2.确认云服务器的安全组访问规则中的入方向允许 对 (20,21  || 21,被动模式规定范围) 端口的访问
    3.vsftpd配置文件正确配置
    4.登录的用户没有被写入黑名单

七.现在遇到的问题
    主动模式登陆后无法获取目录列表,但是使用被动模式可以
```

