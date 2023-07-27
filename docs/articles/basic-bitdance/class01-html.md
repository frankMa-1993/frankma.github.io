# class1-前端与基础课

本节课程围绕“**前端要解决的基本问题**”及“**什么是 HTML** ”两个基本问题展开，了解 **HTML 高效的编写原则**。

## 什么是前端

使用web技术栈解决多端的人机交互问题

## 技术栈

html（内容）+ css （样式）+javascript （行为）

通过网络协议（http等）与服务器端进行交互

## 前端关注的问题

1. 功能——满足需求
2. 美观
3. 无障碍——是不是所有人可用
4. 安全
5. 性能
6. 兼容性——手机web是不是都可以用
7. 体验

## 需要的工具

浏览器：chorme IE

编译器 ：vscode

## HTML

Hyper Text  Markup  Language

[HTML](https://developer.mozilla.org/zh-CN/docs/Glossary/HTML) 是一种相当简单的、由不同[元素](https://developer.mozilla.org/zh-CN/docs/Glossary/Element)组成的标记语言，它可以被应用于文本片段，使文本在文档中具有不同的含义，将文档结构化为逻辑块。并且可以将图片，影像等内容嵌入到页面中。

- html的语法

使用一个标签来封闭一段内容，HTML 标签不区分大小写。一般使用小写字母，在框架，比如 react 和 vue 中我们使用大写来描述自定义的组件

```html
<p>test</p>
```

如上文的例子，一个html段的内容有：

1. **开始标签**（Opening tag）：包含元素的名称（本例为 p），被左、右角括号所包围。表示元素从这里开始或者开始起作用 —— 在本例中即段落由此开始。
2. **结束标签**（Closing tag）：与开始标签相似，只是其在元素名之前包含了一个斜杠。这表示着元素的结尾 —— 在本例中即段落在此结束。
3. **内容**（Content）：元素的内容，本例中就是所输入的文本本身。
4. **元素**（Element）：开始标签、结束标签与内容相结合，便是一个完整的元素。

可以将一个html的元素嵌套在另一个html的元素中，形成一个树形式的结构：

```html
<p>
 <strong>a</strong> 			      <strong>b</strong>
</p>
```

其结构如下

```html
p - | - strong
    | - strong
```

可以打开 F12 调试来查看我们的网页上的结构，框出的区域是我们的 html 元素的树型结构 ，箭头标志的功能让我们可以快速检索到页面上的元素在树的哪一层

![1](/bit-dance/img/1.png)



- 元素分类

在 HTML 中有两种你需要知道的重要元素类别，块级元素和内联元素。

1. 块级元素在页面中以块的形式展现 —— 相对于其前面的内容它会出现在新的一行，其后的内容也会被挤到下一行展现。块级元素通常用于展示页面上结构化的内容，例如段落、列表、导航菜单、页脚等等。一个以块级元素不会被嵌套进内联元素中，但可以嵌套在其它块级元素中。

2. 内联元素通常出现在块级元素中并环绕文档内容的一小部分，而不是一整个段落或者一组内容。内联元素不会导致文本换行：它通常出现在一堆文字之间，例如超链接元素 

- 元素属性

html的标签都有不同的属性，他们绑定在不同的标签上，具有不同的效果，这些信息不会出现在实际的内容中，如下面的例子，我们为一个p标签添加了一个class属性，属性值是main

```html
<p class="main"></p>
```

有时你会看到没有值的属性，它是合法的。这些属性被称为布尔属性

```html
<input type="text" disabled="disabled">
```

- 特殊字符

要注意，因为html元素中，字符 `<`、`>`、`"`、`'` 和 `&` 是特殊字符，所以如果我们想要在页面文本中使用这些字符需要使用特殊的符号：

| 原义字符 | 等价字符引用 |
| :------- | :----------- |
| <        | `<`          |
| >        | `>`          |
| "        | `"`          |
| '        | `'`          |
| &        | `&`          |

## HTML文档

html元素需要组合起来才能成为一个完整的文档，一个html文档由几个部分组成

1. `<!DOCTYPE html>`: 声明文档类型。`<!DOCTYPE html>` 是最短有效的文档声明。
2. `<html></html>`: `<html>`元素。这个元素包裹了整个完整的页面，是一个根元素。
3. `<head></head>`: `<head>`元素。这个元素是一个容器，它包含了所有你想包含在 HTML 页面中但不想在 HTML 页面中显示的内容。
4. `<body></body>`: `<body>`元素。包含了你访问页面时所有显示在页面上的内容

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>我的测试站点</title>
  </head>
  <body>
    <p>这是我的页面</p>
  </body>
</html>
```



## head标签

在页面加载完成的时候，[head](https://developer.mozilla.org/zh-CN/docs/Glossary/Head) 标签里的内容，是不会在页面中显示出来的。它包含了诸如页面的标题、指向 [CSS](https://developer.mozilla.org/zh-CN/docs/Glossary/CSS) 的链接（如果你选择用 CSS 来为 HTML 内容添加样式）、指向自定义图标的链接和其它的元数据（描述 HTML 的数据，比如，作者和描述文档的重要关键词）等信息。

- 标题

title 元素，它可以为文档添加标题

```html
<head>
  <title>我的测试页面</title>
</head>
```

- meta

meta 这个元素简单的指定了文档的字符编码——在这个文档中被允许使用的字符集。如下的例子`utf-8` 是一个通用的字符集，它包含了任何人类语言中的大部分的字符。

```html
<head>
  <meta charset="utf-8">
</head>
```

- 图标

使用 link 对站点添加对自定义图标（**favicon**，为“favorites icon”的缩写）的引用，将在特定的场合（浏览器的收藏，或书签列表）中显示。

```html
<link rel="icon" href="favicon.ico" type="image/x-icon">
```

- css 和 JavaScrit

几乎你使用的所有网站都会使用 [CSS](https://developer.mozilla.org/zh-CN/docs/Glossary/CSS) 来让网页更加炫酷，并使用 [JavaScript](https://developer.mozilla.org/zh-CN/docs/Glossary/JavaScript) 来让网页有交互功能

使用 link 元素可以引入样式文件，`rel="stylesheet"` 表明这是文档的样式表，而 `href` 包含了样式表文件的路径：

```html
<link rel="stylesheet" href="my-css-file.css">
```

script 元素包含 `src` 属性来指向需要加载的 JavaScript 文件路径，同时最好加上 `defer` 以告诉浏览器在解析完成 HTML 后再加载 JavaScript

```html
<script src="my-js-file.js" defer></script>
```

- 主语言

可以（而且有必要）为站点设定语言，这个可以通过添加 [`lang` 属性](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/lang)到 HTML 开始的标签中来实现

```html
<html lang="zh-CN">
```



## 文本标签

大部分的文本结构由标题和段落组成：

在 HTML 中，每个段落是通过 p 元素标签进行定义的，

```html
<p>我是一个段落</p>
```

标题（Heading）则有六个标题元素标签 —— `<h1>`、`<h2>`、`<h3>`、`<h4>`、`<h5>`、`<h6>`。每个元素代表文档中不同级别的内容; `<h1>` 表示主标题（the main heading），`<h2>` 表示二级子标题（subheadings），`<h3>` 表示三级子标题（sub-subheadings），等等。

```html
<h1>我是文章的标题</h1>
```

HTML 也提供了相应的标签，用于标记某些文本，使其具有加粗、倾斜、下划线等效果：

- em 或者 i 用于产生斜体字，例如下面的例子将产生这样的效果：I am *glad* you weren't *late*

```html
<p>I am <em>glad</em> you weren't <em>late</em>.</p>
```

- strong 或者 b 用于强调一部分的内容，例如 ： This liquid is **highly toxic**.

```html
<p>This liquid is <strong>highly toxic</strong>.</p>
```

## 列表

列表在生活中随处可见，用于将事物按照一定的顺序排列，在html 中列表分为两种：

无序列表用于标记列表项目顺序无关紧要的列表，每份无序的清单从 ul 元素开始，用 li 标签标记每个单独的项目，html为列表默认提供了标记

```html
<ul>
  <li>豆浆</li>
  <li>油条</li>
  <li>豆汁</li>
  <li>焦圈</li>
</ul>
```

有顺列表则用于需要按照项目的顺序列出来的场景，他使用 ol 作为开头，有顺列表会以阿拉伯数字作为标记产生类似 1. 2. 这样的顺序记号

```html
<ol>
  <li>沿着条路走到头</li>
  <li>右转</li>
</ol>
```



## 超链接

超链接使我们能够将我们的文档链接到任何其他文档（或其他资源），也可以链接到文档的指定部分：

通过将文本 包裹在 a 元素内，并给它一个 [`href`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a#attr-href) 属性（也称为**超文本引用**或**目标**，它将包含一个网址）来创建一个基本链接。

```html
<a href="https://www.mozilla.org/zh-CN/">Mozilla 主页</a>
```

可以添加的另一个属性是 `title`（标题）；这旨在包含关于链接的补充信息，例如页面包含什么样的信息或需要注意的事情，当鼠标指针悬停在链接上时，标题将作为提示信息出现。

```html
<a href="https://www.mozilla.org/zh-CN/">
  <img src="mozilla-image.png" alt="链接至 Mozilla 主页的 Mozilla 标志">
```

当你链接到要下载的资源而不是在浏览器中打开时，你可以使用 download 属性来提供一个默认的保存文件名

```html
<a href="https://download.mozilla.org/?product=firefox-latest-ssl&os=win64&lang=zh-CN"
   download="firefox-latest-64bit-installer.exe">
  下载最新的 Firefox 中文版 - Windows（64 位）
</a>
```

当点击一个链接或按钮时，打开一个新的电子邮件发送信息而不是连接到一个资源或页面，你可以使用`mailto`:URL 的方案

```html
<a href="mailto:nowhere@mozilla.org">向 nowhere 发邮件</a>
```

target属性可以决定你打开资源的方式

- _self , 它使得目标文档载入并显示在相同的框架或者窗口中作为源文档
- _blank ,浏览器总在一个新打开、未命名的窗口中载入目标文档。
- _parent ，属性作用使得文档载入父窗口或者包含来超链接引用的框架的框架集
- _top ， 属性作用使得文档载入包含这个超链接的窗口

## 文档结构

html 还可以使用块级元素（例如“标题栏”、“导航菜单”、“主内容列”）来定义网站中的复合区域。它提供以下的标签来实现：

为了实现语义化标记，HTML 提供了明确这些区段的专用标签，例如：

- header ：页眉。
- nav ：导航栏。
- main ：主要区域，其中包含了 article ， section ， div 等元素包裹的区域
- aside ： 侧边栏
- footer ：页脚

如下是一个例子，要注意这些标签并没有实际的样式区别，他们都是一个块级元素，但是使用他们来标记一个网站可以更好的区别每个区域的作用，使得条例更加清晰：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>俱乐部</title>
  </head>
  <body>
    <header> <!-- 本站所有网页的统一主标题 -->
      <h1>聆听电子天籁之音</h1>
    </header>
    <nav> <!-- 本站统一的导航栏 -->
      <ul>
        <li><a href="#">主页</a></li>
        <!-- 共 n 个导航栏项目，省略…… -->
      </ul>
      <form> <!-- 搜索栏是站点内导航的一个非线性的方式。 -->
        <input type="search" name="q" placeholder="要搜索的内容">
        <input type="submit" value="搜索">
      </form>
    </nav>
    <main> <!-- 网页主体内容 -->
      <article>
        <!-- 此处包含一个 article（一篇文章），内容略…… -->
      </article>
      <aside> <!-- 侧边栏在主内容右侧 -->
        <h2>相关链接</h2>
        <ul>
          <li><a href="#">这是一个超链接</a></li>
          <!-- 侧边栏有 n 个超链接，略略略…… -->
        </ul>
      </aside>
    </main>
    <footer> <!-- 本站所有网页的统一页脚 -->
      <p>© 2050 某某保留所有权利</p>
    </footer>
  </body>
</html>
```

同时，对于一些要组织的项目或要包装的内容，现有的语义元素均不能很好对应，html提供了两个通用的标签

- div 是块级原酸
- span 是内联的元素

```html
<span class="editor-note">内联的</span>.
<div class="shopping-cart">块级的</div>
```

同时 html 也提供了分割的元素，`<br>` 可在段落中进行换行，`<hr>` 元素在文档中生成一条水平分割线：

```html
<p>aaaa<br>
aaaa<br>
aaaa</p>

<p>aaaa</p>
<hr>
<p>bbbbb</p>
```



## 多媒体

html 可以向网页中嵌入多种不同多媒体元素

- 图片

使用 img 标签可以把图片放到网页上

```html
<img src="dinosaur.jpg">
```

alt 的值应该是对图片的文字描述，用于在图片无法显示或不能被看到的情况，如果你的图片找不到，就会显示它

```html
<img src="images/dinosaur.jpg"
     alt="The head and torso of a dinosaur skeleton;
          it has a large head with long sharp teeth">
```

类似超链接，增加`title`属性来提供需要更进一步的支持信息，当你的鼠标悬停的时候，它就会展示 title 的内容

```html
<img src="images/dinosaur.jpg"
     alt="一只恐龙头部和躯干的骨架，它有一个巨大的头，长着锋利的牙齿。"
     width="400"
     height="341"
     title="A T-Rex on display in the Manchester University Museum">
```

- 矢量图

[SVG](https://developer.mozilla.org/zh-CN/docs/Web/SVG) 是用于描述矢量图像的[XML](https://developer.mozilla.org/zh-CN/docs/Glossary/XML)语言。它基本上是像 HTML 一样的标记，只是你有许多不同的元素来定义要显示在图像中的形状，以及要应用于这些形状的效果。SVG 用于标记图形，而不是内容。

```html
<svg version="1.1"
     baseProfile="full"
     width="300" height="200"
     xmlns="http://www.w3.org/2000/svg">
  <rect width="100%" height="100%" fill="black" />
  <circle cx="150" cy="100" r="90" fill="blue" />
</svg>
```

SVG 除了迄今为止所描述的以外还有其他优点：

1. 矢量图像中的文本仍然可访问（这也有利于 [SEO](https://developer.mozilla.org/zh-CN/docs/Glossary/SEO)）。

2. SVG 可以很好地适应样式/脚本，因为图像的每个组件都是可以通过 CSS 或通过 JavaScript 编写的样式的元素。

我可以通过 img 标签插入 svg，对于不支持 SVG（IE 8 及更低版本，Android 2.3 及更低版本）的浏览器，您可以从`src`属性引用 PNG 或 JPG，并使用[`srcset`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/img#attr-srcset)属性 只有最近的浏览器才能识别）来引用 SVG

```html
<svg width="300" height="200">
    <rect width="100%" height="100%" fill="green" />
</svg>
```

你可以通过直接书写 svg 标签来插入 svg，这样将 SVG 内联减少 HTTP 请求，可以减少加载时间。也可以为 SVG 元素分配`class`和`id`，并使用 CSS 修改样式，内联 SVG 是唯一可以让您在 SVG 图像上使用 CSS 交互（如`:focus`）和 CSS 动画的方法（即使在常规样式表中），您也可以使其成为超链接。

你也可以使用`<iframe>`嵌入 SVG 文档

```html
<iframe src="triangle.svg" width="500" height="500" sandbox>
    <img src="triangle.png" alt="Triangle with three unequal sides" />
</iframe>
```



- 视频和音频

video 元素可以让我们嵌入一段视频，你可以使用 `controls` 属性来使你的视频包含浏览器提供的控件界面

```html
<video src="rabbit320.webm" controls>
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
</video>
```

当然 ，因为浏览器的问题，可能有些格式的视频并不支持再用户的浏览器播放，我们可以将 `src` 属性从 `<video>` 标签中移除，转而将它放在几个单独的标签 `<source>` 标签，这样将会播放第一个格式匹配的媒体

```html
<video controls>
  <source src="rabbit320.mp4" type="video/mp4">
  <source src="rabbit320.webm" type="video/webm">
  <p>你的浏览器不支持 HTML5 视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
</video>
```

以下是其他的常用属性

1. autoplay：是不是自动播放
2. loop：是不是循环播放
3. muted：是不是默认关闭声音
4. poster：视频的封面，指向一个图片的izhi
5. preload：是不是需要缓冲
   - `"none"` ：不缓冲
   - `"auto"` ：页面加载后缓存媒体文件
   - `"metadata"` ：仅缓冲文件的元数据

audio 音频标签和 视频几乎一样，只是不支持 `poster` 属性

```html
<audio controls>
  <source src="viper.mp3" type="audio/mp3">
  <source src="viper.ogg" type="audio/ogg">
  <p>你的浏览器不支持 HTML5 音频，可点击<a href="viper.mp3">此链接</a>收听。</p>
</audio>
```

值得提出的是：

如果我们改用 picture 就它使浏览器在不同资源间做出选择，图片可以像视频和音频一样使用 source，使用 media 可以让我们通过当前屏幕的大小来改变图片，如下： max-width 代表最大尺寸是 799时使用，min-width 代表最小是 800px 使用

```html
<picture>
  <source media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg" />
  <source media="(min-width: 800px)" srcset="elva-800w.jpg" />
  <img src="elva-800w.jpg" alt="Chris standing up holding his daughter Elva" />
</picture>
```



## 嵌入技术

iframe 元素旨在允许你将其他 Web 文档嵌入到当前文档中。这很适合将第三方内容嵌入你的网站，你可能无法直接控制，也不希望实现自己的版本——例如来自在线视频提供商的视频。

```html
<iframe src="https://developer.mozilla.org/zh-CN/docs/Glossary"
        width="100%" height="500" frameborder="0"
        allowfullscreen sandbox>
  <p> <a href="https://developer.mozilla.org/zh-CN/docs/Glossary">
    Fallback link for browsers that don't support iframes
  </a> </p>
</iframe>
```

它包含以下的属性

- allowfullscreen：是不是可以全屏幕
- frmeborder：如果设置为 1，则会告诉浏览器在此框架和其他框架之间绘制边框，这是默认行为。0 删除边框。
- src ： 路径
- sandbox ：该属性可以提高安全性设置

## 表格

表格是由行和列组成的结构化数据集，每一个表格的内容都包含在 body 标签中，其中

- caption：为表格添加一个标题
- tbody : 嵌套在 table 元素中,包裹整个主体元素

- tr ： 代表行
- td ：代表每行的一个单元格、
- thead ：放置在头部的位置，因为它通常代表第一行，第一行中往往都是每列的标题 th
- th ：代表表格的标题单元格，即使你不给表格添加你自己的样式，表格标题也会带有一些默认样式：加粗和居中，让标题可以突出显示
- tfoot ： 放置在底部的位置，往往是对前面所有行的总结
- colspan ：接收一个数字，使得一个单元格的的宽度是多个单元格
- rowspan：接收一个数字，使得一个单元格的的高度是多个单元格
- colgroup :  包裹 col 元素
- col ： 定义“列的样式”，每一个 `<col>` 都会指定每列的样式

一个简单的例子是：
```html
<table>
  <caption>Dinosaurs in the Jurassic period</caption>
  <colgroup>
    <col>
    <col style="background-color: yellow">
  </colgroup>
  <thead>
    <th>Data 1</th>
    <th>Data 2</th>
  </thead>
  <tbody>
    <tr>
    	<td>Calcutta</td>
    	<td>Orange</td>
  	</tr>
  </tbody>
  <tfoot>
    <td>Robots</td>
    <td>Jazz</td>
  </tfoot>
</table>
```

