# class5 - Http

本节课介绍 Http 协议的基本定义和特点，在此基础上，对于 Http 协议的发展历程及报文结构展开进一步分析。

## 从输入字符串到打开网页

1. 输入地址
2. 浏览器处理输入信息
3. 浏览器发请求
4. 到达服务器
5. 服务器返回信息
6. 浏览器读取响应信息
7. 浏览器渲染
8. 页面加载完成

## 什么是HTTP

HTTP就是超文本传输协议（Hyper Text Transfer Protocol，HTTP）是一个简单的请求-响应协议，它通常运行在TCP之上。它指定了客户端可能发送给服务器什么样的消息以及得到什么样的响应。

1. 超文本传输协议
2. 应用层协议 基于 TCP协议
3. 有请求和响应两个部分
4. 简单可拓展
5. 无状态

## HTTP的发展

1.  0.9 ： 单行协议 响应只有 html 文档
2.  1.0 ： 增加了头部，有了状态码，支持多种文档类型
3.  1.1 ：链接复用，缓存，内容协商
4.   2 ：二进制协议，压缩头部，服务器头部

## 请求方法

![img](/bit-dance/img/19.png)

请求分为 Safe(安全的)：不会修改服务器数据的方法：GET、HEAD、OPTIONS

ldempotent(幂等)：同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。GET、HEAD、OPTIONS、PUT、DELETE

## 状态码

在收到请求后，服务器会响应，响应中具有不同状态，不同的状态码代表了不同的含义

- 1xx：指示信息，表示请求收到，继续处理
- 2xx：成功
- 3xx：重定向，要完全请求必须进行进一步操作
- 4xx：客户端错误，有语法错误等
- 5xx：服务器错误，不能实现

常见的状态码有：

- 200 OK 请求成功
- 301 资源(网页)被永久转到其他URL
- 302 临时跳转
- 404 找不到
- 403 被静止
- 401 未授权
- 500 内部错误
- 504 网关或服务器无法在规定的时间内获得想要的响应

## RESTful API

RESTful API一种API设计风格; REST - Representational State Transfer

- 每一个URI代表一种资源
- 客户端和服务器之间，传递这种资源的表现层
- 客户端通过HTTP method，对服务器端资源进行操作，实现“表现层状态转化”

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/82a99afac8ae4136b567ea5a298e8952~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

## 报文分析

http的请求报文由：请求行、头部、空行、主体 (请求数据) 四个部分组成。其中请求行由请求方法字段、URL字段和HTTP协议版本字段3个字段组成，它们用空格分隔。

- 常用请求头字段

Accept ：接收类型，表示浏览器支持的MIME类型(对标服务端返回的Content-Type)

Content-Type： 客户端发送出去实体内容的类型

Cache-Control ：指定请求和响应遵循的缓存机制，如no-cache

If-Modified-Since ： 对应服务端的Last-Modified，用来匹配看文件是否变动，只能精确到1s之内

Expires： 缓存控制，在这个时间内不会请求，直接使用缓存．服务端时间

Max-age： 代表资源在本地缓存多少秒，有效时间内不会请求, 而是使用缓存

If-None-Match ：对应服务端的 ETag，用来匹配文件内容是否改变（非常精确)

Cookie： 有cookie并且同域访问时会自动带上

Referer： 该页面的来源URL(适用于所有类型的请求，会精确到详细页面地址，csrf拦截常用到这个字段)

