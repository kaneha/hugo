+++
title = 'Hugo - 快速入门'
date = 2024-04-11T10:29:17+08:00
weight = 1
+++


首先, 多阅读官方文档:

* [hugo官方文档](https://gohugo.io/documentation/)



## 安装
安装hugo官方文档
  https://gohugo.io/installation/

为什么要安装dart-sass?
> Dart Sass is required to transpile Sass to CSS when using the latest features of the Sass language.

### windows
照着官方文档安装即可.(go, dart-sass, hugo)
windows包管理工具我选择了scoop

### Linux

go, dart-sass 和 hugo 使用homebrew安装, 不要用apt, 版本太老了. 尤其是hugo, apt的老版本hugo可能不兼容新主题.


## 快速开始

``` bash
mkdir quickstart;
hugo new site quickstart
cd quickstart
git init
# 注意, hugo必须有模板文件才能正确运行(否则会404)
# 这里使用现成的主题, 主题自带的有模板文件, 这样就不用自己写模板文件了
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
echo "theme = 'ananke'" >> hugo.toml

# --bind 0.0.0.0 允许所有ip访问服务, 可以试试访问了
# ctrl + c 可以停止服务
hugo server --bind 0.0.0.0
```



添加文章

``` bash
hugo new content posts/my-first-post.md
```

命令新增的md文件长这样 (+++ 中间是文件的元信息 +++), new content其实是按照archetypes目录下的 new content专用模板 `default.md` 来 new content 的. 此文件可以按照自己的偏好自行修改.

draft = true 表示这是草稿(草稿不会发布), false表示不是草稿

``` text
+++
title = 'My First Post'
date = 2024-01-14T07:07:07+01:00
draft = true
+++
```

修改配置文件(hugo.toml)
  baseURL = 'https://example.org/'  (baseURL必须以协议开头, 以斜线结尾)

接着启动服务 `hugo server -D` , -D表示把草稿也发布出去, 访问页面, 会发现新加的页面已经展示出来了.



## 部署hugo

### 部署到nginx

1. 生成静态文件

``` bash
# 执行hugo命令生成静态文件, 有很多参数, 具体自行查看 `hugo --help`
# 注意不要部署到/root目录下, nginx没有访问权限, 访问网站会报错403
hugo
```

这一步会生成网站代码, 注意不指定路径时会默认在当前文件夹下生成public和resources文件夹.

2. 配置nginx

``` nginx
server {
    listen 1234 ssl;
    listen  [::]:1234 ssl;
    server_name blog.***.***; 	# 你的域名
  	ssl_certificate /etc/nginx/ssl/cert.pem; # 证书路径
  	ssl_certificate_key /etc/nginx/ssl/key.pem; # 私钥路径

    location / {
        # 将该路径替换为你的网站根目录。
        root   /var/www/hugo/;
        # 添加默认首页信息
        index  index.html;
    }
}
```

### 使用github action部署

[hugo官方文档 - 使用 github action 部署到 git pages](https://gohugo.io/hosting-and-deployment/hosting-on-github/#build-hugo-with-github-action)
我是完全按照官方文档操作的, 已经部署成功了. 基本逻辑就是自己编写部署流程(workflow), 把workflow提交到github上后交给github去执行网站静态文件的生成和部署工作.
部署唯一的难点在于编写github workflow, 不过hugo官方文档上已经贴出了内容, 可以直接使用, 不过要注意下分支名和hugo版本, 最好和本地使用一致的hugo版本.
github workflow不仅支持部署到 github page, 也可以部署到云服务器等其他地方, 先提一下, 这个之后有需求再了解





## hugo站点文件结构
[官方文档: hugo文件结构](https://gohugo.io/getting-started/directory-structure/)
* `archetypes` : 存放content模板
* `assets` : 包含通常通过 asset pipeline 传递的全局资源。包括图片、CSS、Sass、JavaScript 和 TypeScript 等资源
* `config` : 目录包含站点配置，可能分为多个子目录和文件。对于配置较少或不需要在不同环境中以不同方式运行的项目，项目根目录中的单个 hugo.toml 配置文件就足够了
* `content` : 目录包含组成站点内容的标记文件（通常是 markdown）和页面资源
* `data` : 目录包含用于增强内容、配置、本地化和导航的数据文件（JSON、TOML、YAML 或 XML）。
* `hugo.toml` : 根目录的站点配置文件, 优先级低于config目录下的配置. (config中有配置时, hugo.toml不生效)
* `i18n` : 目录包含多语言站点的翻译表
* `layouts` : 目录包含将content、data和resources转换为完整网站的模板
* `public` : 目录包含发布的网站静态文件，在运行 hugo 命令时生成。Hugo 根据需要重建此目录及其内容。
* `resources` : 目录包含 Hugo asset pipelines 的缓存输出，在运行 hugo 或 hugo server 命令时生成。默认情况下，此缓存目录包括 CSS 和图片。Hugo 根据需要重建此目录及其内容。
* `static` : 目录包含在构建站点时将复制到 public 目录的文件，例如 favicon.ico、robots.txt 和用于验证站点拥有权的文件。在引入 page bundles 和 asset pipelines 之前，static 目录也用于存放图片、CSS 和 JavaScript 等资源。
* `themes` : 用于存放主题

注意: 
* `public` 和 `resources` 目录是在执行`hugo` 或 `hugo server` 命令时生成的, 这两个目录无需加入git版本控制.

## hugo站点文件介绍

### content目录
hugo网站内的所有内容(比如文章)都存放在content目录下.
下面举个例子展示下content目录结构:
> * content
> 	* _index.md
> 	* posts
> 		* first_post.md
> 		* second_post.md
> 		* _index.md
> 		* animals
> 			* _index.md
> 			* dog.md

要注意的点:
* `路径映射`: 每个文件都会按照目录结构映射路径, 例如 dog.md 会被映射为 `https://host:port/posts/animals/dog`
* `_index.md` : 每个目录下都可以有一个 _index.md 文件, 作为目录介绍文件


