# vue3组件库项目学习笔记（五）：配置编码规范

现在我们已经拥有了一个可以发布的组件库，但是大家都知道，现在市面上的组件库基本上都是开源维护的或者团队开发，独立的开发组件库工具，所以想要在团队协作的时候更好的编码，也为了使得我们的代码更加规范，我们需要配置我们的组件库规范，以下介绍几个大家比较耳熟能详的工具的配置，大家也可以选择自己喜欢的工具进行开发：

## 配置Eslint

**eslint** 是一个非常通用的代码质量检查工具，可以通过配置文件对代码的质量进行约束和修复，我们首先还是导入依赖

```shell
pnpm i eslint -D -w
```

因为我们是基于 vue 和 ts 的项目，我们还需要导入相关的依赖，因为 eslint 默认只支持 js 的解析

```shell
pnpm i eslint-plugin-vue@latest @typescript-eslint/eslint-plugin@latest @typescript-eslint/parser@latest -D -w
```

关于 eslint 的个性化配置这里就不多介绍了，我们只讲如何引入它，想了解规则定义的可以看相关的文档

https://eslint.org/docs/latest/rules/

我们在 components 文件夹下面新建一个eslint-config文件夹，并且进行初始化 package.json ，我们将在这个项目中编写我们全部的规则：

```json
{
  "name": "@ls-ui/eslint-config",
  "version": "0.0.1",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "zs",
  "license": "MIT"
}
```

我们在项目中新建一个 index.js 入口文件，作为我们 eslint 的配置文件

```javascript
const eslintRules = require('./eslint.rules');
const tsRules = require('./ts.rules');
const vueRules = require('./vue.rules');

module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true
  },
  extends: ['plugin:vue/vue3-essential', 'prettier', 'plugin:@typescript-eslint/recommended'],
  overrides: [],
  parser: 'vue-eslint-parser',
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
    parser: '@typescript-eslint/parser'
  },
  plugins: ['vue', '@typescript-eslint', 'prettier'],
  rules: {
    ...eslintRules,
    ...tsRules,
    ...vueRules
  }
};
```

之后我们分别编写三个文件作为我们规则的配置

- eslint.rules.js

```javascript
//eslint.rule.js
/* eslint配置规则 https://eslint.org/docs/latest/rules/ */
module.exports = {
  'prettier/prettier': 'error',
  'arrow-body-style': 'off',
  'prefer-arrow-callback': 'off',
  'no-console': 2,
  // 不允许不必要的转义字符 https://eslint.org/docs/latest/rules/no-useless-escape
  'no-useless-escape': 'off',
  'comma-dangle': 'off',
  // 禁止使用 var https://eslint.org/docs/latest/rules/no-var#rule-details
  'no-var': 'error',
  // 使用单引号 https://eslint.org/docs/latest/rules/quotes#version
  quotes: ['error', 'single'],
  // 禁止分号 https://eslint.org/docs/latest/rules/semi#rule-details
  // semi: 'error',
  // 禁止 debugger https://eslint.org/docs/latest/rules/no-debugger#rule-details
  'no-debugger': 'error',
  // 禁止未使用的变量 https://eslint.org/docs/latest/rules/no-unused-vars#rule-details
  'no-unused-vars': 'error',
  // 不允许使用未声明的变量 https://eslint.org/docs/latest/rules/no-undef
  'no-undef': 'off',
  // 函数括号前的空格 https://eslint.org/docs/latest/rules/space-before-function-paren
  'space-before-function-paren': 'off',
  // 禁止多个空行 https://eslint.org/docs/latest/rules/no-multiple-empty-lines#rule-details
  'no-multiple-empty-lines': ['error', { max: 1, maxEOF: 0, maxBOF: 0 }],
  // 在文件末尾要求或禁止换行 https://eslint.org/docs/latest/rules/eol-last#rule-details
  'eol-last': 'error',
  // 禁止所有选项卡 https://eslint.org/docs/latest/rules/no-tabs#rule-details
  'no-tabs': 'error',
  'default-param-last': 'off'
};
```

