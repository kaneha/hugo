+++
title = 'Hugo - Blowfish主题'
date = 2024-04-11T11:24:24+08:00
weight = 2

+++



Blowfish主题. 啊, 很不错. 官方文档介绍非常详细, 多看看官方文档, 这里只写一些大概的说明和一些个人总结.

[中文文档 · Blowfish](https://blowfish.page/zh-cn/docs/)



下面总结下官方文档的内容

## 官方文档内容总结

### 入门指南

[入门指南 · Blowfish](https://blowfish.page/zh-cn/docs/getting-started/)

首先就是看这一篇, 可以快速上手, 主要介绍了:

* 颜色方案
* 如何使用菜单

### 配置

[配置 · Blowfish](https://blowfish.page/zh-cn/docs/configuration/)

*  `hugo.toml` 文件的一些配置项
* 如何设置网站本地化(i18n)
* `languages.[language-code].toml`, 包含本地化的站点配置和作者简介
* `params.toml`, 主题功能的配置参数, 配置项非常多, 涉及页面的每个部分

**遇到的坑:**

1. `showhero` 配置项官方文档介绍是 *"缩略图是否会在每个页面中作为 hero 图像显示"*, 但设置 `showhero=true` 后, 实际hero图像使用的却是背景图, 也就是说, 目前缩略图只有在文章列表中才会用到, 其他地方使用的都是背景图.

### 主页布局

[主页布局 · Blowfish](https://blowfish.page/zh-cn/docs/homepage-layout/)

* 介绍了主页的几种布局



### 简码(shortcodes)

[简码 · Blowfish](https://blowfish.page/zh-cn/docs/shortcodes/)

* 介绍了由Blowfish主题提供的额外的shortcodes

> 什么是简码, 参考官方文档:
>
> [Shortcodes | Hugo (gohugo.io)](https://gohugo.io/content-management/shortcodes/)

### 缩略图

[缩略图 · Blowfish](https://blowfish.page/zh-cn/docs/thumbnails/)

* 文章列表中每篇文章前的缩略图介绍

注意: 缩略图特指文章列表中每篇文章的图片.

### Partials

[Partials · Blowfish](https://blowfish.page/zh-cn/docs/partials/)

* 介绍了怎么使用Blowfish提供的 `partial template`

> 至于什么是 `partial template` ?
>
> [Partial templates | Hugo (gohugo.io)](https://gohugo.io/templates/partials/)

### Front Matter(前置格式/扉页参数)

[Front Matter · Blowfish](https://blowfish.page/zh-cn/docs/front-matter/)

* 介绍了hugo的 `Front Matter` 参数, 以及Blowfish新增的 `Front Matter` 参数

> 什么是 `Front Matter` ?
>
> [Front matter | Hugo (gohugo.io)](https://gohugo.io/content-management/front-matter/)



### 系列(Series)

[系列 · Blowfish](https://blowfish.page/zh-cn/docs/series/)

* 介绍了Blowfish主题自定义的一个分类 `series`. 通过它可以在文章的首尾列出同一系列的其他文章, 方便快速导航



> 什么是分类(Taxonomies)?
>
> [Taxonomies | Hugo (gohugo.io)](https://gohugo.io/content-management/taxonomies/)

### 内容示例

[内容示例 · Blowfish](https://blowfish.page/zh-cn/docs/content-examples/)

* 简单介绍了分支页面( _index.md ), 叶子页面 和自定义布局的一些用法



### 进阶自定义

[进阶自定义 · Blowfish](https://blowfish.page/zh-cn/docs/advanced-customisation/)

* 自定义配色方案
* 覆盖样式
* 如何修改主题源代码



## 遇到的问题

### 创建的菜单点击不跳转

menus.zh-cn.toml 菜单配置示例:

``` ini
[[main]]
  name = "Linux"
  parent = "目录"
  pageRef = "posts/operating-system"
  pre = "bars"
  weight = 20
```

原因是pageRef引用的 Hugo 分类不存在,  在 posts/operating-system/ 目录下新建一个 `_index.md` 文件就好了.

参考: https://gohugo.io/getting-started/glossary/#section
