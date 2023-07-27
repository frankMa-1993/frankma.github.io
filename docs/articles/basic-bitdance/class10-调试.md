# class10 - 前端调试



在程序员的世界中，BUG 一词相信同学们再熟悉不过了，本节课将围绕前端开发中所遇见的 BUG 出发，讲解作为一名合格的前端开发人员，你应该掌握哪些开发调试知识

# Chorme DevTools

Chorme DevTools 是 chorme内核为大家提供的高效的前端调试工具，我们在 chorme浏览器按 F12 或者点击右键 -> 检查 可以打开它，它分为多个板块：

## Elements

它代表了一个网页的元素和样式，可以通过选中一个元素来获得它的样式信息：

1. 点击 .cls 标签可以动态修改元素的 class
2. 在显示的 class 里可以动态修改元素的样式，或者新增新的属性
3. 可以右键一个元素，使用 Force state 来使得元素处于一个状态，如鼠标悬停或者链接激活等等，也可以点击右侧 style下面的 :hov 来选择对应的伪类
4. 可以通过 computed 来搜索所有的属性进行调试

![img](/bit-dance/img/23.png)

## Console

这个界面可以打印出前端的日志，我们使用 console.log、console.warn、console.error、console.debug、console.info 来表示不同等级的日志，可以通过筛选来筛选不一样等级的日志：

![img](/bit-dance/img/24.png)

console.table 可以具象化展示JSON和数组数据

![image-20230123130434275](/bit-dance/img/25.png)

console.dir 则通过类似文件树的方式展示对象的属性

![image-20230123130654837](/bit-dance/img/26.png)

## Score Tab

![image-20230123131733057](/bit-dance/img/27.png)

我们可以通过两种方法添加断点，在代码中添加 `debugger;`  或者在source界面对应代码的行号面前双击，也可以直接添加断点，断点列表可以在BreakPoints 中查看，同时我们还可以查看属性的作用域和调用栈

![image-20230123134150500](/bit-dance/img/28.png)

但是工程代码一般是通过压缩混淆后的，代码已经难阅读了，我们需要用到 souce map这个工具，他可以在出错的时候定位到原始代码的位置，但是带上source map 不安全，还是可以通过它获取原始代码，所以一般打包带上 source map ，发布版本是删除了source map 的，而带有 source map  的版本一般上传监控服务器等位置。

## NetWork

这个界面包含了浏览器的请求的各种资源

![image-20230123135700113](/bit-dance/img/29.png)

可以在这个页面控制面板处模拟不同的网络环境，比如弱网环境来进行调试，也可以在写请求详细页面获得接口的传入值和返回值进行接口的调试

![1](/bit-dance/img/30.png)

## Application

这个信息包含了相关的本地存储信息，比如cookie localstroge等等

## Performance

这个页面包含了性能相关的信息

![image-20230123141131499](/bit-dance/img/31.png)

## Lighthouse

分析网站的核心指标

![image-20230123141954134](/bit-dance/img/32.png)

# 移动端调试

## 真机调试

用USE数据线连接，打开开发者模式，输入浏览器地址

## Vconsole

使用 Vconsole 插件可以在真机app中导入 chorme 的DevTools

![image-20230123142921580](/bit-dance/img/33.png)

## 代理工具

使用电脑作为代理服务器，手机通过 HTTP 代理连接到电脑，手机上的请求都经过代理服务器。常用的 charles

# Node调试

执行如下命令

```javascript
node --inspect=8888 index.js
```

打开浏览器 chrome://inspect/#devices，添加上我们调试服务 localhost:8888 就可以进行node的调试

![image-20230123144528247](/bit-dance/img/34.png)

# 开发调试技巧

1. 使用 source -> overrides 修改代码可以直接修改内容保存到本地

2. 代理服务器解决跨域问题，例如 devserver 的服务器代理，或者 vue.config.js 的本地代理等等，这里给一篇教程 https://blog.csdn.net/weixin_48508063/article/details/124192979?spm=1001.2014.3001.5502

3. 本地 source map，使用代理工具使用 map local把远程的代理到本地来访问本地的 source map

4. Mock 数据，利用代理工具让本地数据变成远程的，这里还是放一篇文档连接：

   https://blog.csdn.net/m0_62570630/article/details/123337769