- ts.rules.js

```javascript
/* ts配置规则  https://typescript-eslint.io/rules */
module.exports = {
  // 规定数组类型定义方式 https://typescript-eslint.io/rules/array-type
  '@typescript-eslint/array-type': 'error',
  // 禁止使用大写 String、Number 定义类型 https://typescript-eslint.io/rules/ban-types
  '@typescript-eslint/ban-types': 'off', // beta
  // 不允许尾随逗号 https://typescript-eslint.io/rules/comma-dangle
  '@typescript-eslint/comma-dangle': 'error',
  // 强制使用 interface 定义类型 https://typescript-eslint.io/rules/consistent-type-definitions
  '@typescript-eslint/consistent-type-definitions': ['error', 'interface'],
  // 统一导出规则 https://typescript-eslint.io/rules/consistent-type-exports
  // '@typescript-eslint/consistent-type-exports': 'error',
  // 自定义对象类型样式 https://typescript-eslint.io/rules/consistent-indexed-object-style
  '@typescript-eslint/consistent-indexed-object-style': ['warn', 'record'],
  // !禁止使用后缀运算符的非空断言 https://typescript-eslint.io/rules/no-non-null-assertion/
  '@typescript-eslint/no-non-null-assertion': 'error',
  // 强制一致地使用类型导入 https://typescript-eslint.io/rules/consistent-type-imports
  '@typescript-eslint/consistent-type-imports': ['error', { prefer: 'type-imports' }],
  // 禁止未使用的变量 https://typescript-eslint.io/rules/no-unused-vars
  '@typescript-eslint/no-unused-vars': 'error',
  // 不可以有 any https://typescript-eslint.io/rules/no-explicit-any/
  '@typescript-eslint/no-explicit-any': 'off',
  // 不可以有 require https://typescript-eslint.io/rules/no-var-requires/
  '@typescript-eslint/no-var-requires': 'off',
  // 带有默认值的函数参数在最后 https://typescript-eslint.io/rules/default-param-last
  '@typescript-eslint/default-param-last': 'error',
  // 必须标记函数返回值 https://typescript-eslint.io/rules/explicit-function-return-type
  '@typescript-eslint/explicit-function-return-type': 'off'
};
```

- vue.rules.js

