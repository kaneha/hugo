+++
title = '文件冷备 Restic'
date = 2024-05-05T18:37:34+08:00
tags = ["软件","文件冷备"]
+++



## Restic

Restic is a modern backup program that can back up your files:

- from **Linux, BSD, Mac and Windows**
- to **many different storage types**, including self-hosted and online services
- **easily**, being a single executable that you can run without a server or complex setup
- **effectively**, only transferring the parts that actually changed in the files you back up
- **securely**, by careful use of cryptography in every part of the process
- **verifiably**, enabling you to make sure that your files can be restored when needed
- **freely** - restic is entirely free to use and completely open source



### Restic安装

ubuntu安装:

``` bash
apt update
apt install restic
```



### Restic用法介绍

restic是用来备份数据的.

备份方式有:

* 本地备份
* sftp备份到远程
* Rclone的方式可以备份到网盘等地方

通过sh脚本操作restic命令, 可以实现自动备份和自动删除快照.



### Restic命令简单介绍

restic命令很简单, 尽量多使用 `restic --help` 查看使用说明

#### 一些常用命令

``` bash
# 1. 初始化
# 注意! 初始化的时候要设置密码, 这个密码绝对不能忘记, 否则备份的数据就没法访问了.
restic -r <repo_path> init

# 2. 备份指定目录内容到restic仓库, 并创建一个快照
# 注意: 一个仓库只能对应一个目录或文件, 再backup其他文件或目录时会把之前的覆盖掉(当然了, 之前的快照还留着)
restic -r <repo_path> backup <source_path>
# 备份时排除指定目录或文件, 要排除多个目录则需要写多个--exclude参数
restic -r <repo_path> --exclude <pattern> backup <source_path>
# 举个例子, 排除指定目录内 xx/ignore 下的所有子文件(包括ignore目录)
restic -r repo --exclude xx/ignore/** backup /xx/source

# 3.将仓库挂载到指定挂载点, 可以用来查看仓库中所有的快照内容. 要结束挂载时使用ctrl+c强制结束即可.
restic -r <repo_path> mount <mount_path>

# 4. 查看指定仓库的快照列表数据
restic -r <repo_path> snapshots

# 5. 将仓库的指定快照版本数据恢复到指定目录
restic -r <repo_path> restore <snapshot_id> <target_path>

# 6.根据快照id删除仓库的某个快照, 但数据依然存留, 需要执行prune子命令才能清理数据, 或者给forget子命令加上--prune参数以制动执行prune
restic -r <repo_path> forget <snapshots_id>
# 根据策略删除快照(有很多删除快照时的快照保留策略, 使用restic forget --help 自行查看)
restic -r <repo_path> forget --keep-last n   # 保留最近的n个快照, 删除其他快照
```



#### 一些重要参数

注意: 可以使用 `restic 子命令 --help` 的方式查看每个子命令有哪些参数

`restic` 命令参数

> * `-p, --password-file  <file>` : 每条restic命令都需要输入密码, 可以把密码保存到一个文件中, 使用 -p参数指向此文件路径, 即可免去手动输入密码的步骤
> * `-r, --repo  <repo>` : 指明要操作的仓库



`restic backup` 子命令参数

> * `-e, --exclude  <pattern>` : 要排除的内容, 按照多个pattern排除时需要重复指定多次此参数.



`restic forget` 子命令参数

> * `-l, --keep-last  <n>` : 保留最近的n个快照.
> * `--prune` : 如果有快照被删除, 则自动执行 `prune` 子命令.





参考:

* [restic · Backups done right!](https://restic.net/)

* [愉快的使用restic备份数据 | Escape (escapelife.site)](https://www.escapelife.site/posts/912084a4.html#toc-heading-4)

