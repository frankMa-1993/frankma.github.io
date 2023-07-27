# vue3组件库项目学习笔记（七）：正式开发问题拾遗

目前组件库的开发还在进行中，这里把一些开发过程中遇到的问题和解决方案总结一下，也方便后续开发的同学踩坑了可以马上解决问题，这期的问题主要是：

- 多项目开发的配置文件优化和全局导入方法的修改
- 怎么样使用 icon 组件
- 怎么样使用做组件协作开发

想要看代码的可以直接前往我的 github：https://github.com/aiai0603/seven-bit-ui

想要使用我的组件库的可以直接下载：seven-bit-ui （原来的名字被占用了所以我换了一个）

## 配置文件优化

按照上一篇的结构搭建的项目还有几个需要优化的地方，这里我说一下：

我们在完成我们的结构之后发现，我们的项目在根目录使用 pnpm run build 命令的时候报错了，提示我们不能使用 require，这里不知道为什么产生了这样的问题，应该是部分包的效果导致的，但是你只要修改根目录的 tsconfig.json 文件，把 module 项目修改即可：

```json
"module": "commonjs",
```

## 版本号注意点

之后就是我们需要优化我们的项目编号来解决 pnpm 安装错误的问题，目前我们的项目分为三个，

- 一个我们本地开发的版本，这个版本在 packages/components/package.json 这个文件中定义
- 一个是打包后的版本，这个版本的包在 packages/components/transitpkg/package.json 这个文件中，当我们发布以后，它还会存在在 packages/components/dist/package.json ，两者的package.json 编号是一致的
- 最后一个是我们 publish 上去的包

这三个包分别有什么用呢？

- 我们本地开发版本的包，如果我们在 examples 上安装了它，当我们在 packages/components 中修改我们的代码的时候，examples 中安装的版本会直接和我们修改的代码同步，所以我们在 examples 中安装它可以快速的测试我们编写的组件，作者将它的版本编写为 0.0.1
-  当我们打包完成的时候，我们可以在本地安装我们的打包版本来测试我们的项目打包后是否正常，我们需要的操作是，首先将我们的transitpkg/package.json 复制到 dist 文件夹下，因为只有 publish 命令会干这个事情，在我们发布之前测试的话我们需要手动干这个事情，之后我们需要修改 transitpkg/package.json 中的版本号，因为如果存在两个一样的版本号，pnpm 遍历的时候可能会直接安装 transitpkg/package.json 的项目，但是这个项目是空的，然后我们根据我们 dist/package.json 的版本号来安装我们的项目，就可以安装上我们打包好的项目了
- 当我们把项目上传到 npm ，我们就可以在任何地方使用我们的组件库，比如你的个人博客项目中，你可以在本地新建一个 vue 项目来测试你的组件库

## 全局安装和局部引用

这里作者被坑了好久，认真学习了以后，我们从原理说起：

当我们使用这样的方式导出我们的组件的时候

```javascript
export { sbButton, sbLink, sbIcon, sbCol, sbRow, sbModal, sbProgress, sbBread, sbBreadItem };
```

如果我们需要在项目中引用我们的组件，我们需要：

```javascript
import  { sbButton } from 'seven-bit-ui'
```

当我们需要使用 app.use 来安装我们的组件的时候，我们需要给我们的组件一个 install 方法，所以在导出组件的时候，我们为他提供了一个方法：（这里把 withInstall 拿出来是为了方便演示，你应该把它放在单独的一个 utils 中正如之前教程说的那样）

```javascript
import Button from './sbButton.vue';

const withInstall = (comp: any) => {
  comp.install = (app: any) => {
    //注册组件
    app.component(comp.__name, comp);
  };
  return comp;
};

const sbButton = withInstall(Button);
export default sbButton;
```

如果我们需要用户直接导入我们的整个组件库，我们可以使用，我们需要为我们的整个组件库提供一个方法来安装所有的组件：
```javascript
const components = [sbButton, sbLink, sbIcon, sbCol, sbRow, sbBread, sbBreadItem, sbModal, sbProgress];
export default {
  install: (app: any) => {
    for (const comkey in components) {
      app.component(components[comkey].__name, components[comkey]);
    }
  }
};
```

这样之后我们就支持使用单独安装和全部安装两种方式来引入我们的组件，像这样：