```javascript
/* vue配置规则 https://eslint.vuejs.org/rules/ */
module.exports = {
  /* 禁止在模板中使用 this https://eslint.vuejs.org/rules/this-in-template.html */
  'vue/this-in-template': 'error',
  /* 关闭名称校验 https://eslint.vuejs.org/rules/multi-word-component-names.html */
  'vue/multi-word-component-names': 'off',
  'vue/max-attributes-per-line': ['error', { singleline: { max: 30 }, multiline: { max: 30 } }],
  /*  组件标签顺序 https://eslint.vuejs.org/rules/component-tags-order.html */
  'vue/component-tags-order': ['error', { order: ['template', 'script', 'style'] }],
  /* 只允许使用ts类型的script https://eslint.vuejs.org/rules/block-lang.html */
  'vue/block-lang': ['error', { script: { lang: 'ts' } }],
  /* 只允许使用 setup script类型的语法 https://eslint.vuejs.org/rules/component-api-style.html */
  'vue/component-api-style': ['error', ['script-setup', 'composition']],
  /* 自定义事件强制使用中划线连接 https://eslint.vuejs.org/rules/custom-event-name-casing.html */
  'vue/custom-event-name-casing': ['error', 'kebab-case', { ignores: [] }],
  /* 禁止使用 v-html  https://eslint.vuejs.org/rules/no-v-html.html */
  'vue/no-v-html': 'error',
  /* bind绑定时能简写就直接简写 https://eslint.vuejs.org/rules/prefer-true-attribute-shorthand.html */
  'vue/prefer-true-attribute-shorthand': 'error',
  /* 模板中未使用的组建不允许注册 https://eslint.vuejs.org/rules/no-unused-components.html */
  'vue/no-unused-components': 'error',
  /* 单标签禁止改写成双标签 https://eslint.vuejs.org/rules/html-self-closing.html */
  'vue/html-self-closing': 'off',
  /* 强制标签闭合 https://eslint.vuejs.org/rules/html-end-tags.html */
  'vue/html-end-tags': 'error',
  /* 模板缩进规则 */
  'vue/html-indent': ['error', 2, { attribute: 1, baseIndent: 1, closeBracket: 0, alignAttributesVertically: true }],
  /* 模板中使用组建必须使用中划线小写形式 https://eslint.vuejs.org/rules/component-name-in-template-casing.html */
  'vue/component-name-in-template-casing': ['error', 'kebab-case', { registeredComponentsOnly: false }],
  // 对模板中的自定义组件强制执行属性命名样式 https://eslint.vuejs.org/rules/attribute-hyphenation.html#vue-attribute-hyphenation
  'vue/attribute-hyphenation': 'error',
  // Prop 名称强制使用特定大小写 https://eslint.vuejs.org/rules/prop-name-casing.html
  'vue/prop-name-casing': 'error',
  // 强制执行 v-on 事件命名样式 https://eslint.vuejs.org/rules/v-on-event-hyphenation.html
  'vue/v-on-event-hyphenation': 'error',
  // 不允许字段名称重复 https://eslint.vuejs.org/rules/no-dupe-keys.html
  'vue/no-dupe-keys': 'error',
  // 禁止 v-if / v-else-if 链中的重复条件
  'vue/no-dupe-v-else-if': 'error',
  // 不允许重复属性 https://eslint.vuejs.org/rules/no-duplicate-attributes.html
  'vue/no-duplicate-attributes': [
    'error',
    {
      allowCoexistClass: true,
      allowCoexistStyle: true
    }
  ],
  // 不允许 export 进入 <script setup> https://eslint.vuejs.org/rules/no-export-in-script-setup.html
  'vue/no-export-in-script-setup': 'error',
  // 不允许修改 props https://eslint.vuejs.org/rules/no-mutating-props.html
  'vue/no-mutating-props': 'error',
  // 不允许解析错误的 template https://eslint.vuejs.org/rules/no-parsing-error.html
  'vue/no-parsing-error': [
    'error',
    {
      'abrupt-closing-of-empty-comment': true,
      'absence-of-digits-in-numeric-character-reference': true,
      'cdata-in-html-content': true,
      'character-reference-outside-unicode-range': true,
      'control-character-in-input-stream': true,
      'control-character-reference': true,
      'eof-before-tag-name': true,
      'eof-in-cdata': true,
      'eof-in-comment': true,
      'eof-in-tag': true,
      'incorrectly-closed-comment': true,
      'incorrectly-opened-comment': true,
      'invalid-first-character-of-tag-name': true,
      'missing-attribute-value': true,
      'missing-end-tag-name': true,
      'missing-semicolon-after-character-reference': true,
      'missing-whitespace-between-attributes': true,
      'nested-comment': true,
      'noncharacter-character-reference': true,
      'noncharacter-in-input-stream': true,
      'null-character-reference': true,
      'surrogate-character-reference': true,
      'surrogate-in-input-stream': true,
      'unexpected-character-in-attribute-name': true,
      'unexpected-character-in-unquoted-attribute-value': true,
      'unexpected-equals-sign-before-attribute-name': true,
      'unexpected-null-character': true,
      'unexpected-question-mark-instead-of-tag-name': true,
      'unexpected-solidus-in-tag': true,
      'unknown-named-character-reference': true,
      'end-tag-with-attributes': true,
      'duplicate-attribute': true,
      'end-tag-with-trailing-solidus': true,
      'non-void-html-element-start-tag-with-trailing-solidus': false,
      'x-invalid-end-tag': true,
      'x-invalid-namespace': true
    }
  ],
  // 禁止使用 ref() 包装的值作为操作数 https://eslint.vuejs.org/rules/no-ref-as-operand.html
  'vue/no-ref-as-operand': 'error',
  // 不允许在组件定义中使用保留名称 https://eslint.vuejs.org/rules/no-ref-as-operand.html
  'vue/no-reserved-component-names': 'error',
  // 不允许覆盖保留键 https://eslint.vuejs.org/rules/no-reserved-keys.html
  'vue/no-reserved-keys': 'error',
  // 禁止 props 中的保留名称 https://eslint.vuejs.org/rules/no-reserved-props.html
  'vue/no-reserved-props': [
    'error',
    {
      vueVersion: 3
    }
  ],
  // 不允许计算属性中的副作用 https://eslint.vuejs.org/rules/no-side-effects-in-computed-properties.html
  'vue/no-side-effects-in-computed-properties': 'error',
  // 禁用key属性<template> https://eslint.vuejs.org/rules/no-template-key.html
  'vue/no-template-key': 'error',
  // 不允许在 textarea 中写 {{}} https://eslint.vuejs.org/rules/no-textarea-mustache.html
  'vue/no-textarea-mustache': 'error',
  // 禁止 v-for 指令或范围属性的未使用变量定义 https://eslint.vuejs.org/rules/no-unused-vars.html
  'vue/no-unused-vars': 'error',
  // 不允许调用计算属性 https://eslint.vuejs.org/rules/no-use-computed-property-like-method.html
  'vue/no-use-computed-property-like-method': 'error',
  // 禁止在与 v-for 相同的元素上使用 v-if https://eslint.vuejs.org/rules/no-use-v-if-with-v-for.html
  'vue/no-use-v-if-with-v-for': [
    'error',
    {
      allowUsingIterationVar: true
    }
  ],
  // 禁止无用的属性<template> https://eslint.vuejs.org/rules/no-useless-template-attributes.html
  'vue/no-useless-template-attributes': 'error',
  // 禁止组件上的 v-text / v-html https://eslint.vuejs.org/rules/no-v-text-v-html-on-component.html
  'vue/no-v-text-v-html-on-component': 'error',
  // 要求 prop 类型是构造函数 https://eslint.vuejs.org/rules/require-prop-type-constructor.html
  'vue/require-prop-type-constructor': 'error',
  // 强制渲染函数始终返回值 https://eslint.vuejs.org/rules/require-render-return.html
  'vue/require-render-return': 'error',
  // V-bind:key使用v-for指令要求 https://eslint.vuejs.org/rules/require-v-for-key.html
  'vue/require-v-for-key': 'error',
  // 强制 props 默认值有效 https://eslint.vuejs.org/rules/require-valid-default-prop.html
  'vue/require-valid-default-prop': 'off',
  // 强制返回语句存在于计算属性中 https://eslint.vuejs.org/rules/return-in-computed-property.html
  'vue/return-in-computed-property': 'error',
  // 需要有效的属性名称 https://eslint.vuejs.org/rules/valid-attribute-name.html
  'vue/valid-attribute-name': 'off',
  // 强制执行有效的模板根 https://eslint.vuejs.org/rules/valid-template-root.html
  'vue/valid-template-root': 'off',
  // 执行有效v-bind指令 https://eslint.vuejs.org/rules/valid-v-bind.html
  'vue/valid-v-bind': 'error',
  // 禁止在数据上使用不推荐使用的对象声明 https://eslint.vuejs.org/rules/no-deprecated-data-object-declaration.htm
  'vue/no-deprecated-data-object-declaration': 'error',
  // 禁止使用已弃用 destroyed 和 beforeDestroy 生命周期挂钩 https://eslint.vuejs.org/rules/no-deprecated-destroyed-lifecycle.html
  'vue/no-deprecated-destroyed-lifecycle': 'error',
  // 禁止使用已弃用 $listeners https://eslint.vuejs.org/rules/no-deprecated-dollar-listeners-api.html
  'vue/no-deprecated-dollar-listeners-api': 'error',
  // 禁止 <template v-for> 放置在子元素上的键 https://eslint.vuejs.org/rules/no-v-for-template-key-on-child.html
  'vue/no-v-for-template-key-on-child': 'error',
  // 强制从 vue 导入，而不是从 @vue/* 导入 https://eslint.vuejs.org/rules/prefer-import-from-vue.html
  'vue/prefer-import-from-vue': 'error',
  // 要求控制里面内容的显示 <transition> https://eslint.vuejs.org/rules/require-toggle-inside-transition.html
  'vue/require-toggle-inside-transition': 'error',
  // 执行有效v-is指令 https://eslint.vuejs.org/rules/valid-v-is.html
  'vue/valid-v-is': 'error',
  // 对组件定义名称强制使用特定大小写 https://eslint.vuejs.org/rules/component-definition-name-casing.htm
  'vue/component-definition-name-casing': ['error', 'PascalCase'],
  // 强制执行属性顺序 https://eslint.vuejs.org/rules/attributes-order.html
  'vue/attributes-order': [
    'error',
    {
      order: ['DEFINITION', 'LIST_RENDERING', 'CONDITIONALS', 'RENDER_MODIFIERS', 'GLOBAL', ['UNIQUE', 'SLOT'], 'TWO_WAY_BINDING', 'OTHER_DIRECTIVES', 'OTHER_ATTR', 'EVENTS', 'CONTENT'],
      alphabetical: false
    }
  ],
  // 在单行元素的内容之前和之后需要换行符 https://eslint.vuejs.org/rules/singleline-html-element-content-newline.html
  'vue/singleline-html-element-content-newline': 'off',
  // 需要 props 的默认值 https://eslint.vuejs.org/rules/require-default-prop.html
  'vue/require-default-prop': 'error',
  // emit 必须是已经声名的方法 https://eslint.vuejs.org/rules/require-explicit-emits.html
  'vue/require-explicit-emits': [
    'error',
    {
      allowProps: false
    }
  ],
  // props 必须定义详细的类型 https://eslint.vuejs.org/rules/require-prop-types.html
  'vue/require-prop-types': 'error',
  // 支持＜template＞中的注释指令 https://eslint.vuejs.org/rules/comment-directive.html
  'vue/comment-directive': 'off'
};
```

