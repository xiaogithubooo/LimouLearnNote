# 1.VuePress 的简单概要

一个 `VuePress` 站点本质上是一个由 [Vue](https://vuejs.org/) 和 [Vue Router](https://router.vuejs.org/) 驱动的单页面应用。

路由会根据 `Markdown` 文件的相对路径来自动生成。每个 `Markdown` 文件都通过 [markdown-it](https://github.com/markdown-it/markdown-it) 编译为 `HTML`，然后将其作为 `Vue` 组件的模板。因此，你可以在 `Markdown` 文件中直接使用 `Vue` 语法，便于你嵌入一些动态内容。

在开发过程中，我们启动一个常规的开发服务器，并将 `VuePress` 站点作为一个常规的 SPA。如果你以前使用过 `Vue` 的话，你在使用时会感受到非常熟悉的开发体验。

在构建过程中，我们会为 `VuePress` 站点创建一个服务端渲染的版本，然后通过虚拟访问每一条路径来渲染对应的 `HTML`。

```shell
配置
$ npm init vuepress work-blogs
? Select a language to display / 选择显示语言 简体中文
? 选择包管理器 npm
? 你想要使用哪个打包器？ vite
? 你想要创建什么类型的项目？ docs
生成 package.json...
? 设置应用名称 work-blogs
? 设置应用版本号 0.0.1
? 设置应用描述 存放工作室文档和博客
? 设置协议 MIT
? 是否需要一个自动部署文档到 GitHub Pages 的工作流？ Yes
生成模板...
? 选择你想使用的源 当前源
安装依赖...
这可能需要数分钟，请耐心等待.
我们无法正确输出子进程的进度条，所以进程可能会看似未响应

added 239 packages in 14s

75 packages are looking for funding
  run `npm fund` for details
模板已成功生成!
? 是否想要现在启动 Demo 查看? Yes
启动开发服务器...
启动成功后，请在浏览器输入给出的开发服务器地址(默认为 'localhost:8080')

> work-blogs@0.0.1 docs:dev
> vuepress dev docs


  vite v5.4.11 dev server running at:

  ➜  Local:   http://localhost:8080/
  ➜  Network: http://192.168.101.254:8080/

 # 如果打开网站报错就使用 npm install -D sass-embedded 安装依赖...
 
```

# 2.VuePress 的工作目录

```shell
 # 查看工作目录
 $ tree -L 1
 ./work-blogs
├── .gitignore # 忽略文件
├── docs/ # 存储 Markdown 文档的地方, 同时也作为 VuePress 的源文件目录
│	├── get-started.md
│	├── README.md
│	└── .vuepress/ # VuePress 应用的源文件目录
├── node_modules/ # 包管理依赖代码
├── package.json # 包管理依赖文件
└── package-lock.json # 包管理依赖文件

```

其中，由于 `.vuepress` 中会有一些临时文件、缓存文件、构建文件生成，因此建议在项目的根目录内加上以下的 `.gitignore` 文件。

```shell
# .gitignore
# VuePress 默认临时文件目录
.vuepress/.temp
# VuePress 默认缓存目录
.vuepress/.cache
# VuePress 默认构建生成的静态文件目录
.vuepress/dist

```

# 3.VuePress 的约定路由

`VuePress` 采用约定式路由，并且 `.md` 文档会被自动渲染为 `.html` 文件。

```shell
目录结构
└─ docs
   ├─ guide
   │  ├─ getting-started.md
   │  └─ README.md
   ├─ contributing.md
   └─ README.md
   
```

将 `docs` 目录作为你的 `sourceDir`，在运行 `vuepress dev docs` 命令时，`Markdown` 文件对应的路由路径为：

| 相对路径                    | 路由路径                      |
| --------------------------- | ----------------------------- |
| `/README.md`                | `/`                           |
| `/index.md`                 | `/`                           |
| `/contributing.md`          | `/contributing.html`          |
| `/guide/README.md`          | `/guide/`                     |
| `/guide/getting-started.md` | `/guide/getting-started.html` |

# 4.VuePress 的配置文件

## 4.1.VuePress 的站点配置（站点级作用域配置）

一般的站点配置文件如下，[相关的配置选项看这里](https://vuepress.vuejs.org/zh/reference/config.html)。

```js
// docs/.vuepress/config
import { viteBundler } from '@vuepress/bundler-vite'
import { defaultTheme } from '@vuepress/theme-default'
import { defineUserConfig } from 'vuepress'

export default defineUserConfig({
  lang: 'zh-CN',
  title: '智能科教平台工作室共享文档',
  description: '旨在发布科教平台工作室的一些文档说明, 同时为一些项目做技术支持',

  bundler: viteBundler(),
  theme: defaultTheme({
    logo: 'https://vuejs.press/images/hero.png',
    navbar: ['/', '/get-started'],
  }),
})

```

## 4.2.VuePress 的前置数据（页面级作用域配置）

`Markdown` 文件可以包含一个 `YAML Frontmatter` 配置，`Frontmatter` 必须在 `Markdown` 文件的顶部，并且被包裹在一对三短划线中间。

```shell
test.md
---
lang: zh-CN
title: 页面的标题
description: 页面的描述
---
```

`Frontmatter` 中的字段和 [配置文件](https://vuepress.vuejs.org/zh/guide/configuration.html#config-file) 中的 [站点配置](https://vuepress.vuejs.org/zh/guide/configuration.html#站点配置) 十分类似。你可以通过 `Frontmatter` 来覆盖当前页面的 `lang`, `title`, `description` 等属性。因此，你可以把 `Frontmatter` 当作页面级作用域的配置。

相关配置选项可以[查看官方配置文档](https://vuepress.vuejs.org/zh/reference/frontmatter.html)，这里给出我的常用配置。

# 5.VuePress 的渲染细节

页面的主要内容是使用 `Markdown` 书写的。`VuePress` 首先会将 `Markdown` 转换为 HTML ，然后将 `HTML` 作为 `Vue` 单文件组件的 `<template>` 。

借助 [markdown-it](https://github.com/markdown-it/markdown-it) 和 `Vue` 模板语法的能力，基础的 `Markdown` 可以得到很多的扩展功能。

## 5.1.使用内置

- 表格
- 删除线
- 链接
- 避免渲染

## 5.2.使用拓展

- 标题锚点语法  [markdown-it-anchor](https://github.com/valeriangalliat/markdown-it-anchor)
- Emoji 语法 [markdown-it-emoji](https://github.com/markdown-it/markdown-it-emoji)
- 目录语法 [markdown.toc](https://vuepress.vuejs.org/zh/reference/config#markdown-toc)
- 代码块语法 [@vuepress/plugin-prismjs](https://ecosystem.vuejs.press/zh/plugins/markdown/prismjs.html) 和 [@vuepress/plugin-shiki](https://ecosystem.vuejs.press/zh/plugins/markdown/shiki.html)

## 5.3.使用组件

由于 `Markdown` 中允许使用 `HTML`，所以

> [!IMPORTANT]
>
> 补充：注意应该避免使用非标准的标签。
