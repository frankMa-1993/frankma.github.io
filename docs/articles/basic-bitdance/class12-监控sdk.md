# class12 - 前端监控 SDK 实战

这节实战课，学习了如下的内容：

1. 引入前端监控的概念
2. 介绍前端监控的性能指标与异常
3. 实战：封装用于监听性能指标与前端异常的监听器
4. 实战：封装一个有完整上报能力的 sdk

## 什么是前端监控

经典面试题目：从输入 URL 到我们看到画面经历了什么

![image-20230205201122628](/bit-dance/img/44.png)

简单来说，共有以下几个过程：

- DNS解析
- 发起TCP连接
- 发送HTTP请求
- 服务器处理请求并返回HTTP报文
- 浏览器解析渲染页面
- 连接结束。

前端监控就是尽可能采集这一过程以及后续用户交互产出的性能指标与发生的异常事件并且上报到平台完成消费。

## 为什么要进行前端监控

用户在使用过程中可能遇到的体验问题：白屏时间长、交互卡顿、资源加载失败等等

通过前端监控，开发者可以对页面数据进行采集和上报，帮助开发者快速对质量差的页面进行分析和归因

对质量差的页面进行分析和质量的优化可以有效提升用户体验，从而帮助开发者提升用户体验

## 前端监控的内容

![image-20230205205001766](/bit-dance/img/45.png)

## 常用的性能指标

早期网页是纯静态的，但随着Web 爆发式发展，页面交互越来越复杂。开发者开始思考如何提高Web 性能、改善用户体验。
因此，2010年8月, W3C成立了 Web 性能工作组，由来自Google和 Microsoft的工程师担任主席，目标是制定衡量Web应用性能的方法和API.随后，Web 性能工作组开始制定一系列 Web性能标准，应用到桌面和移动浏览器以及其他环境中,帮助Web开发人员评估和理解应用的性能特征。

这是标准浏览器正在使用的评估模型，这些数据可以通过 window.performance.timing 查看，返回每个阶段的用时，这是技术人员常用的手段：

![image-20230205205225078](/bit-dance/img/46.png)

但是只有这些角度还不够，用户并不关系这些细节，也并不理解这些指标的作用，所以我们需要从用户的角度出发考虑进行评估，因此我们有了一下指标：

FP (First Paint):首次渲染的时间点。FP时间点之前，用户看到的都是没有任何内容的白色屏幕。

FCP (First Contentful Paint):首次有内容渲染的时间点

FMP (First Meaningful Paint):首次绘制有意义内容的时间点

TTI (Time to Interactive):测量页面从开始加载到主要子资源完成渲染,并能够快速、可靠地响应用户输入所需的时间。TTI反映页面可用性的重要指标。TTI值越小，代表用户可以更早地操作页面，用户体验就更好。

Sl(Speed Index):衡量页面可视区域加载速度，帮助检测页面的加载体验差异

FID (First Input Delay):测量从用户第一次与页面交互(比如当他们单击链接、点
按按钮等等)直到浏览器对交互作出响应，实际能够开始处理事件时处理程序所经过的时间

![image-20230205205821591](/bit-dance/img/47.png)

LCP (Largest Contentful Paint)：最大的内容在可视区域内变得可见的时间点。最大的元素，例如一篇文章中的一大段文字或产品页面上的一张图片，大概就是让你理解页面内容的最有用的元素。

LCP 最大的优势是容易理解；和FMP类似；并且容易计算和上报

![image-20230205210353824](/bit-dance/img/48.png)

TBT (Total Blocking Time):量化主线程在空闲之前的繁忙程度，有助于理解在加载期间，页面无法响应用户输入的时间有多久。

如果一个任务在主线程上运行超过50毫秒，那么它就是长任务。超过50ms后的任务耗时,都算作任务的阻塞时间。一个页面的TBT，是从 FCP 到TTI之间所有长任务的阻塞时间的总和。

![image-20230205210552992](/bit-dance/img/49.png)