之后我们回到根目录编写一个 .eslintrc 文件，在这里我们配置引入我们刚刚写好的规则包

```json
{
  "root": true,
  "extends": ["@ls-ui/eslint-config"]
}
```

同时我们配置一个 .eslintignore 文件，在文件里配置的内容不会被检测，一般我们将 node_modules 里的包，打包生成的内容等信息忽略，这个文件的书写规则和 git 的 .gitignore规则一致

```json
node_modules/*
packages/components/script/*
packages/components/coverage/*
packages/components/dist/*
```

## 引入 Prettier

代码质量其实更多的是语法方面的校验，如果想要我们的风格实现统一，比如，一行多少字，要不要分号，要不要双引号等等这些关于代码风格的统一需要用到`prettier`，同样我们下载它的依赖

```shell
pnpm i prettier -D -w
```

之后我们新建配置文件和忽略文件，分别是`prettier.config.js`和`.prettierignore`文件，具体配置可以查看官网 

https://prettier.io/docs/en/options.html#single-attribute-per-line

```javascript
module.exports = {
  printWidth: 120, //最大单行长度
  tabWidth: 2, //每个缩进的空格数
  useTabs: false, //使用制表符而不是空格缩进行
  semi: true, //在语句的末尾打印分号
  vueIndentScriptAndStyle: true, //是否缩进 Vue 文件中的代码<script>和<style>标签。
  singleQuote: true, //使用单引号而不是双引号
  quoteProps: 'as-needed', //引用对象中的属性时更改 "as-needed" "consistent" "preserve"
  bracketSpacing: true, //在对象文字中的括号之间打印空格
  trailingComma: 'none', //在对象或数组最后一个元素后面是否加逗号（在ES5中加尾逗号）
  arrowParens: 'avoid', //箭头函数只有一个参数的时候是否使用括号 always：使用  avoid： 省略
  insertPragma: false, //是否在文件头部插入一个 @format标记表示文件已经被格式化了
  htmlWhitespaceSensitivity: 'strict', //HTML 空白敏感性 css strict ignore
  endOfLine: 'auto', //换行符使用什么
  tslintIntegration: false //不让ts使用prettier校验
};
```

