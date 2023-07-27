# vue3组件库项目学习笔记（三）：测试你的组件

在完成一个组件之后，我们可以看到，想要测试你的组件，需要将他引入你的项目中，然后将你的组件的内容一项一项进行测试，在属性和内容多了以后，这样做是十分没用效率的，所以我们可以选用 vite 环境自带的 vitest 进行自动化测试

## 引入测试库

首先我们将我们需要的库导入到根目录中，前者是自动化测试的框架，后者是 Vue推荐的测试库，用于进行测试覆盖率的测试

```shell
pnpm add vitest happy-dom c8 -D -w 
```

之后我们在我们需要测试的地方，也就是 components 文件夹中创建一个 vite.config.ts 文件，在这个配置文件中我们引入 vue 插件之后导入测试需要的测试插件

```typescript
/// <reference types="vitest" />
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue"
export default defineConfig(
    {
        plugins: [
            vue(),
        ],
        test: {
            environment: "happy-dom"
        },

    }
)
```

之后我们在  components  文件夹的 package.json 中写入测试的脚本，前者可以运行测试，而后者还可以进行测试覆盖率的测试

```json
  "scripts": {
    "test": "vitest",
    "coverage": "vitest run --coverage"
  },
```

在完成了测试框架的搭建以后，我们来编写我们的测试，在之前的篇章中，我们在 button 组件中预留了 `__button__` 文件夹，我们现在在其中写入一个 button.test.ts 文件，vitest 运行之后，它会自动寻找文件夹中以

`**.{test,spec}.{js,mjs,cjs,ts,mts,cts,jsx,tsx}` 为格式的文件，运行其中的测试

下面简单讲解一下 vitest 的写法：

- **describe** 此函数接受名称和函数，用于将相关测试组合在一起。当您为具有多个测试点（例如逻辑和外观）的组件编写测试时，它会派上用场。 
- **it** 这个函数代表被测试的实际代码块。它接受一个字符串，该字符串通常是测试用例的名称或描述
- **expect** 此函数用于测试值或创建断言，内部包含一个测试的值 ，后接一个判断的条件
- **toBe**  表示前面的结果要提供的一致才能通过测试

```typescript
import { describe, expect, it } from "vitest";

describe("one plus oen is two", () => {
    it("should be 2", () => {
        expect(1 + 1).toBe(2)
    })
}
```

## 编写测试

因为我们的项目主要是组件库，所以我们需要在测试的时候先创建一个组件，再对组件的各项内容进行测试，所以这个时候我们就需要用到 `@vue/test-utils` 这个工具了

```shell
pnpm add @vue/test-utils -D -w   
```

这个工具主要提供了一个`mount`方法，我们通过`mount`实例化一个组件，传入不同参数来测试组件是否符合我们的预期，例如：

```typescript
import { describe, expect, it } from 'vitest';
import { mount } from '@vue/test-utils';
import button from '../button.vue';

describe('test Button', () => {
  it('slot test', () => {
    const wrapper = mount( button, {
      slots: {
        default: 'Hello world'
      }
    });
    expect(wrapper.text()).toContain('Hello world');
  });
});
```

以上的例子中，我们创建了一个 button 组件，然后将我们的传入一个 `'Hello world'` 的 slot，之后我们测试我们组件的 text 是不是包含 `'Hello world'` 这个内容我们在文件夹下运行 `pnpm run test` 可以看到测试成功，输出了以下的内容：

```shell
Test Files   1 passed (1)
      Tests  1 passed (1)
   Start at  21:05:23
   Duration  78ms
```

更多的测试相关的 api 内容可以在： https://cn.vitest.dev/guide/ 查看

关于更多 `@vue/test-utils` 的使用可以在  https://v1.test-utils.vuejs.org/zh/guides/ 查看

## 覆盖率测试

我们为我们的组件库编写两个测试，分别测试我们的类和slot ：

```typescript
describe('test Button', () => {
  it('slot test', () => {
    const wrapper = mount(button, {
      slots: {
        default: 'Hello world'
      }
    });
    expect(wrapper.text()).toContain('Hello world');
  });
  it('class test', () => {
    const wrapper = mount(button, {
      props: {
        type: 'primary'
      }
    });
    expect(wrapper.classes()).toContain('ls-button-primary');
  });
});

```

之后我们运行我们的 `pnpm run coverage` 脚本，可以看到，运行结果中，它会打印出一张表格

```shell
 % Coverage report from c8
------------|---------|----------|---------|---------|-------------------
File        | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
------------|---------|----------|---------|---------|-------------------
All files   |   92.85 |      100 |   28.57 |   92.85 |
 button.vue |   93.54 |      100 |       0 |   93.54 | 24-25
 types.ts   |      92 |      100 |      40 |      92 | 18-19
------------|---------|----------|---------|---------|-------------------
```

它的含义是：

- %stmts是语句覆盖率（statement coverage）：是不是每个语句都执行了？

- %Branch分支覆盖率（branch coverage）：是不是每个if代码块都执行了？

- %Funcs函数覆盖率（function coverage）：是不是每个函数都调用了？

- %Lines行覆盖率（line coverage）：是不是每一行都执行了？

可以看到，因为我们的测试中没用测试 size 和 click函数的内容，我们的函数覆盖率是不够的，，最后一列也告诉了我们那么并没有被覆盖到，如果之后有需要我会继续补充相关我完整文档。



## 在根目录运行所有的测试内容

至此我们的组件测试包含了一个在 examples 文件夹内的引入组件的可视化测试，以及一个在 packages/components 文件夹内的自动化测试，如果我们想要运行每个内容需要经常切换文件夹，这是十分复杂的，所以我们在根目录的 package.json 中写入如下的内容：

```json
  "scripts": {
    "exm:dev": "pnpm run --filter ./examples dev",
    "test": "pnpm run --filter ./packages/components test",
    "coverage": "pnpm run --filter ./packages/components coverage"
  },
```

这些脚本可以让我们在根目录运行执行文件夹下方的 package.json 内部的脚本，使用这些脚本，我们就可以在根目录完成我们需要的操作，至此一个包含了测试的组件库框架已经搭建好了

