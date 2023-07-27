# vue3组件库项目学习笔记（二）：开发一个组件

在搭建了基础架构之后，我们就需要编写一个完整的组件，它应该包括数据接口，样式，组件的 vue 文件等等内容

## 编写组件

我们知道，组件都有很多的属性字段，并且部分的属性是可以枚举的，所以我们将这些会传递进组件的内容都写下来，放在 types.ts 文件中

```typescript
import type { ExtractPropTypes } from 'vue';
//枚举可以枚举字段的内容
export const ButtonType = ['primary', 'text'];

export const ButtonSize = ['large', 'small'];

export const buttonProps = {
  //判定内容在枚举字段内
  type: {
    type: String,
    validator(value: string) {
      return ButtonType.includes(value);
    }
  },
  size: {
    type: String,
    validator(value: string) {
      return ButtonSize.includes(value);
    }
  },
  //圆角
  round: Boolean,
  //禁用
  disabled: Boolean
};
//导出
export type ButtonProps = ExtractPropTypes<typeof buttonProps>;
```

之后我们来编写我们组件的核心逻辑，我们的写法如下，我们首先接收一个传入组件的参数集合，然后根据这些参数来改变我们组件的样式，到达配置我们组件的目的，最后使用 slot 插槽来将按钮内部的内容输入其中：

```vue
<script lang="ts" setup>
import "./style/index.less";
import { computed } from "vue";
import { buttonProps } from "./types";
const props = defineProps(buttonProps);
console.log(props)

const styleClass = computed(() => {
  return {
    "ls-button":true,
    [`ls-button-${props.type}`]: props.type,
    "is-round": props.round,
    "is-disabled": props.disabled,
    [`ls-button-${props.size}`]: props.size,
  };
});
</script>
<template>
  <button  :class="styleClass">
    <slot />
  </button>
</template>
```

在完成了逻辑之后，我们为其编写对应的样式，放在 style/index.less 文件中，你可以按照自己的喜好来编写自己喜欢的样式：

```less
.ls-button {
  display: inline-block;
  line-height: 1;
  white-space: nowrap;
  cursor: pointer;
  background: #fff;
  border: 1px solid #dcdfe6;
  color: #606266;
  appearance: none;
  text-align: center;
  box-sizing: border-box;
  outline: none;
  margin: 0;
  transition: 0.1s;
  font-weight: 500;
  padding: 12px 20px;
  font-size: 14px;
  border-radius: 4px;

  &:hover {
    color: #409eff;
    border-color: #c6e2ff;
    background-color: #ecf5ff;
  }
}

.ls-button-primary {
  color: #fff;
  background-color: #409eff;
  border-color: #409eff;

  &:hover {
    background: #66b1ff;
    border-color: #66b1ff;
    color: #fff;
  }
}

.ls-button-text {
  border-color: transparent;
  color: #409eff;
  background: transparent;
  padding-left: 0;
  padding-right: 0;

  &:hover {
    color: #66b1ff;
    border-color: transparent;
    background-color: transparent;
  }
}

.ls-button.is-disabled {
  color: #c0c4cc;
  cursor: not-allowed;
  background-image: none;
  background-color: #fff;
  border-color: #ebeef5;
}

.ls-button-primary.is-disabled,
.ls-button-primary.is-disabled:active,
.ls-button-primary.is-disabled:focus,
.ls-button-primary.is-disabled:hover {
  color: #fff;
  background-color: #a0cfff;
  border-color: #a0cfff;
}

.ls-button-primary.is-disabled.is-plain,
.ls-button-primary.is-disabled.is-plain:active,
.ls-button-primary.is-disabled.is-plain:focus,
.ls-button-primary.is-disabled.is-plain:hover {
  color: #8cc5ff;
  background-color: #ecf5ff;
  border-color: #d9ecff;
}

.ls-button-large {
  padding: 10px 20px;
  font-size: 14px;
  border-radius: 4px;
}

.ls-button-small {
  padding: 9px 15px;
  font-size: 12px;
  border-radius: 3px;
}

.is-round {
  border-radius: 20px;
}
```

## 使用 app.use

我们希望我们的组件可以使用 app.use 方法来挂载，所以我们需要提供一个方法，在我们的 utils 的index.ts  内把内容改写成这个方法