Origin： 最初的请求是从哪里发起的(只会精确到端口） ,Origin比Referer更尊重隐私

User-Agent：用户客户端的一些必要信息，如UA头部等

- 常用响应头字段

Cache-Control：告诉所有的缓存机制是否可以缓存及哪种类型。

Content-Type：返回内容的MIME类型。

ETag：请求变量的实体标签的当前值。

Expires：响应过期的日期和时间。

Last-Modified：请求资源的最后修改时间。

Max-age： 代表资源在本地缓存多少秒，开启 Cache-Control 后有效

Location：用来重定向接收方到非请求URL的位置来完成请求或标识新的资源。

Pragma：包括实现特定的指令，它可应用到响应链上的任何接收方。

Proxy-Authenticate：它指出认证方案和可应用到代理的该URL上的参数。

refresh：应用于重定向或一个新的资源被创造，在5秒之后重定向（由网景提出，被大部分浏览器支持）

Retry-After：如果实体暂时不可取，通知客户端在指定时间之后再次尝试。

Server：服务器相关信息。

Set-Cookie：设置Http Cookie。

Acces-Control-Allow-Origin：服务器允许请求的 Origin 头部

## 缓存

通常 HTTP 缓存策略分为两种：强缓存 和 协商缓存

- 强缓存即强制直接使用缓存。强缓存不会向服务器发送请求，直接从缓存中读取资源，在 chrome 控制台的 network 选项中可以看到该请求返回 200 的状态码，并且size显示from disk cache或from memory cache；
- 协商缓存就得和服务器协商确认下这个缓存能不能用。协商缓存会先向服务器发送一个请求，服务器会根据这个请求的 request header 的一些参数来判断是否命中协商缓存，如果命中，则返回 [304](https://so.csdn.net/so/search?q=304&spm=1001.2101.3001.7020) 状态码并带上新的 response header 通知浏览器从缓存中读取资源。

![image-20230119115552647](/bit-dance/img/17.png)

### 强缓存

强缓存可以通过设置Expires和[Cache-Control](https://so.csdn.net/so/search?q=Cache-Control&spm=1001.2101.3001.7020) 两种响应头实现。如果同时存在，Cache-Control优先级高于Expires。

- Expires 响应头，它是 HTTP/1.0 的产物。代表该资源的过期时间，其值为一个绝对时间。它告诉浏览器在过期时间之前可以直接从浏览器缓存中存取数据。由于是个绝对时间，客户端与服务端的时间时差或误差等因素可能造成客户端与服务端的时间不一致，将导致缓存命中的误差。如果在Cache-Control响应头设置了 max-age 或者 s-max-age 指令，那么 Expires 会被忽略。
- Cache-Control 出现于 HTTP/1.1。可以通过指定多个指令来实现缓存机制。主要用表示资源缓存的最大有效时间。即在该时间端内，客户端不需要向服务器发送请求。优先级高于 Expires。其过期时间指令的值是相对时间，它解决了绝对时间的带来的问题。Cache-Control 有很多属性，不同的属性代表的意义也不同。
  - public 表明响应可以被任何对象（包括：发送请求的客户端，代理服务器，等等）缓存。
  - private 表明响应只能被单个用户缓存，不能作为共享缓存（即代理服务器不能缓存它）、
  - no-cache 不使用强缓存，需要与服务器验协商缓存验证。
  - no-store 缓存不应存储有关客户端请求或服务器响应的任何内容，即不使用任何缓存。
  - max-age 指定一个时间长度，在这个时间段内缓存是有效的，单位是s。

### 协商缓存

若未命中强缓存，则浏览器会将请求发送至服务器。服务器根据http请求头中的**`Last-Modify(服务器给客户端）/If-Modify-Since（客户端给服务器）`**或**`Etag(服->客）/If-None-Match(客->服)`**来判断是否命中协商缓存。如果命中，则http返回码为304 (你本地之前加载的资源是有效的)，浏览器从缓存中加载资源。

- Last-Modify/If-Modify-Since

  1. 浏览器第一次请求一个资源的时候, 服务器返回的**header**中会加上**Last-Modify**，Last-modify是一个时间标识，标识该资源的**最后修改时间**，
  2. 当浏览器再次请求该资源时 (进行协商请求时 )，发送的请求头中会包含**If-Modify-Since**，**该值为缓存之前返回的Last-Modify**。服务器收到If-Modify-Since后，根据实际服务器的资源的最后修改时间, 进行判断是否命中缓存。
  3. 如果命中缓存，则返回 **http****304**，并且不会返回资源内容，并且不会返回Last-Modify。

  由于对比的是**服务端的修改时间**，所以就算客户端与服务端时间差距, 也不会有问题。但是修改只能精确到秒级, 一秒进行了多次修改, 就不行了，于是出现了ETag/If-None-Match

- ETag/If-None-Match

  Etag/If-None-Match返回的是一个校验码，ETag可以保证每一个资源是唯一的，资源变化都会导致ETag变化。ETag值的变更则说明资源状态已经被修改。服务器根据浏览器上发送的**If-None-Match**值来判断是否命中缓存。ETag生成靠以下几种因子：

  1. 文件的i-node编号，是Linux/Unix用来识别文件的编号。
  2. 文件最后修改时间
  3. 文件大小

  Etag是服务器自动生成或者由开发者生成的对应资源在服务器端的唯一标识符，能够更加 **准确的控制缓存。**Last-Modified与ETag是可以一起使用的，服务器会优先验证ETag，一致的情况下，才会继续比对Last-Modified，

## Cookie

cookie 是通过 name - value 的键值对方式存储数据的方式，可以提供这个链接了解：https://baike.baidu.com/item/cookies/187064?fr=aladdin

Set-Cookie由服务器发送，它包含在响应请求的头部中。它用于在客户端创建一个Cookie，Cookie头由客户端发送，包含在HTTP请求的头部中。注意，只有cookie的domain和path与请求的URL匹配才会发送这个cookie。 这是 Set-Cookie 的属性值：

![image-20230119121032094](/bit-dance/img/18.png)

## HTTP 2.0

简单来说，HTTP/2（超文本传输协议第2版，最初命名为HTTP2.0），是HTTP协议的第二个主要版本，它相比 http / 1 更快 更稳定 更简单，他们的区别是：

- 二进制传输

在HTTP2.0中引入了新的编码机制，所有传输的数据都会被分割，并采用二进制格式编码。HTTP2.0会将所有传输的信息分为更小的消息和帧，并采用二进制格式编码，帧将会是协议中最小通信单位，至少标识出当前帧所属的数据流

- 数据流

http 1 每次建立连接需要进行TCP的三次握手和四次挥手

http 1.1 增加了长连接，在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟

http P2.0 中 帧是最小的数据单位，每个帧会标识出该帧属于哪个流，流是多个帧组成的数据流，一个TCP连接中存在多个流，即可以同时发送多个请求，对端可以通过帧中的表示知道该帧属于哪个请求，在客户端，这些帧乱序发送，到对端后再根据每个帧首部的流标识符重新组装。通过该技术，可以避免HTTP旧版本的队头阻塞问题，极大提高传输性能。

![多路复用](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/16/16bf8cde0afa5904~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.awebp)

- Header压缩

在HTTP2.0中，我们使用了HPACK（HTTP2头部压缩算法）压缩格式对传输的header进行编码，减少了header的大小。并在两端维护了索引表，用于记录出现过的header，后面在传输过程中就可以传输已经记录过的header的键名，对端收到数据后就可以通过键名找到对应的值。

- 服务器推送

在HTTP2.0中，服务端可以在客户端某个请求后，主动推送其他资源。 

- 安全

HTTP2.0使用了tls的拓展ALPN做为协议升级，除此之外，HTTP2.0对tls的安全性做了近一步加强，通过黑名单机制禁用了几百种不再安全的加密算法。

## HTTPS

HTTPS相对于HTTP有哪些不同呢？其实就是在HTTP跟TCP中间加多了一层加密层**TLS/SSL**, 原先是应用层将数据直接给到TCP进行传输，现在改成应用层将数据给到TLS/SSL，将数据加密后，再给到TCP进行传输

SL和TSL是一种安全协议。其中SSL是早起采用的安全协议，后来TSL是在SSL的基础上进一步标准化了SSL协议。在上面的图中可以看到，SSL和TSL位于传输层之上，在数据到达传输层之前都会经过SSL/TSL协议层处理，由SSL/TSL保证数据的机密性和完整性。

## HTTP使用场景

- 静态资源

方案：缓存+cdn+文件名hash 

[内容分发网络](https://www.zhihu.com/search?q=内容分发网络&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1604554133})（Content Delivery Network，简称CDN）是建立并覆盖在承载网之上，由分布在不同区域的边缘[节点服务器](https://www.zhihu.com/search?q=节点服务器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1604554133})群组成的分布式网络。

假设通过CDN加速的域名为`www.a.com`，接入CDN网络，开始使用加速服务后，当终端用户（北京）发起HTTP请求时，处理流程如下：

1. 当终端用户（北京）向`www.a.com`下的指定资源发起请求时，首先向LDNS（本地DNS）发起[域名解析](https://www.zhihu.com/search?q=域名解析&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1604554133})请求。
2. LDNS检查缓存中是否有`www.a.com`的IP地址记录。如果有，则直接返回给终端用户；如果没有，则向授权DNS查询。
3. 当授权DNS解析`www.a.com`时，返回域名CNAME `www.a.tbcdn.com`对应IP地址。
4. 域名解析请求发送至阿里云DNS调度系统，并为请求分配最佳节点IP地址。
5. LDNS获取DNS返回的解析IP地址。
6. 用户获取解析IP地址。
7. 用户向获取的IP地址发起对该资源的访问请求。


从这个例子可以了解到：

（1）CDN的加速资源是跟[域名绑定](https://www.zhihu.com/search?q=域名绑定&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1604554133})的。
（2）通过域名访问资源，首先是通过DNS分查找离用户最近的CDN节点（边缘服务器）的IP
（3）通过IP访问实际资源时，如果CDN上并没有缓存资源，则会到源站请求资源，并缓存到CDN节点上，这样，用户下一次访问时，该CDN节点就会有对应资源的缓存了。

- 登录

跨域：当一个请求url的协议、域名、端口三者之间任意一个与当前页面url不同即为跨域，跨域指的是浏览器不能执行其它网站的脚本。是由浏览器的同源策略造成的，是浏览器对JavaScript 施加的安全限制。跨域并不是请求发不出去，请求能发出去，服务端能收到请求并正常返回结果，只是结果被浏览器拦截了。

同源策略限制行为：

- `Cookie`、`LocalStorage`和`IndexDB`无法读取；

- 无法获得非同源网页的 DOM节点；

- `AJAX`请求不能发送；

解决跨域有这几种方法：

1. Proxy代理

Proxy通过服务端接口转发来实现对于跨域问题的问题，因为HTTP同源策略只在浏览器中生效。 这里介绍几种不同Proxy代理方法：

在vue.config.js中利用 WebpackDevServer 配置本地代理，项目上线需要把打包后的文件放在服务器上运行，而不是启动脚手架运行，也就没有内置web服务器做代理，所以此方式只适用于开发测试阶段

```json
module.exports = {
  //...
  devServer: {
    proxy: {
      '/api': {
        target: 'xxx',
        pathRewrite: {
          '^/api': ''
        },
        changeOrigin: true
      }
    }
  }
};
```

上线时需要使用 nginx 代理或者服务器配置 cors ：

```json
server {
 
    #nginx监听所有localhost:8080端口收到的请求
    listen       8080;
    server_name  localhost;
 
    location /api/ {
        proxy_pass http://192.168.25.20:9000;
    }
    error_page 404 /404.html;
        location = /40x.html {
    }
    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }
}
```

2. 跨域资源共享 CORS

目前最主流、最简单的方案，直接让后端设置响应头，允许资源共享就ok。CORS 是跨域资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，属于跨源 AJAX 请求的根本解决方法。

- 普通跨域请求：只需服务器端设置Access-Control-Allow-Origin
- 带cookie跨域请求：前后端都需要进行设置

参考： https://www.cnblogs.com/mochenxiya/p/16597545.html

3. JSONP

JSONP 是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，兼容性好（兼容低版本IE），缺点是只支持get请求，不支持post请求。核心思想：网页通过添加一个`<script>`元素，向服务器请求 JSON 数据，服务器收到请求后，将数据放在一个指定名字的回调函数的参数位置传回来。

```html
this.$http.jsonp('http://www.domain2.com:8080/login', {
    params: {},
    jsonp: 'handleCallback'
}).then((res) => {
    console.log(res); 
})
```

- JWT

JWT是JSON Web Token的缩写，是为了在网络应用环境间传递声明而执行的- -种基于JSON的开放标准((RFC 7519)。JWT本身没有定义任何技术实现，它只是定义了一种基于Token的会话管理的规则，涵盖Token需要包含的标准内容和Token的生成过程，特别适用于分布式站点的单点登录(SSO) 场景。
一个JWT Token格式是由`.`分割的三部分组成，这三部分依次是：

- 头部（Header）：JWT的Header中存储了所使用的加密算法和Token类型
- 负载（Payload）：Payload表示负载，也是一个JSON对象，JWT规定了7个官方字段供选用，除了官方字段，开发者也可以自己指定字段和内容。
- 签名（Signature）：Signature部分是对前两部分的签名，防止数据篡改。首先，需要指定一一个密钥(secret) 。 这个密钥只有服务器才知道，不能泄露给用户。然后，使用Header里面指定的签名算法(默认是HMAC SHA256)，按照下面的公式产生签名。

![在这里插入图片描述](https://img-blog.csdnimg.cn/3b0d3d2e8cd04eedadff941a9ae49cdc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5pio5aSp55qE5YWJ,size_20,color_FFFFFF,t_70,g_se,x_16)

## 实战

fetch是web提供的一个可以获取异步资源的api，它提供的api返回的是Promise对象：

具体参考： https://www.cnblogs.com/mq0036/p/14372997.html

在开发中我们习惯使用封装好的 axios 库：

具体参考： https://cloud.tencent.com/developer/article/1098141

## 了解更多

- WebSocket
  浏览器与服务器进行全双工通讯的网络技术，场景：实时性要求高，如聊天室，URL使用 ws:// 或 wss:// 等开头

- QUIC

  Quick UDP Internet Connection，基于UDP。