```javascript
import sbui from 'seven-bit-ui';
const app = createApp(App);
app.use(sbui);

import  { sbButton } from 'seven-bit-ui'
const app = createApp(App);
app.use(sbButton);
```

所以最后我们的整个目录修改成这样：

- 在 packages / components / src 文件夹的有一个总的 inex.ts，提供全局安装方法，并且导出所有的组件，像这样：

  ```javascript
  import { default as sbButton } from './sbButton';
  import { default as sbLink } from './sbLink';
  import { default as sbModal } from './sbModal';
  import { default as sbProgress } from './sbProgress';
  import { default as sbIcon } from './sbIcon';
  import { default as sbCol } from './sbCol';
  import { default as sbRow } from './sbRow';
  import { default as sbBread } from './sbBread';
  import { default as sbBreadItem } from './sbBreadItem';
  const components = [sbButton, sbLink, sbIcon, sbCol, sbRow, sbBread, sbBreadItem, sbModal, sbProgress];
  export default {
    install: (app: any) => {
      for (const comkey in components) {
        app.component(components[comkey].__name, components[comkey]);
      }
    }
  };
  export { sbButton, sbLink, sbIcon, sbCol, sbRow, sbModal, sbProgress, sbBread, sbBreadItem };
  ```

- 而在 src 下的每个组件的文件夹的 index.ts 文件里，我们需要为每个组件单独提供install 方法：

  ```javascript
  import Button from './sbButton.vue';
  const withInstall = (comp: any) => {
    comp.install = (app: any) => {
      app.component(comp.__name, comp);
    };
    return comp;
  };
  const sbButton = withInstall(Button);
  export default sbButton;
  ```

最后作者发现之前我们在 components  文件夹下面也放一个 index.ts 进行操作，这样会导致打包的时候全局安装的方法打包不进去，所以我们将全局安装的方法安排到了 components / src / index.ts 中，任何我们需要将 packages/components/package.json 进行修改，把 main 入口的内容进行修改：

```json
"main": "src/index.ts",
```

## 引用iconfont

显然在开发 icon 组件的时候，我们会觉得自己设计 svg 是吃力不讨好的，除非你需要证明你设计方面的技能，所以我们一般会选择使用现有的 icon 设计，这里我们选择 iconfont ，地址是：https://www.iconfont.cn/

下面是一个简单教你引入 iconfont 资源为自己组件的方案

首先我们注册一个自己的账号，创建一个自己的项目，在   资源管理——我的项目 就可以创建项目了，然后注意在创建的时候  FontClass/Symbol 前缀 这一项改成我们项目的名字，比如我改成了 sb-icon-，那么我们到时候引用项目的时候需要使用 sb-icon-xxx 来引用我们的图标，可以防止和其他人的重名，其他的项目配置默认即可

![image-20230203212455458](/img/2.png)

之后我们可以直接选择一个图标，添加入库，也就是点击购物车的图标

![image-20230203212313648](/img/1.png)

之后我们点击最上方右侧的购物车将我们选择的图标添加到我们的项目：

<img src="/img/3.png" alt="image-20230203212714573" style="zoom:50%;" />

最后我们导出我们的整个项目的代码，我们进入我的项目，选择我们创建的项目，点击下载至本地：

![image-20230203212852740](/img/4.png)

这时候我们得到了一个 zip 文件，解压它，取出其中的 iconfont.js 这是我们需要的内容，我们进入我们编写的 icon 组件：

我们引入我们的 iconfont.js ，你可以选择把它放在其他位置，这是你的自由，然后将你的 template 按照作者的方式编写，然后动态生成 iconName 就行了，此时你的用户只需要传入一个 name 就可以展示对应的图标

```vue
<template>
  <svg aria-hidden="true" v-bind="attrs">
    <use :xlink:href="iconName"></use>
  </svg>
</template>
<script lang="ts" setup>
  import './font/iconfont.js';
  import './style/index.less';
  import { computed, useAttrs } from 'vue';
  import { iconProps } from './types';
  const props = defineProps(iconProps);
  const attrs = useAttrs();

  const iconName = computed(() => {
    return `#sb-icon-${props.name}`;
  });
</script>

