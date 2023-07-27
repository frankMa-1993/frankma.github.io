# class13 - Webpack

本节课将重点围绕「 Webpack 」这一核心话题展开。简述前端工程化的常用工具webpack 的原理和使用

## webpack的作用

webpack的作用是把很多文件打包整合到一起, 缩小项目体积, 提高加载速度，常用的场景是：

- 代码压缩

将JS、CSS代码混淆压缩，让代码体积更小，加载更快

- 编译语法

编写CSS时使用Less、Sass，编写 JS 时使用ES6、TypeScript等，这些标准目前都无法被浏览器兼容，因此需要构建工具编译，例如使用Babel编译ES6语法。

- 处理模块化：

CSS 和 JS 的模块化语法，目前都无法被浏览器兼容。因此开发环境可以使用既定的模块化语法，但是需要构建工具将模块化语法编译为浏览器可识别形式。

## 开始使用 webpack

首先需要使用 node 安装 webpack

```shell
npm install webpack
npm install webpack-cli
```

我们可以通过 webpack 命令将一个文件进行打包，webpack 会根据模块的依赖关系进行静态分析，相互依赖的各个模块都会被包含到 bundle.js 文件中。Webpack 会给每个模块分配一个唯一的 id 并通过这个 id 索引和访问模块。

```shell
npx webpack a.js bundle.js
```

之后创建一个 webpack 的配置文件 webpack.config.js ，在其中我们可以进行打包的配置，webpack 命令执行后，会默认载入当前目录的 webpack.config.js 文件。以下是常用的配置说明：

```javascript
// webpack 中引入其他的模块要使用commonJS模块化require导入，因为webpack是nodeJS 开发的，不能使用 import导入
var webpack = require('webpack');
const { resolve } = require('path');
module.exports = {
    //entry可以是个字符串或数组或者是对象。当entry是个字符串的时候，用来定义入口文件
    entry: './js/app.js',
    //output参数是个对象，用于定义构建后的文件的输出。其中包含path和filename
    output: {
        path: './build',
        filename: 'bundle.js'
    },
    //基础目录，绝对路径，用于从配置中解析入口点(entry point)和 加载器(loader)。默认使用当前目录，但是推荐在配置中传入一个值
    context:resolve(__dirname, './'),
    //关于模块的加载相关，我们就定义在module.loaders中。这里通过正则表达式去匹配不同后缀的文件名，然后给它们定义不同的加载器
  	module: {
       {
        // 正则: 匹配所有以css结尾的文件
        test: /\.css$/,
        // 使用的加载器，加载器处理顺序：从右往左
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: '../',
            },
          }, 
          'css-loader'
        ]
      },
      {
        test: /\.less$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: '../',
            },
          }, 
          'css-loader', 
          'less-loader'
        ]
      },
      {
        test: /\.(png|jpg|gif)$/i,
        use: [
          {
            loader: 'url-loader',
            //配置项目
            options: {
              limit: 8 * 1024,
              // 配置输出的文件名
              name: '[name].[ext]',
              // 配置静态资源的引用路径
              publicPath: "../images/",
              // 配置输出的文件目录
              outputPath: "images/"
            }
          }
        ]
      },
      // (4) 配置对于高版本js的兼容性处理
      {
        test: /\.js$/,
        // 配置排除项
        exclude: /(node_modules)/,
        use: [
        {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      ]
 	},
    //Webpack社区提供了一个便捷的本地开发工具webpack-dev-server,在webpack.config.js文件添加devServer对象，是专门用于存放webpack-dev-server配置的,当webpack-dev-server发现工程源文件进行了更新操作就会自动刷新浏览器，展示更新后的内容
    devServer: {
    	hot: true,
    	// 自动打开浏览器
    	open: true
    }
    //webpack在构建包的时候会按目录的进行文件的查找，resolve属性中的extensions数组中用于配置程序可以自行补全哪些文件后缀，如下的配置我们想要加载一个js文件时，只要require(‘common’)就可以加载common.js文件了。
    resolve:{
        extensions:['','.js','.json']
    },
    //插件，用于完成一些 loader 不能完成的工作
    plugins: [
        new webpack.NoErrorsPlugin()
    ]，
    mode: 'development', //生产环境
     // mode:'production,'//开发环境
};
```

## webpack的运行流程

webpack是运行流程如下：