为了 prettier 和 eslint 同时起作用，我们还需要下载一些依赖

```shell
pnpm i eslint-plugin-prettier eslint-config-prettier -D -w
```

最后我们在 package.json 里添加几条命令来允许我们的检查和修复命令，第一条是运行 eslint 检查并且修复问题，但是有些问题不能直接修复；第二条命令可以找出所有的问题，第三条命令是用 prettier 检查并且问题格式问题：

```json
"scripts": {
    "lint:fix": "eslint . --fix",
    "lint:eslint": "eslint .",
    "lint:prettier": "prettier --write ."
},
```

## 配置 stylelint

再配置了我们的 js 和 vue 代码检查之后，我们还希望我们的样式会被检查，所以我们使用 stylelint 进行配置，同样还是导入依赖：

```shell
pnpm i stylelint -D -w
pnpm i stylelint-less -D -w
pnpm i postcss-less -D -w
pnpm i stylelint-config-standard -D -w
pnpm i stylelint-config-prettier -D -w
```

也是一样的配置`stylelint.config.js` 和 `.stylelintignore ` 文件：

具体可以参考官网 https://stylelint.io/user-guide/rules/selector-class-pattern/

```javascript
module.exports = {
  root: true,
  plugins: ['stylelint-less'],
  extends: ['stylelint-config-standard', 'stylelint-config-prettier'],
  syntax: 'less',
  customSyntax: 'postcss-less',
  rules: {},
  ignoreFiles: ['**./*.js,', '**./*.ts,', '**./*.tsx,', '**./*.jsx,', '**./*.vue,']
};
```

