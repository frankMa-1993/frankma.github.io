# 组件库项目学习笔记

## 技术栈

| 语言       | typescript                                                  |
| ---------- | ----------------------------------------------------------- |
| 框架       | vue3 + less                                                 |
| 打包工具   | vite3 + gulp（自动化）                                      |
| 文档工具   | vitepress                                                   |
| 测试工具   | vitest                                                      |
| 规范化工具 | eslint、prettier、stylelint、husky、lint-staged，commitlint |
| 包管理工具 | pnpm                                                        |
| 开发idea   | vscode                                                      |



## 搭建框架

首先我们需要明白，作为一个组件库，他需要如下几大模块进行我们的开发

- 组件库核心代码，用于存放我们的组件，已经最后发包
- 展示区域，用于引入我们的组件进行测试
- 文档区域，用于我们编写我们组件的文档

根据这个架构我们先将我们的组件库设计为这样

```javascript
|- examples (展示区域)
|- docs (文档区域)
|- packages (核心源码)
|- 根目录存放大量的配置文件
```

同时我们的核心代码区域，我们这样安排，我们将代码分为需要抽取的公共方法和单独的组件，组件又分为传值的接口枚举，组件和样式这些部分，还有组件的测试用例，所以我们将核心代码区域进行这样的安排

```javascript
packages (核心源码)
|- components (组件区域)
|- - src (单独的组件)
|- - - button (例如我们的第一个组件 按钮)
|- - - - style (样式文件夹)
|- - - - - index.less (样式)
|- - - - __button__ (测试文件夹)
|- - - - - button.test.ts (测试用例)
|- - - - button.vue (样式文件)
|- - - - types.ts (接口和枚举)
|- - - - index.ts (导出文件)
|- - - index.ts (导出文件)
|- utils (公共方法)
|- 其他内容
```

## 初始化项目

我们使用 pnpm 作为我们的包管理工具 ，我们需要在 packages/components ，packages/utils ， examples 和 根目录分别初始化一个项目，之后这些项目之间将会相互引用，在他们所在的层级分别运行：

```javascript
pnpm init
```

运行完毕后他们所在的层级都会出现一个 package.json 文件。

- 根目录配置

我们需要在根目录下新建一个  .npmrc 文件，并且配置，这是设置是因为我们的项目将会包含多个 **node_modules** 模块，但是有些包必须再根目录的 模块中才有效，为了防止错误，我们配置这一项

```json
shamefully-hoist = true
```

之后我们在根目录建立一个 **pnpm-workspace.yaml** 文件，把项目关联起来，这样之后我们的模块之间就可以相互调用了 ：

```json
packages:
    - 'packages/**'
    - 'examples'
```

之后我们开始安装我们项目需要的依赖包

```shell
pnpm i vue@next typescript less -D -w
```

因为我们使用 ts 进行开发，所以我们需要在根目录配置一个 tsconfig.json 文件，你可以按照自己的需求配置，也可以直接使用如下的内容

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "jsx": "preserve",
    "strict": true,
    "target": "ES2015",
    "module": "ESNext",
    "skipLibCheck": true,
    "esModuleInterop": true,
    "moduleResolution": "Node",
    "lib": ["esnext", "dom"]
  }
}
```

因为我们的项目需要引入 vue 的模块，所以为了我们的 ts 可以识别 vue 模块，我们需要应该文件来 引入他们，在根目录新建一个 typings 文件夹 ，并且新建一个 vue-shim.d.ts 文件

```typescript
declare module '*.vue' {
    import type { DefineComponent } from "vue";
    const component:DefineComponent<{},{},any>
}
```

- examples文件夹配置

因为我们需要在examples文件夹中安装一个 vue3 的项目模板来运行展示我们开发的组件，所以我们需要安装对应的依赖

```shell
pnpm install vite @vitejs/plugin-vue -D -w
```

之后我们需要一个 vite 的配置文件来引入 vue 的插件

```json
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
    plugins:[vue()]
})
```

对于一个 vue 项目，我们新建一个 index.html 文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app"></div>
    <script src="main.ts" type="module"></script>
</body>
</html>
```

一个 app.vue 文件

```vue
<template>
    <div>
        启动测试
    </div>
</template>
```

一个 main.ts 文件

```typescript
import {createApp} from 'vue'
import App from './app.vue'

const app = createApp(App)

app.mount('#app')
```

之后我们在 package.json 文件的 scripts 字段里写入运行脚本

```json
"scripts": {
    "dev": "vite"
 },
```

之后使用 pnpm run dev 我们的项目就运行起来了

- untils 文件夹配置

同样我们使用 pnpm init 初始化这个文件夹，之后将配置文件里的 name 字段进行更改，这样这部分就是我们整个项目的一个子模块了，再将入门模块写成 ts 格式

```json
"name": "@ls-ui/utils",
"main": "index.ts",
```

之后我们新建一个简单的 index.ts 文件，编写一个简单的函数进行测试

```typescript
export const test = (a:number):number=>{
    return a 
}
```

- components 文件夹配置

和 utils 相同，我们 pnpm init  生成配置文件，并且修改配置，因为我们要发布的组件均在这个文件夹，所以我们把它命名为我们要发布的组件库的名字

```json
"name": "ls-ui",
"main": "src/index.ts",
```

然后我们新建 src/ index.ts 文件，尝试调用 utils 的模块，当然再调用模块之前，你需要 pnpm install @ls-ui/utils 来安装对应的模块

```typescript
import { test } from '@ls-ui/utils'
console.log(test (1))
```

之后我们开发一个简单的按钮来测试我们的整体流程，根据我们之前建立的流程，在 src/button/button.vue 写入

```vue
<template>
    <button>测试按钮</button>
</template>
```

在 src/button/index.ts 导出这个按钮

```typescript
import LsButton from './button.vue'
export default LsButton
```

在 src/index.ts 集中导出

```typescript
import LsButton from './button';
export { LsButton };
```

- 综合测试

我们在 examples 里调用刚刚开发完成组件，我们使用 pnpm install ls-ui安装它，然后再页面调用它

```vue
<template>
    <div>
        <LsButton />
    </div>
</template>
<script lang="ts" setup>
import { LsButton } from 'ls-ui'
</script>
```

运行你的项目，如果你看到你的项目中，页面中出现一个按钮，并且控制台出现一个输出的数字 1 那么你的架构已经搭建成功了。