CLS (Cumulative Layout Shift):量化了在页面加载期间,视口中元素的移动程度。例如：当我们点击按钮时,突然出现了一块内容。无论是以一种意外几率方式加载广告，还是在加载图片时文本向下移动，内容的意外移动都会让人非常不舒服。

## 前端常见异常

1. 静态资源错误：在拉取和加载静态资源的过程中发生了预期之外的错误，如网络异常等，导致静态资源无法正常渲染到页面上。

2. 请求异常：Http请求状态码分多类
   100 -199----------------> 信息响应

   200 - 299 ----------------> 成功响应

   300 - 399 ----------------> 重定向消息

   400 - 499----------------> 客户端错误响应

   500 - 599------------> 服务端错误响应

   请求响应状态码>= 400 就是请求异常。对于通过异步请求拉取的静态资源错误也可选择归纳到请求异常

3. JS异常：在页面运行时发生的Js错误会严重影响页面的正常渲染与交互是前端监控的重点。通常打开 f12 调试工具可以看到他们以红色 error 的形式展现

4. 白屏异常：前面几类异常都可以通过浏览器提供的标准化方法来监听到，而白屏异常没有标准化的监听方法,所以更考验前端监控开发者的功底。通常我们可以通过判断 DOM树的结构来粗略的判断白屏是否发生。通常导致白屏发生的原因可能有如下几点:1.发生Js错误导致关键资源渲染失败。⒉请求异常或静态资源加载失败。3.长时间的Js线程繁忙阻塞渲染任务。

## 性能监控

我们可以根据 `window.performance` 和 `PerformanceObserver` 来拿到我们需要的时间数据，之后根据我们获得的时间来计算出我们需要的指标，从而实现性能的监控：

```typescript
//通过 window.performance 对象拿到 fp fcp 和 fip。
window.performance.getEntriesByType('paint');
window.performance.getEntriesByType('first-input');
```

PerformanceObserver 相较于前者拥有更多的内容可以获取，具体可以查阅相关文档，在实战的时候，我们先尝试 通过 window.performance 对象获取我们需要的数据，如果不能满足，再使用 PerformanceObserver

```typescript
/**
 * 列举出性能指标对应的 entry type
 * fp,fcp --> paint
 * lcp --> largest-contentful-paint
 * fip --> first-input
 */
const entryTypes = ['paint', 'largest-contentful-paint', 'first-input']

// 通过 PerformanceObserver 监听
const p = new PerformanceObserver(list => {
  for (const entry of list.getEntries()) {
    console.log(entry);
  }
})
p.observe({ entryTypes });
```

我们可以将我们的方法封装成一个monitor（监听器），我们封装之后，才能将它整合到我们的 sdk 中

```typescript
// 封装成一个 monitor
function createPerfMonitor(report: ({ name: string, data: any }) => void) {
  const name = 'performance';
  const entryTypes = ['paint', 'largest-contentful-paint', 'first-input']
  function start() {
    const p = new PerformanceObserver(list => {
      for (const entry of list.getEntries()) {
        report({ name, data: entry });
      }
    })
    p.observe({ entryTypes });
  }
  return { name, start }
}
```

## JS 错误监控

因为我们的异步请求现在多采用了 promise 封装，所以我们可以使用 window.addEventListener 的 errer 和 unhandledrejection 监听我们 js 错误和请求的错误，具体可以查看官方文档：

https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener

```typescript
// 监听 js 执行报错
window.addEventListener("error", (e) => {
  // 只有 error 属性不为空的 ErrorEvent 才是一个合法的 js 错误
  if (e.error) {
    console.log('caputure an error', e.error);
  }
});
// throw(new Error('test'));
// 监听 promise rejection
window.addEventListener("unhandledrejection", (e) => {
  console.log('capture a unrejection', e);
});
Promise.reject('test');
```

同样我们可以把它们封装成一个monitor（监听器）