```

## 多组件协作

开发面包屑的时候遇到了问题，我们希望我们可以配置我们组件的面包屑间隔图标，比如我希望我的面包屑是 a / b / c 的形式，而另一个人希望是 a > b > c 这样的形式，但是我们的面包屑一般是这样的形式：

```vue
  <sb-bread>
      <sb-bread-item>111</sb-bread-item>
      <sb-bread-item href="#">222</sb-bread-item>
      <sb-bread-item>333</sb-bread-item>
   </sb-bread>
```

你不知道你有几个子项目，你需要在 sb-bread 这个父组件里将信息分发给下面的子项目，但是你有不确定有没有子组件，类似这样：

```vue
  <sb-bread separator=">">
      <sb-bread-item>111</sb-bread-item>
      <sb-bread-item href="#">222</sb-bread-item>
      <sb-bread-item>333</sb-bread-item>
   </sb-bread>
```

作者根据查阅资料使用的方案是这样的，在 sb-bread 里我们使用 provide 将信息传递下去，如果 sb-bread-item 是 sb-bread 的子项目，就可以使用  inject 拿到我们提供的内容，这样就可以使用实现我们自定义分隔符的渲染了，一个简单是例子是：（完整的代码欢迎去我的 git 查看 https://github.com/aiai0603/seven-bit-ui ）

```vue
<template>
  <div ref="breadcrumb" :class="styleClass" v-bind="attrs">
    <slot />
  </div>
</template>
<script lang="ts" setup>
  import './style/index.less';
  import '../sbIcon';
  import { computed, provide, ref, useAttrs } from 'vue';
  import { breadProps } from './types';
  const props = defineProps(breadProps);
  const attrs = useAttrs();
  const breadcrumb = ref<HTMLDivElement>();

  provide('breadcrum', props);

  const styleClass = computed(() => {
    return {
      'sb-bread': true
    };
  });
</script>
```

```vue
<template>
  <a v-if="props.href" class="sb-bread-item sb-bread-is-link" :href="props.href">
    <slot />
  </a>
  <div v-else class="sb-bread-item">
    <slot />
  </div>
  <sb-icon v-if="separatorIcon" class="sb-bread-item-separator" :name="separatorIcon"></sb-icon>
  <span v-else class="sb-bread-item-separator">
    {{ separator }}
  </span>
</template>
<script lang="ts" setup>
  import './style/index.less';
  import { sbIcon } from '../index';
  import { inject, toRefs } from 'vue';
  import { breadItemProps } from './types';
  const props = defineProps(breadItemProps);

  const breadContext = inject('breadcrum');

  const { separator, separatorIcon } = toRefs(breadContext as any);
</script>
```

## less 来编写动态的样式

我们在编写 grid 组件的时候，需要按照传统将我们的一个块 24 等分，我们根据这 24 等分来写我们的样式，如果我们根据传入是数据来使用 style 计算我们的样式，那就好麻烦了，一个组件要绑定多个 style 而且写在组件上的 style 层级很高，用户很难去修改，所以我们使用 less 循环来编写一组 1-24 等分的样式：

```less
.for(@i) when(@i <= 24) {
  .for(@i + 1);
  @size: (100% * @i / 24);
  .sb-col-span-@{i} {
    display: block;
    width: @size;
    flex: 0 0 @size;
  }
}
.for(1);
```

这里定义了一个函数叫 for ，传入一个参数 i 循环到 i 等于24的时候；对于每个 i 我们计算出它对应的比例是多少，注意此处的计算一定要带上 () 包裹，否则无法识别，之后我们再将我们计算出来的结果绑定到我们需要的 class 属性上，上述的例子相当于是生成了如下的24个类，使用循环可以快速帮助我们生成我们需要的内容：

```less
.sb-col-span-1 {
    display: block;
    width: 100% * 1 / 24;
    flex: 0 0 100% * 1 / 24;
 }
.sb-col-span-2 {
    display: block;
    width: 100% * 2 / 24;
    flex: 0 0 100% * 2 / 24;
 }
/***省略**/
.sb-col-span-24 {
    display: block;
    width: 100%;
    flex: 0 0 100%;
 }
```

## 结尾

以上是作者在开发过程中遇到的问题和处理的方案，作者还在持续更新组件库，后续将会有机会继续更新其他内容，欢迎关注作者，订阅收藏专栏来关注后续

源代码地址： https://github.com/aiai0603/seven-bit-ui
