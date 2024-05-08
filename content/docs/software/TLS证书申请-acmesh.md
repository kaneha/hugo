+++
title = 'TLS证书申请 - Acme.sh'
date = 2024-04-12T12:25:47+08:00
draft = false
weight = 2
+++



## 快速上手

直接阅读官方文档, 大概要5分钟.

注意, 我采用dns的方式自动验证域名所有权, 需要用到ali的 api key 和 secret

[acme.sh官方文档](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)



使用步骤:

1. 安装acme.sh
2. 使用 acme.sh 命令申请tls证书
3. 使用 acme.sh 命令复制证书和密钥到 nginx/apache 目录下 (官方不推荐使用 `~/acme.sh `目录下的证书和密钥, 要复制出去使用)
4. 检查crontab, 看 acme.sh是否正确注册定时任务(安装acme.sh时会注册定时任务来检测证书, 自动续期)



注意的点:

* 证书安装(复制)命令执行时会被记录, 将来证书自动更新时会自动再次调用安装命令重新复制证书和密钥
* 安装acme.sh时会到crontab注册定时任务检测证书, 记得检查定时任务是否成功自动注册.



## 证书及相关文件常见格式



* `.pem` : 既可以是证书, 也可以是私钥. 也可以既是证书又是私钥
* `.crt` : 证书
* `.cer` : 也是证书, 格式不同
* `.key` : 私钥

