+++
title = 'Hugo - 3 - 进阶内容'
date = 2024-04-11T15:20:49+08:00
draft = false
series = ["Hugo建站"]
series_order = 3

+++





## 一些说明

hugo的进阶学习不会照搬官方文档, 东西太多了, 而且有很多东西也用不到. 我打算用到什么功能就往这里写, 持续更新.



## 模板

官方文档: [Templates | Hugo (gohugo.io)](https://gohugo.io/templates/)

hugo中的模板文件是一种特殊的HTML文件, 并且这些模板文件都位于根目录的 `layouts` 文件夹下. 模板使用 `variables`, `functions` 和 `methods` 来将你的 `content`, `resource` 和 `data` 转换成页面.



## 定制网站实践



### 修改主题模板

主题一般使用 `git submodule` 来安装, 不方便直接修改主题文件, 怎么办呢?  已知: 

* 根目录内的文件优先级高于主题内的文件

所以, 我们可以按照相同的目录结构, 将主题内的`layouts`模板文件复制到根目录的`layouts`目录下来达到修改主题模板的目的.

下面是一些修改模板的具体实践

**1. 首页列出所有 section**

``` html
<div>
  <h1>目录</h1>
  <ul>
      <!-- 遍历所有sections -->
    {{ range .Site.Sections }}
    <li>
      <a href="{{ .RelPermalink }}">{{ .Title }}</a>
    </li>
    {{ end }}
  </ul>
</div>
```