1. 入口处理：从编写的 entry 开始，启动我们的编译流程
2. 依赖解析：根据文件的 require 和 import 的情况，找到文件依赖的资源
3. 资源解析：根据module的配置，将资源用不同的解析器进行处理，转成标准的内容
4. 资源合并：将转译后的内容合并打包并且在我们 output 指定的文件中输出

## 使用webpack的详解

webpack的使用就是书写配置文件，使用我们要详细的去学如何去配置我们的 webpack.config.js 文件，他有多种的属性：

![image-20230123163940367](/bit-dance/img/35.png)

- entry 和 output

entry  用于定于一次打包的入口文件，也就是从哪个文件开始打包

output 参数是个对象，用于定义构建后的文件的输出。其中包含path和filename，代表了输出路径和输出文件名称

```javascript
	entry: './js/app.js',
    output: {
        path: './build',
        filename: 'bundle.js'
    },
```

- module

webpack只能匹配优先类型的前端文件，比如 .css 。如果我们需要引入其他类型的文件，比如 .less 文件，我们需要通过加载器来处理它们，module就是做这个事情的，它通过rules定义一个对象数组，每一项通过 test 定义正则表达式去匹配不同后缀的文件名，然后通过 use 给它们定义不同的加载器，部分加载器可能需要你配置参数，你可以在 options 字段进行配置，exclude 则可以排除指定目录下的文件，使得他们不使用加载器进行加载，以下的例子是几种常见的配置，css 的解析，less 的解析，图片的解析，babel的配置：

```javascript
  	module: {
       {
        // 正则: 匹配所有以css结尾的文件
        test: /\.css$/,
        // 使用的加载器，加载器处理顺序：从右往左
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: '../',
            },
          }, 
          'css-loader'
        ]
      },
      {
        test: /\.less$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: '../',
            },
          }, 
          'css-loader', 
          'less-loader'
        ]
      },
      {
        test: /\.(png|jpg|gif)$/i,
        use: [
          {
            loader: 'url-loader',
            //配置项目
            options: {
              limit: 8 * 1024,
              // 配置输出的文件名
              name: '[name].[ext]',
              // 配置静态资源的引用路径
              publicPath: "../images/",
              // 配置输出的文件目录
              outputPath: "images/"
            }
          }
        ]
      },
      //  配置对于高版本js的兼容性处理
      {
        test: /\.js$/,
        // 配置排除项
        exclude: /(node_modules)/,
        use: [
        {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      ]
 	},
```

- plugins

有一些 loader 不能完成的工作，我们需要导入一些插件来完成这些工作，比如自动生成一个html 文件，需要一个插件，我们这样导入

```javascript
//在顶部引入
const HTMLWebpackPlugin = require('html-webpack-plugin')
//....省略代码
module.exports = {
    //....
	plugins: [
        new HTMLWebpackPlugin()
 	]，
}
```

- devServer

devServer 是一个本地开发服务器，会自动监听变化，自动打包构建，自动更新刷新浏览器，你只需要下载相关的库，并且配置devServer 项就可以使用它，其中的 hot 是否开启热更新，就是就是你修改代码保存之后，浏览器不会刷新，只会修改你更改过的依赖代码。

```javascript
    devServer: {
        //热更新
    	hot: true,
    	// 自动打开浏览器
    	open: true
    }
```

当然当你配置了热更新之后，你需要使用不一样的命令来执行打包

```shell
npx webpack server
```

- watch

这个属性说明了是否开启 webpack 的监听功能，设置为 true 后，每次按保存键，webpack自动为我们打包，我们可以通过  watchOptions 来进行 watch 的配置

```javascript
    watch:true
	watchOptions:{
    	poll:1000,//监测修改的时间(ms)
    	aggregeateTimeout:500, //防止重复按键，500毫米内算按键一次
    	ignored:/node_modules/,//不监测
	}
```

- devtool

配置webpack的调试工具，一共有20多种可以选择，具体可以查看官网的文档，https://webpack.js.org/

合理的配置可以有效的实现Source Map 

```javascript
    devtool:"eval-source-map"
```

- mode

提供 mode 配置选项，告知 webpack 使用相应模式的内置优化。

1. development，会将 process.env.NODE_ENV 的值设为 development。启用 NamedChunksPlugin 和 NamedModulesPlugin。
2. 会将 process.env.NODE_ENV 的值设为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin

区别简单的来说就是：production 会移除一些没有依赖的方法、变量和文件，并且代码会进行压缩，比development的文件小。

- optimization

optimization主要用来自定义一些优化打包策略，主要有这些常用的值：

