# class14 - Vite



本节课，讲师将前置介绍有关前端工程基本知识，同时讲解前端构建工具及解决问题，进而引出作为前端构建工具的 Vite 是什么及其在开发过程中的应用。

## 为什么使用构建工具

前端是有一系列资源组成的，js 代码，css样式，静态资源，他们产生了一系列的资源，他们有一系列的问题，而构建工具就是解决这些问题的：

- 模块化

前端模块化有一些列常用的规范，但是还没有形成统一

解决：提供统一的模块加载方案，实现统一的模块规范

- 资源编译

js ， ts ，jsx 浏览器不认识，我们需要把他们编译成浏览器认识的

解决：构建工具会集成一些工具，实现语法转义和资源加载

- 构建产物质量

代码需要压缩，兼容性问题，比如移动端需要兼容低端浏览器

解决：产物压缩、无用代码删除，语法降级

- 开发效率

需要热更新

解决：提供热更新系统

## Vite

vite 是新一代前端构建工具，No-bundle开发，源文件不需要打包，生成环境基于 rollup ，它的特点是：高性能，dev和热更新都很快，简单易用

vite 基于原生ESM开发服务 vite dev serve，无需打包项目源码，天然按需加载，可以利用文件级的浏览器缓存

vite 使用 go 开发的 esbuild 工具，摒弃了原生js开发的弊端

vite 内置了很多webpack的基础设置，包括 css 的解析，html 插件等等

## Vite的使用

我们还是要首先下载依赖

```shell
pnpm i vite -g
```

之后我们就可以直接初始化一个 vite 项目了

```shell
pnpm create vite
```

之后依次输入 项目名称，框架（这里以react为例），语言（js/ts），进入项目，运行 pnpm install 下载所有的依赖就可以运行它了，之后可以看到在package.json 文件中有三条命令，分别是启动项目，打包和预打包

```json
"scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview"
  },
```

vite 项目不需要任何配置就可以解析 ts tsx 这些组件，css，scss，less这些样式以及各类资源文件

对于样式，你可以在vite项目中使用 css module的形式编写样式，这样引入的样式名称在编译中会变成一个转换为一个唯一的hash值。这样可以有效解决类名冲突等问题，你们你有多个 css 模块可能使用了同一个类名字

```css
//index.modules.scss
.main{
    color:red;
}

//app.tsx
import styles from './index.modules.scss';

<p className = {styles.main}></p>
```

对于资源的引用，你只需要在对于位置引入它，然后在 src 绑定他就行了，无论你使用什么路径引入，vite都会自动将他转化成相对路径

```tsx
import reactLogo from './assets/react.svg'

<img src={reactLogo} className="logo react" alt="React logo" />
```

vite 默认使用热更新的方式，无需配置

vite 打包默认删去未使用的代码进行 tree-shaking

在 vite.config.ts 这个文件中，我们可以配置我们需要的插件，比如刚刚初始化的项目，我们需要引入一个 react的插件，当然你使用 vue 的话，可以引入vue的组件

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
})

```

## vite的架构

![image-20230124113149276](/bit-dance/img/38.png)

## Vite 进阶

- 底层工具

esbuild 官方文档：https://esbuild.github.io/

rollup 官方文档：https://rollupjs.org/guide/

推荐先了解基本使用在了解插件开发

- 插件

插件的作用是抽离核心逻辑，易于维护，易于拓展，生态丰富

vite 插件的钩子 hooks：你可以在对于的钩子处调用它实现不同的功能

![image-20230124141848547](/bit-dance/img/39.png)

插件的开发可以参考这个：https://cn.vitejs.dev/guide/api-plugin.html

- 代码分割

vite 之前的打包一般代码都在一个文件里，无法进行并发求情，缓存利用率低，修改任何文件，都需要重新打包。

拆包就是就是把核心代码分成多个包，如果一个包修改了，其他包不受影响，学习 rollup 的代码分割，vite 就是基于 rollup 的代码分割的

- babel

js 的语法标准繁多，但是浏览器支持不一致，但是开始想要使用高级的语法，babel 就是把高级代码进行降低的工具，原理可以查看官网文档：https://babeljs.io/docs/en/

并且babel 也可以配置插件，你也可以自己编写其插件，你可以查看这个手册来学习：https://github.com/jamiebuilds/babel-handbook/blob/master/translations/zh-Hans/plugin-handbook.md

- SSR

这是一种常见的渲染模式，用于优化提升首屏幕的性能。vite提供了 vite-ssr 项目打包渲染的方案 ，可以查看官方文档 https://cn.vitejs.dev/guide/ssr.html

- 底层标准

https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/

![image-20230124143027101](/bit-dance/img/40.png)

- 社区生态

vite 提供了良好的社区生态，社区可以有大量的其他开发者编写的插件等，可以前往寻找你需要的组件 ： https://github.com/vitejs/awesome-vite
