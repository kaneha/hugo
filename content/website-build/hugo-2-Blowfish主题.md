+++
title = 'Hugo - 2 - Blowfish主题'
date = 2024-04-11T11:24:24+08:00
draft = false
series = ["Hugo建站"]
series_order = 2

+++



Blowfish主题. 啊, 很不错. 官方文档介绍非常详细, 多看看官方文档, 这里只写一些大概的说明和一些个人总结.

[中文文档 · Blowfish](https://blowfish.page/zh-cn/docs/)



下面总结下官方文档的内容

## 官方文档内容总结

### 入门指南

首先就是看这一篇, 可以快速上手, 主要介绍了:

* 颜色方案
* 如何使用菜单

[入门指南 · Blowfish](https://blowfish.page/zh-cn/docs/getting-started/)



### 配置

*  `hugo.toml` 文件的一些配置项
* 如何设置网站本地化(i18n)
* `languages.[language-code].toml`, 包含本地化的站点配置和作者简介
* `params.toml`, 主题功能的配置参数, 配置项非常多, 设计页面的每个部分

[配置 · Blowfish](https://blowfish.page/zh-cn/docs/configuration/)



### 主页布局

* 介绍了主页的几种布局

[主页布局 · Blowfish](https://blowfish.page/zh-cn/docs/homepage-layout/)



### 短代码(shortcodes)

* 介绍了由Blowfish主题提供的额外的shortcodes

下面是一个短代码例子, 在页面上可以看到效果

{{< alert icon="moon" cardColor="\#FFE7BA" iconColor="#1d3557" textColor="#1C1C1C" >}}
Blowfish专用短代码 - Alert
{{< /alert >}}



什么是短代码, 参考官方文档:

[短代码 | Hugo官方文档 (opendocs.io)](https://hugo.opendocs.io/content-management/shortcodes/)

[Shortcodes | Hugo (gohugo.io)](https://gohugo.io/content-management/shortcodes/)



### 缩略图

* 文章列表中每篇文章前的缩略图, 没啥说的

[缩略图 · Blowfish](https://blowfish.page/zh-cn/docs/thumbnails/)



### Partials

* 介绍了怎么使用Blowfish提供的 `partial template`

[Partials · Blowfish](https://blowfish.page/zh-cn/docs/partials/)



至于什么是 `partial template` ?

[部分模板 | Hugo官方文档 (opendocs.io)](https://hugo.opendocs.io/templates/partials/)

[Partial templates | Hugo (gohugo.io)](https://gohugo.io/templates/partials/)



### Front Matter(前置格式/扉页参数)

* 介绍了hugo的 `Front Matter` 参数, 以及Blowfish新增的 `Front Matter` 参数

[Front Matter · Blowfish](https://blowfish.page/zh-cn/docs/front-matter/)



什么是 `Front Matter` ?

[前置格式 | Hugo官方文档 (opendocs.io)](https://hugo.opendocs.io/content-management/front-matter/)

[Front matter | Hugo (gohugo.io)](https://gohugo.io/content-management/front-matter/)



### 系列(Series)

* 介绍了Blowfish主题自定义的一个分类 `series`. 通过它可以在文章的首尾列出同一系列的其他文章, 方便快速导航

[系列 · Blowfish](https://blowfish.page/zh-cn/docs/series/)



什么是分类(Taxonomies)?

[分类法 | Hugo官方文档 (opendocs.io)](https://hugo.opendocs.io/content-management/taxonomies/)

[Taxonomies | Hugo (gohugo.io)](https://gohugo.io/content-management/taxonomies/)



### 内容示例

* 简单介绍了分支页面( _index.md ), 叶子页面 和自定义布局的一些用法

[内容示例 · Blowfish](https://blowfish.page/zh-cn/docs/content-examples/)



### 进阶自定义

* 自定义配色方案
* 覆盖样式
* 如何修改主题源代码

[进阶自定义 · Blowfish](https://blowfish.page/zh-cn/docs/advanced-customisation/)