```typescript
// 封装成一个 monitor
function createJsErrorMonitor(report: ({ name: string, data: any }) => void) {
  const name = "js-error";
  function start() {
    window.addEventListener("error", (e) => {
      // 只有 error 属性不为空的 ErrorEvent 才是一个合法的 js 错误
      if (e.error) {
        report({ name, data: { type: e.type, message: e.message } });
      }
    });
    window.addEventListener("unhandledrejection", (e) => {
      report({ name, data: { type: e.type, reason: e.reason } });
    });
  }
  return { name, start }
}
```

## 静态资源的错误

静态资源我们也是通过 error 的方式来处理，我们需要注意：

1. 为了兼容性（兼容老版本），我们需要对 target 和 srcElement 两个属性进行获取，即使 srcElement 已经过时了。
2. 通过对 target 是不是 html 元素的类型判定，我们可以判定它是不是一个静态资源：
3. 静态资源错误，在捕获阶段才能监听到，所以我们需要把 addEventListener 的第二个参数设为 true，具体可以查阅文档

```typescript
// 监控静态资源错误，注意需要在捕获阶段才能监听到
// 2. 封装成一个 monitor
function createResourceErrorMonitor(report: ({ name: string, data: any }) => void) {
  const name = "resource-error";
  function start() {
    window.addEventListener('error', e => {
      // 注意区分 js error
      const target = e.target || e.srcElement;
      if (!target) {
        return
      }
      if (target instanceof HTMLElement) {
        let url;
        // 区分 link 标签，获取静态资源地址
        if (target.tagName.toLowerCase() === 'link') {
          url = target.getAttribute('href');
        } else {
          url = target.getAttribute('src');
        }
        report({ name, data: { url } });
      }
    }, true)
  }
  return { name, start }
}
```

## 请求错误

我们通过hook xhr和fetch 对象来监听请求时发生的错误。

- hook（钩子）是一种特殊的消息处理机制，它可以监视系统或者进程中的各种事件消息，截获发往目标窗口的消息并进行处理。

- XMLHttpRequest (简称*xhr*) 是浏览器提供的 JavaScript对象，通过他它可以请求服务器上的数据资源 ，我们常说的 ajax 就是基于它的封装

对于它的监听，我们首先需要编写一个 hook ，传入一个对象，将我们编写的逻辑注入到这个对象中，这里使用了高阶函数的技巧，在开发中是十分有效的，关于高阶函数以及为什么使用它，可以在之前的 js 篇章中（本专栏第三节）学习

```typescript
function hookMethod(
  obj: any,
  key: string,
  hookFunc: Function,
) {
  return (...params: any[]) => {
    obj[key] = hookFunc(obj[key], ...params)
  }
}
```

之后我们通过我们编写的 hooks 对我们 xhr 对象的 open 和 send 进行获取，并且在原来的对象中执行我们的逻辑，其中为了将我们需要的信息存储下来，我们可以用 this.xxx = 我们的值 这样的方式，借用原来对象的存储空间来存储我们需要的数据

```typescript
// hook xhr 对象的 open 方法拿到请求地址和方法
hookMethod(XMLHttpRequest.prototype, 'open', (origin: Function) =>
  function (method: string, url: string) {
    this.payload = {
      method,
      url,
    };
    // 执行原函数
    origin.apply(this, [method, url]);
  }
)();

// hook xhr 对象的 send 方法监听到错误的请求
hookMethod(XMLHttpRequest.prototype, 'send', (origin: Function) =>
  function (...params: any[]) {
    this.addEventListener("readystatechange", function () {
      if (this.readyState === 4 && this.status >= 400) {
        this.payload.status = this.status;
        console.log(this.payload);
      }
    });
    origin.apply(this, params);
  }
)();
//封装
function createXhrMonitor(report: ({ name: string, data: any }) => void) {
  const name = "xhr-error";
  function hookMethod(
    obj: any,
    key: string,
    hookFunc: Function,
  ) {
    return (...params: any[]) => {
      obj[key] = hookFunc(obj[key], ...params)
    }
  }
  function start() {
    hookMethod(XMLHttpRequest.prototype, 'open', (origin: Function) =>
      function (this, method: string, url: string) {
        this.payload = {
          method,
          url,
        };
        origin.apply(this, [method, url]);
      }
    )();
    hookMethod(XMLHttpRequest.prototype, 'send', (origin: Function) =>
      function (this, ...params: any[]) {
        this.addEventListener("readystatechange", function () {
          if (this.readyState === 4 && this.status >= 400) {
            this.payload.status = this.status;
            report({ name, data: this.payload });
          }
        });
        origin.apply(this, ...params);
      }
    )();
  }
  return { name, start }
}
```

