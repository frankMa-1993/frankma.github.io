# vue3组件库项目学习笔记（六）：发布组件文档

现在我们已经拥有了一个可以发布的组件库，也可以进行良好的协作和维护，但是我们想要我们的用户能够清楚的了解每个组件的使用方式，我们需要一个文档网站，类似于 element-plus 这样的官网。

很庆幸，vite官方提供了非常良好的生态帮助我们搭建这样的站点：vitepress框架

关于这个框架本人写过一篇非常详细的专栏，如果你还没有接触过相关的内容，那么作者非常推荐你先去了解相关的内容，本教程将会默认你完成掌握相关的配置：

https://blog.csdn.net/weixin_46463785/article/details/128591769

## 配置站点

首先我们新建 docs 文件夹，我们假设你还在本地调试我们的程序，所以你开源先将 docs 文件夹配置在 pnpm-workspace.yaml 里：

```yaml
packages:
  - 'packages/**'
  - 'examples'
  - 'docs'
```

之后我们在 docs 文件夹下引入依赖：

```shell
pnpm add vitepress -D -w
```

之后我们在文件夹下 pnpm init 初始化内容，然后写入以下的脚本：

```json
"scripts": {
    "docs:dev": "vitepress dev docs",
    "docs:build": "vitepress build docs",
    "docs:serve": "vitepress serve docs"
},
```

之后我们在我们的根目录的 package.json 里写入命令来启动我们的 vitepress

```shell
"doc:dev": "pnpm run --filter ./docs docs:dev"
```

在根目录运行 pnpm run doc:dev ，如果运行成功说明你的站点已经搭建起来了

## 配置内容

这个内容在我的教程里已经非常详细的讲过了，我就不再讲一次了，

https://blog.csdn.net/weixin_46463785/article/details/128591769

我直接给出每个文件里应该有的内容，首先我们搭建一个如下的结构：

```javascript
docs
├─ docs
│  ├─ .vitepress
│  │  ├─ theme
│  │  │  └─ index.js
│  │  └─ config.js
│  ├─ public 
│  └─ index.md
└─ package.json
```

之后我们在我们的 index.md 配置我们的主页

```markdown
---
layout: home

title: LS-UI
titleTemplate: 测试哈哈哈哈

hero:
  name: LS
  text: 测试哈哈哈哈
  tagline: 测试哈哈哈哈
  image:
    src: /icon.png
    alt: LS-UI
  actions:
    - theme: brand
      text: 开始
      link: /guild/install
    - theme: alt
      text: 在 Github 上查看
      link: https://github.com/aiai0603/ls-ui

features:
  - icon: 💡
    title: Vue3组件库
    details: 基于vite打包和TypeScript开发
  - icon: 📦
    title: 仅供学习使用
    details: 测试哈哈哈哈
  - icon: 🛠️
    title: 按需引入
    details: 直接支持按需引入无需配置任何插件。
---
```

然后我们在 .vitepress/config.js 里配置我们的导航和主体内容，要注意你需要先保存一张图片作为你的图标在 public 文件夹下面，命名为 icon.png

```json
module.exports = {
    themeConfig: {
        siteTitle: false,
        logo: "/icon.png",
        nav: [
            { text: "指南", link: "/guild/install" },
            { text: "组件", link: "/examples/button/" },
        ],
        socialLinks: [{ icon: "github", link: "https://github.com/aiai0603" }],
        sidebar: {
          "/guild/": [
              {
                  text: "基础",
                  collapsible: true,    //是不是可以动态展开
                  collapsed: true,      //默认是不是展开
                  items: [
                      {
                          text: "安装",
                          link: "/guild/install",
                      },
                      {
                          text: "快速开始",
                          link: "/guild/start",
                      },
                  ],
              },
          ],
          "/examples/": [
              {
                text: "组件",
                collapsible: true,    //是不是可以动态展开
                collapsed: true,      //默认是不是展开
                items: [
                  {
                    text: "Button按钮",
                    link: "/examples/button/",
                  }
                ],
              },
            ],
      },
    },
}
```

然后我们在 .vitepress/theme/index.js 里配置我们组件库，我们需要先安装我们自己的组件库，做法和 examples 文件夹下的预览网页一样不在赘述了；你也可以在发布你的组件库后，在其他位置搭建你的文档项目，然后下载 npm 版本的组件库，这样更加方便，灵活性更高：

```javascript
import DefaultTheme from "vitepress/theme";
import LsButton from 'ls-ui';
export default {
  ...DefaultTheme,
  enhanceApp: async ({ app, router, siteData }) => {
    app.use(LsButton)
  },
};
```

在搭建完成框架以后我们就可以书写我们的 md 文档了，vitepress 框架将把我们的文档解析成网页展示给用户：我们在 docs 文件夹下新建两个文件夹，分别叫做 guild 和 examples。

然后我们书写两个文件 install.md 和 start.md 在 guild 文件夹中，如果你想将他们命名成不同的名字，你需要修改刚刚编写的 config.js 这个文件：

````markdown
# 安装

## 环境支持

由于 Vue 3 不再支持 IE11，LS-UI 也不再支持 IE11 浏览器。

## 版本

V 1.0.9


# 下载组件库

## NPM
```shell
npm install ls-ui --save
```

## YARN
```shell
yarn add ls-ui
```

## PNPM
```shell
pnpm install ls-ui
```

````

````markdown
# 快速开始

本节将介绍如何在项目中使用 LS-UI

## 用法

```html
<template>
  <ls-button>按钮</ls-button>
</template>

<script setup>
    import { LsButton } from 'ls-ui'
</script>
```
````

最后我们在 examples 文件夹下面 创建 button 文件夹并且写入一个 index.md 文件，我们将在这里放置我们的组件展示，通过 vitepress 框架，我们可以实现在 md 文件中写入 vue 的语法，更多细节在我的教程提到:

https://blog.csdn.net/weixin_46463785/article/details/128592061

最后使用 md 的特殊语法，我们就可以实现控制我们的代码块隐藏或者展示了：

````markdown
# BUTTON

默认按钮的使用

<ls-button  type="primary" style="margin-right:10px">默认按钮</ls-button>
<ls-button type="text">文字按钮</ls-button>

<details>
<summary> 展开看看 </summary>

```vue
<ls-button  type="primary" style="margin-right:10px">默认按钮</ls-button>
<ls-button type="text">文字按钮</ls-button>
```

</details>
````

## 发布站点

显然你需要将你的站点发布到公网让更多人看见你的文档，你可以选择用自己的服务器，或者使用 GITpages 的方式来发布，具体发布的可以参考我写过的教程：

https://blog.csdn.net/weixin_46463785/article/details/128591987