```typescript
import type { Plugin } from "vue"

export const withInstall = <T, E extends Record<string, any>>(main: T, extra?: E): T => {
  (main as SFCWithInstall<T>).install = (app): void => {
    for (const comp of [main, ...Object.values(extra ?? {})]) {
      app.component(comp.name, comp);
    }
  };

  if (extra) {
    for (const [key, comp] of Object.entries(extra)) {
      (main as any)[key] = comp;
    }
  }
  return main as SFCWithInstall<T> & E;
};

export type SFCWithInstall<T> = T & Plugin;
```

然后我们可以在 button 组件的 index.ts 内调用这个方法来提供组件这个能力

```typescript
import { withInstall } from '@ls-ui/utils'
import Button from './button.vue'
const LsButton = withInstall(Button)
export default LsButton
```

最后我们在 components 文件夹下 与 package.json 同级的位置创建一个 index.ts 文件，提供一个 install 方法，因为 app.use 要求我们传入的参数具有这个方法

```typescript
import components from './src/index'
export * from './src/index'
import { App } from 'vue'
import { version } from './package.json';

const install = function (app: App): void {
  Object.entries(components).forEach(([key, value]) => {
    app.component(key, value);
  });
};

export default {
  install,
  version
};
```

别忘了修改我们的 package.json 文件，让他的 main 模块指向我们的刚刚写好的文件

```xml
"main": "index.ts",
```

一切完成之后，我们就可以在 examples 里调用我们的模块了 ，在 main.ts 文件里使用 app.use 的方法来调用

```typescript
import { createApp } from 'vue';
import App from './app.vue';
import LsButton from "ls-ui"

const app = createApp(App);
app.use(LsButton as any)
app.mount('#app')
```

之后在 app.vue 文件里尝试调用它，如果不出以外，你的界面上将会现实一个蓝色的按钮，并且它具有圆角

```vue
<template>
  <div>
    <ls-button type="primary" size="small" round >
      哈哈哈哈哈
    </ls-button>
  </div>
</template>
<script lang="ts" setup>
</script>
```

## 组件Attrs与方法

我们继续完善我们的组件，比如我们希望我们的组件具有 name 属性，就想原生的 html 元素一样，我们可以定义一个props 然后再来写我们的 props属性，再绑定到组件代码上，但是其实对于这种情况，我们可以使用 Attrs 来实现。通过它可以获得绑定在组件上，但是 prosp 内不接收的属性，如果原生的 html 元素和我们的组件上都需需要某个元素并且作用相同，我们可以利用它简化我们的代码

通过 useAttrs 方法我们可以获得我们需要的内容，再通过 v-bind 绑定到我们的按钮上：

```vue
<script lang="ts" setup>
import { useAttrs  } from "vue";
//...
const attrs = useAttrs()
console.log(attrs)
//.....
</script>
<template>
  <button  :class="styleClass"  v-bind="attrs" >
    <slot />
  </button>
</template>

```

之后我们在我们的组件里传递一个 name 属，此时打开F12 定位到我们的button，可以看到我们的标签上具有 `name = "lst"` 这个字段：

```vue
<ls-button type="primary" size="small" round name="lst">
     哈哈哈哈哈
</ls-button>
```

然后我们可以理所当然的想到，一个按钮最重要的就是具有作用，比如我们点击它的时候产生一些效果，所以我们应该为我们的组件添加一个可以相应的方法，其实也很简单，和 vue 父子组件通信是一样的，以下的完整的demo：

```vue
<script lang="ts" setup>
import "./style/index.less";
import { computed , useAttrs , defineEmits } from "vue";
import { buttonProps } from "./types";
const props = defineProps(buttonProps);
console.log(props)
const attrs = useAttrs()
console.log(attrs)


const emits = defineEmits(['click'])

const styleClass = computed(() => {
  return {
    "ls-button":true,
    [`ls-button-${props.type}`]: props.type,
    "is-round": props.round,
    "is-disabled": props.disabled,
    [`ls-button-${props.size}`]: props.size,
  };
});

const myClick = ( ) => {
  emits('click', 11);
}
</script>
<template>
  <button  :class="styleClass"  v-bind="attrs" @click="myClick()">
    <slot />
  </button>
</template>
```

app.vue

```vue
<template>
  <div>
    <ls-button type="primary" size="small" round name="lst" @click="test">
      哈哈哈哈哈
    </ls-button>
  </div>
</template>
<script lang="ts" setup>
const test = ( msg:number ) => {
  console.log(msg);
};
</script>
```

然后在你点击按钮之后在控制台打印出 2 那么你的组件就编写完成了
