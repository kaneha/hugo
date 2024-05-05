+++
title = '远程控制 OpenSSH'
date = 2024-04-13T20:34:10+08:00
tags = ["软件", "SSH"]

+++

## SSH密钥登录

SSH 密钥登录分为以下的步骤。(客户端指本机, 服务端指要远程控制的机器)
1. 客户端通过ssh-keygen生成自己的公钥和私钥。
2. 手动将客户端的公钥放入远程服务器的指定位置。
3. 客户端向服务器发起 SSH 登录的请求。
4. 服务器收到用户 SSH 登录的请求，发送一些随机数据给用户，要求用户证明自己的身份。
5. 客户端收到服务器发来的数据，使用私钥对数据进行签名，然后再发还给服务器。
6. 服务器收到客户端发来的加密签名后，使用对应的公钥解密，然后跟原始数据比较。如果一致，就允许用户登录。

简单来说就是:  客户端生成私钥和公钥并把公钥给服务端, 客户端持有私钥, 服务端持有公钥. 这样客户端就能使用密钥登录服务端了.

## 常用命令

**ssh-keygen**

用于生成密钥对. 

用法: `ssh-keygen [options]`

参数介绍如下:

``` bash
-t <加密算法> ==> 选择加密算法, 比如rsa
-C <注释> ==> 给密钥添加注释
```

举例: `ssh-keygen -t rsa -C "密钥对的用途"`

**ssh-keyscan**

用于客户端获取指定服务端持有的公钥. 一般将获取到的公钥存入本机 known_hosts 文件, 可以用来跳过首次 ssh 连接时的验证公钥过程.

用法: `ssh-keyscan [options] <hostname>`

参数介绍如下:

``` bash
-p <端口> ==> 指定要连接的服务端ssh端口
```

**ssh-copy-id**

用于将本地的公钥安装到远程主机对应账户的 authorized_keys 文件中.

用法: `ssh-copy-id [options] [user]@hostname`

参数介绍:

``` bash
-i <公钥文件> ==> 指定复制哪个公钥
-p <端口> ==> 指定要连接的服务端ssh端口
```



## ssh相关文件

### known_hosts

什么是known_hosts?

> A file associated with a specific account that contains one or more host keys. Each host key is associated with an SSH server address (IP or hostname) so that the server can be authenticated when a connection is initiated. The user or administrator who makes the first connection to an SSH server is responsible for verifying that the host key presented by that server is the actual key (not a rogue key) before it gets placed in the known hosts file.

known_hosts文件保存的公钥举例:

``` bash
# [域名/ip]:端口 加密协议 公钥
[hostname]:port ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAq2A7hRGmdnm9tUDbO9IDSwBK6TbQa+PXYPCPy6rbTrTtw7PHkccKrpp0yVhp5HdEIcKr6pLlVDBfOLX9QUsyCOV0wzfjIJNlGEYsdlLJizHhbn2mUjvSAHQqZETYP81eFzLQNnPHt4EVVUh7VfDESU84KezmD5QlWpXLmvU31/yMf+Se8xhHTvKSCZIFImWwoG6mbUoWf9nzpIoaSjB+weqqUUmpaaasXVal72J+UX2B+2RPW3RcT0eOzQgqlJL3RKrTJvdsjE3JEAvGq3lGHSZXy28G3skua2SmVi/w4yCE6gbODqnTWlg7+wC604ydGXA8VJiS5ap43JXiUFFAaQ==
```

**以配置 github 的 ssh 连接来举例说明**

案例: 
要使用 ssh 从 github clone 代码的话, 必须要先在本机生成密钥对并把公钥交给 github . 然后当你第一次使用 ssh 从 github clone 代码时, 终端会提示你要连接一个未知主机, 是否要将它提供的公钥加入 known_hosts. 此时选择 yes 则将 github 提供的公钥加入本机 known_hosts, ssh 成功建立连接, 并且之后再 ssh 连接 github 都不会再出现此提示.

分析:
为什么之后再 ssh 连接 github 时不会再出现提示? 因为之后每次 ssh 连接 github 时, github 仍会提供公钥, 但本机会先在 known_hosts 中匹配此公钥, 如果已经保存有相同的公钥, 则连接成功.

总结:
known_hosts 文件本机是用来验证公钥的, 当请求建立 ssh 连接时, 服务端会发送公钥到本机, 本机会在 known_hosts 文件中查找, 如果文件中存在此公钥, 则成功建立连接. 如果不存在, 则会发送提示询问是否将公钥加入 known_hosts 文件.



### authorized_keys 

authorized_keys 文件指定了哪些机器可以登录到这台机器. authorized_keys 文件中每一行是一条公钥. 配置 ssh 免密登录时要将生成的公钥交给服务端保管, 这一步就是将公钥写入 authorized_keys 文件.