之后添加命令，如果你的结构和教程的不一样，你需要自己修改检查样式的路径 ：

```json
"scripts": {
    "lint:css": "stylelint packages/components/src/**/*.less --fix"
},
```

## 配置husky

husky 作用是在我们使用 `git提交`的过程前后，进行一系列的 `hook操作`，我们也可以理解其就是一个`git`的生命周期，我们可以在这个过程中做一些事情，然后我们就可以让用户提交的时候检测一次上面的文件命名是否有问题，如果有就阻止用户提交代码，除此之外我们也就可以对代码进行校验了，上面的`eslint`，`prettier`，`stylelint`这些工具我们都可以让其全部检测通过才允许用户提交代码，这样一来，我们就可以保证提交到仓库的代码是有一定可靠性的

我们还是首先下载依赖：

```shell
pnpm i husky -w -D
```

之后我们分别执行如下的命令：

```shell
npm pkg set scripts.prepare="husky install"
npm run prepare
npx husky add .husky/pre-commit "npm test"
```

我们可以看到出现了一个 .husky 文件夹，我们进入其中，找到 pre-commit 文件，这个文件就是我们可以在 git 提交之前可以运行脚本编写的位置，我们在其中编写一些我们刚刚编写的命令，比如执行 eslint 的修复：

```shell
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

pnpm run lint:fix
npm test
```