splitChunks：设置这个值会自动提取所有公共模块到单独bundle中去

minimize：表示是不是压缩模块

minimizer：属性存放一个数组，数组里可以存放用于代码压缩的插件

usedExports： 表示是不是只导出那些外部使用了的那些成员

concatenateModules：表示是不是合并模块，因为打包默认会把很多模块放进一个单独的函数中，如果模块很多的话，那么输出文件就会有很多这种模块函数

sideEffect：这个新特性可以让我们来标识我们的代码有没有副作用。但是有些模块的导入虽然没有使用，但是外部可能会继承自他们，或者有有些简介的联系，sideEffects 就可以标记这些模块，告诉javascript哪些没有副作用，可以去掉，哪些有副作用不可以去掉

```javascript
optimization: {
    splitChunks: {
      // 自动提取所有公共模块到单独 bundle
      chunks: 'all'
    },
    // 表示只导出那些外部使用了的那些成员
    usedExports: true,
    // 压缩模块
    minimize:true,
    minimizer:[
   		 new TerserPlugin(),
   		 new OptimizeCssAssetsWebpackPlugin()
    ],
    concatenateModules:true,
    sideEffect: true
},
```

## 理解 Loader

loader 主要的作用的内容转换，因为 webpack 只认识 js 代码，loader要做的就是把非 js 的资源转换成 js 的资源，css 是链式调用的，比如 less 的解析需要用到如下三个 loader，他们是按照顺序分别执行的：

![image-20230123214239485](/bit-dance/img/36.png)



loader 的执行分为 pitch 和 normol 两个阶段，前者是从数组第一个向后执行，而后者是从最后一个向前执行，loader 会先执行 pitch，然后获取资源再执行 normal loader，但是如果 pitch 有返回值时，就不会走之后的 loader，并将返回值返回给之前的 loader。

因此，数组的第一个 loader 要返回 js 脚本，也就是最后要输出给webpack的内容；每个 loader 只做一件事，为了使 loader 在更多的场景中链式调用；每一个 loader 都是一个模块；每个 loader 都是无状态的，确保 loader 在不同的模块转换之间保存状态。
如果想要学会自己写一个 loader 可以查看 https://mp.weixin.qq.com/s/TPWcB4MfVrTgFtVxsShNFA

以下是常见的 loader:

![image-20230123215313387](/bit-dance/img/37.png)

## 理解插件

插件架构可以提升工具的拓展性：webpack的原理和底层是非常复杂的，新人需要了解整个流程成本很高，迭代成本很高，牵一发动全身，并且作为开源项目缺乏成长性，参与它很难。插件的精髓是，对外开放拓展，对修改封闭，我只实现核心的功能，其他的功能都由插件生态来共建和实现

webpack的实现原理是：Webpack 编译阶段会为各个编译对象初始化不同的 Hook ，开发者可以在自己编写的 Plugin 中监听到这些 Hook ，在打包的某个特定时间段触发对应 Hook 注入特定的逻辑从而实现自己的行为。

webpack上有两个很重要的对象：

1. compiler 对象中保存着完整的 Webpack 环境配置，它通过 CLI 或 者 Node API传递的所有选项创建出一个 compilation 实例。这个对象会在首次启动 Webpack 时创建，我们可以通过 compiler 对象上访问到 Webapck 的主环境配置，比如 loader 、 plugin 等等配置信息
2. compilation 对象代表一次资源的构建，compilation 实例能够访问所有的模块和它们的依赖，在 compilation 对象中我们可以获取/操作本次编译当前模块资源、编译生成资源、变化的文件以及被跟踪的状态信息，同样 compilation 也基于 tapable 拓展了不同时机的 Hook 回调

Plugin 的原型对象上需要存在一个 apply 方法，当 webpack 创建 compiler 对象时会调用各个插件实例上的 apply 方法并且传入 compiler 对象作为参数。同时需要指定一个绑定在 compiler 对象上的 Hook ， 在 Hook 的回调中处理插件自身的逻辑，根据 Hook 的种类，在完成逻辑后通知 webpack 继续进行

Webpack Plugin 的核心机制就是基于 tapable 产生的发布订阅者模式，在不同的周期触发不同的 Hook 从而影响最终的打包结果。

想要详细了解的自行查阅一些资料，比如:

https://blog.csdn.net/gogo2027/article/details/127750064?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-127750064-blog-125101031.pc_relevant_3mothn_strategy_recovery&spm=1001.2101.3001.4242.1&utm_relevant_index=3