## SDK 的实现

前端监控需要做的是  数据采集，组装上报，清洗存储，数据消费 ，sdk主要完成前两步，而后面两部需要借助后端平台进行处理。

我们刚刚编写的大量函数已经完成了数据的采集，所以我们的 sdk 还需要一个函数将我们采集到的数据上报到平台。可以使用 Navigator.sendBeacon() 这个函数，它通过 HTTP POST 将少量数据异步传输到 Web 服务器。它主要用于将统计数据发送到 Web 服务器，同时避免了用传统技术（如：XMLHttpRequest）发送分析数据的一些问题。

更多的信息，可以查看相关文档：https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/sendBeacon

```typescript
navigator.sendBeacon(url, JSON.stringify({ name: string, data: any }));
```

现在我们已经有一个数据采集和上传的函数，最后我们需要将我们的各项功能组装到一起，具体说明注释

```typescript
//提供一个地址，来使得我们的sdk拥有上报的后台
function createSdk(url: string) {
  //存储我们拥有的 monitor
  const monitors: Array<{ name: string, start: Function }> = [];
  // 存储sdk自身的内容
  const sdk = {
    url,
    report,
    loadMonitor,
    monitors,
    start,
  }
  //上报函数
  function report({ name: string, data: any }) {
    // 注意：数据发送前需要先序列化为字符串
    navigator.sendBeacon(url, JSON.stringify({ name: string, data: any }));
  }
  //导入monitor的函数
  function loadMonitor({ name: string, start: Function }) {
    monitors.push({ name: string, start: Function });
    // 实现链式调用
    return sdk;
  }
  //启动 sdk，启动我们所有的 monitor
  function start() {
    monitors.forEach(m => m.start());
  }
  return sdk;
}
const sdk = createSdk("111.com");
const jsMonitor = createJsErrorMonitor(sdk.report);
//链式调用 ，装入我们的 sdk
sdk.loadMonitor(jsMonitor).loadMonitor(createPerfMonitor(sdk.report));
//启动
sdk.start();
throw (new Error('test'));

//随意导入两个 monitor 来测试
function createJsErrorMonitor(report: ({ name: string, data: any }) => void) {
  const name = "js-error";
  function start() {
    window.addEventListener("error", (e) => {
      // 只有 error 属性不为空的 ErrorEvent 才是一个合法的 js 错误
      if (e.error) {
        report({ name, data: { type: e.type, message: e.message } });
      }
    });
    window.addEventListener("unhandledrejection", (e) => {
      report({ name, data: { type: e.type, reason: e.reason } });
    });
  }
  return { name, start }
}

function createPerfMonitor(report: ({ name: string, data: any }) => void) {
  const name = 'performance';
  const entryTypes = ['paint', 'largest-contentful-paint', 'first-input']
  function start() {
    const p = new PerformanceObserver(list => {
      for (const entry of list.getEntries()) {
        report({ name, data: entry });
      }
    })
    p.observe({ entryTypes });
  }
  return { name, start }
}
```



## 让我们的 SDK 更加健壮

1. 加入更多的指标

2. 获取请求各阶段的耗时

3. 我们的 hook 没有 unhook 的能力，对对象的破坏是不可逆的，可以尝试实现一个更加安全的 hook

   