之后我们可以在一些文件里加入一些违反我们要求的内容，比如加入一个 console 指令，然后我们提交到我们的 git 试试，如果系统阻止了你的提交，说明配置成功了

## 配置lint-staged

 [lint-staged ](https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Flint-staged) 是一个命令行工具，它能够对 git 的 staged（暂存区）中的文件使用 linter 工具格式化，修复一些风格问题，并再次添加到 staged 上。他可以帮我们只检测本次变更的文件，不会影响之前的文件，这样一来需要检测文件数量大大变少，还是先安装依赖

```shell
pnpm i lint-staged -D
```

在`scripts`当中去添加这样一个脚本用于调用它：

```json
"lint:staged": "lint-staged",
```

之后编写一个`.lintstagedrc.js `文件用于配置我们需要的参数，这个文件告诉了系统，说明类型的文件需要使用什么命令进行检查

```json
module.exports = {
  '*.{js,jsx,ts,tsx}': ['eslint --fix', 'prettier --write'],
  '{!(package)*.json,*.code-snippets,.!(browserslist)*rc}': ['prettier --write--parser json'],
  '*.vue': ['prettier --write', 'eslint --fix'],
  'packages/components/src/**/*.less': ['stylelint --fix', 'prettier --write'],
  '*.md': ['prettier --write']
};
```

添加一些更新添加我们的脚本运行，如果没有报错说明你的配置成功了，在配置成功之后，我们只需要在 husky 的配置中将我们的检查命令换成一条统一的  lint:staged 命令就可以了

```shell
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run lint:staged
npm test
```

## 配置commitlint

清晰命令的`commit`信息可以帮助我们更好的溯源，也可以让我们更加清晰的了解每次提交都变更了什么内容，一般在开源的项目当中基本都会使用到，这样也方便多成员对项目更快的理解。所以我们使用 commitlint 这个包来进行我们的限制，还是先下载依赖：

```shell
pnpm i @commitlint/cli -D
pnpm i @commitlint/config-conventional -D
pnpm i cz-conventional-changelog -D
```

下载完之后我们去完善我们的配置文件 commitlint.config.js，还是老样子，想要个性化定制的可以查看 *https://commitlint.js.org/#/reference-rules*

```js
module.exports = {
  ignores: [commit => commit.includes('init')],
  extends: ['@commitlint/config-conventional'],
  rules: {
    'body-leading-blank': [2, 'always'],
    'footer-leading-blank': [1, 'always'],
    'header-max-length': [2, 'always', 108],
    'subject-empty': [2, 'never'],
    'type-empty': [2, 'never'],
    'subject-case': [0]
  }
};
```

在有了配置文件之后我们将我们的命令添加到 package.json，要注意这次需要多添加一项  "config" 的内容：

```json
  "scripts": {
    "commit": "git-cz"
  },
  "config": {
    "commitizen": {
      "path": "cz-conventional-changelog"
    }
  },
```

最后我们将提交的限制放在 husky 里：我们在 pre-commit 的同级创建一个文件，叫 commit-msg，然后将脚本写入其中：

```shell
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx commitlint --edit $1
```

此时你可以随便提交一些内容，然后随意填写提交信息，发现是提交失败的说明你配置成功了，因为你的提交信息收到了约束，现在当你要提交内容的时候，你需要在命令行写入以下内容：

```shell
pnpm run commit
```

运行这个脚本之后，你可以按照要求的格式创建一系列的提交信息，这样就可以保证协作的时候，大家的提交信息都是规范可查的。

至此我们已经配置了我们需要的规范化代码、样式、代码风格、文档格式、提交检查、提交信息等一系列的内容，你可以将你的组件库发布到 git 上让大家参与你的组件库维护，让他成为一个良好的开源项目，以下是我的链接：

https://github.com/aiai0603/ls-ui

