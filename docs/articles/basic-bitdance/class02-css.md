# class2 - CSS

CSS 作为前端技术栈中关键一环，对页面元素及样式呈现起到了直接作用。本节课旨在通过对 CSS 的工作流程及原理、页面中 CSS 使用方法等详细解读，帮助前端新手建立对 CSS 的全面而深刻的认知。

## CSS概念

CSS 即 Cascading Style Sheets，是用来定义页面元素样式的。例如设置颜色，位置和大小等。

一个css由几个部分组成，如下，h1叫做选择器，用于选取一类的对象，给与这些对象的属性由大括号包裹，内部由  key：value 对组成，其代表将 key 属性设置为 value：

```css
h1 {
    color : white;
}
```

## CSS的写法

css由三种使用方式 ，一般我们使用第一种方式

- 外链：用户在写好 css 文件后，将其引用进来：

```html
<link rel="stylesheet" href="main.css">
```

- 嵌入：用户在页面的 head 内部使用style开辟一部分空间来写css

```html
<head>
    <style>
        li { margin:0 }
    </style>
</head>
```

- 内联：用户在对应的 html 元素上直接写 css 

```html
<div style="color:white"></div>
```

## CSS的工作过程

![image-20230115173633104](/bit-dance/img/2.jpg)

如下，在页面打开的时候，首先加载了所有的html元素，之后加载他们的css，css决定了每个元素的位置等属性，共同生成了 dom 树，dom树决定了我们看到的页面

## 选择器

- CSS 元素选择器

元素选择器根据元素名称来选择 HTML 元素。如下：这条元素将会选择所有的 p元素

```css
p {
  text-align: center;
  color: red;
}
```

- id 选择器

id 选择器使用 HTML 元素的 id 属性来选择特定元素。元素的 id 在页面中是唯一的，因此 id 选择器用于选择一个唯一的元素，如下：这条 CSS 规则将应用于 id="para1" 的 HTML 元素：

```css
#para1 {
  text-align: center;
  color: red;
}
```

- CSS 类选择器

类选择器选择有特定 class 属性的 HTML 元素，如需选择拥有特定 class 的元素，请写一个句点（.）字符，后面跟类名。如下：所有带有 class="center" 将会被选择

```css
.center {
  text-align: center;
  color: red;
}
```

注意：class 和 id 不能以数字开头

- CSS 通用选择器

通用选择器（*）选择页面上的所有的 HTML 元素。

```css
* {
  text-align: center;
  color: blue;
}
```

- 属性选择器

属性选择器可以选择具有对应属性，或者对应属性是某个值的元素，如下：我们可以选择所有具有 disabled 的元素

```css
[disabled]{
    
}
```

如果我们想要某个属性是对应的值，我们可以这样写

```css
[input="password"]{
    
}
```

我们也可以使用一些方式进行匹配

- ^= 表示以某部分开头
- $= 表示以某部分结尾

## 组合选择器

组合器是解释选择器之间关系的某种机制，CSS 中有四种不同的组合器：

- 后代选择器 (空格)，后代选择器匹配属于指定元素后代的所有元素。

```html
<!DOCTYPE html>
<html>
<head>
<style>
div p {
  background-color: yellow;
}
</style>
</head>
<body>

<div>
  <p>Paragraph 1 in the div.</p>
  <p>Paragraph 2 in the div.</p>
  <section><p>Paragraph 3 in the div.</p></section>
</div>

<p>Paragraph 4. Not in a div.</p>
<p>Paragraph 5. Not in a div.</p>

</body>
</html>

```

在这个例子中，Paragraph 1 2 3 将会被选择

- 子选择器 (`>`)，子选择器匹配属于指定元素子元素的所有元素。

```html
<!DOCTYPE html>
<html>
<head>
<style>
div > p {
  background-color: yellow;
}
</style>
</head>
<body>

<div>
  <p>Paragraph 1 in the div.</p>
  <p>Paragraph 2 in the div.</p>
  <section><p>Paragraph 3 in the div.</p></section> <!-- not Child but Descendant -->
  <p>Paragraph 4 in the div.</p>
</div>

<p>Paragraph 5. Not in a div.</p>
<p>Paragraph 6. Not in a div.</p>

</body>
</html>
```

在这个例子中，Paragraph 1 2 4 将会被选择

- 相邻兄弟选择器 (`+`)，相邻兄弟选择器匹配所有作为指定元素的相邻同级的元素。

```html
<!DOCTYPE html>
<html>
<head>
<style>
div + p {
  background-color: yellow;
}
</style>
</head>
<body>

<div>
  <p>Paragraph 1 in the div.</p>
  <p>Paragraph 2 in the div.</p>
</div>

<p>Paragraph 3. Not in a div.</p>
<p>Paragraph 4. Not in a div.</p>

</body>
</html>

```

在这个例子中，Paragraph 3 将会被选择

- 通用兄弟选择器 (`~`)，通用兄弟选择器匹配属于指定元素的同级元素的所有元素。

```html
<!DOCTYPE html>
<html>
<head>
<style>
div ~ p {
  background-color: yellow;
}
</style>
</head>
<body>

<p>Paragraph 1.</p>

<div>
  <p>Paragraph 2.</p>
</div>

<p>Paragraph 3.</p>
<code>Some code.</code>
<p>Paragraph 4.</p>

</body>
</html>
```

在这个例子中，Paragraph 3 4 将会被选择

- 选择器组，如果我们使用 `，` 分割一组选择器，那么他们将都会被选择

```html
<!DOCTYPE html>
<html>
<head>
<style>
div , p {
  background-color: yellow;
}
</style>
</head>
<body>
	<div>Paragraph 1.</div>
	<p>Paragraph 2.</p>
</body>
</html>
```

在这个例子中，Paragraph 1 2 将会被选择

## 伪类

伪类用于定义元素的特殊状态，例如，它可以用于：设置鼠标悬停在元素上时的样式、为已访问和未访问链接设置不同的样式、设置元素获得焦点时的样式等等

- 状态的伪类

这类的伪类可以根据元素所处的状态来改变样式，例如：

```css
/* 未访问的链接 */
a:link {
  color: #FF0000;
}

/* 已访问的链接 */
a:visited {
  color: #00FF00;
}

/* 鼠标悬停链接 */
a:hover {
  color: #FF00FF;
}

/* 已选择的链接 */
a:active {
  color: #0000FF;
}
```

- 结构的伪类

根据你dom节点出现的位置来决定是不是选择某个元素，

例如：`:first-child` 伪类与指定的元素匹配：该元素是另一个元素的第一个子元素，在下面的例子中，选择器匹配作为任何元素的第一个子元素的任何 p 元素：

```css
p:first-child {
  color: blue;
}
```

在下面的例子中，选择器匹配作为另一个元素的第一个子元素的 p 元素中的所有 i 元素：

```css
p:first-child i {
  color: blue;
}
```

- lang伪类

` :lang ` 伪类允许您为不同的语言定义特殊的规则。在下面的例子中，` :lang ` 为属性为 `lang="en"` 的 `q` 元素定义引号：

```html
<html>
<head>
<style>
q:lang(en) {
  quotes: "~" "~";
}
</style>
</head>
<body>

<p>Some text <q lang="en">A quote in a paragraph</q> Some text.</p>

</body>
</html>
```

完整的伪类规则如下：

| 选择器                                                       | 例子                  | 例子描述                                                     |
| :----------------------------------------------------------- | :-------------------- | :----------------------------------------------------------- |
| [:active](https://www.w3school.com.cn/cssref/selector_active.asp) | a:active              | 选择活动的链接。                                             |
| [:checked](https://www.w3school.com.cn/cssref/selector_checked.asp) | input:checked         | 选择每个被选中的 `<input>` 元素。                              |
| [:disabled](https://www.w3school.com.cn/cssref/selector_disabled.asp) | input:disabled        | 选择每个被禁用的 `<input>` 元素。                              |
| [:empty](https://www.w3school.com.cn/cssref/selector_empty.asp) | p:empty               | 选择没有子元素的每个 `<p>` 元素。                              |
| [:enabled](https://www.w3school.com.cn/cssref/selector_enabled.asp) | input:enabled         | 选择每个已启用的 `<input>` 元素。                              |
| [:first-child](https://www.w3school.com.cn/cssref/selector_first-child.asp) | p:first-child         | 选择作为其父的首个子元素的每个 `<p>` 元素。                    |
| [:first-of-type](https://www.w3school.com.cn/cssref/selector_first-of-type.asp) | p:first-of-type       | 选择作为其父的首个 `<p>` 元素的每个 `<p>` 元素。                 |
| [:focus](https://www.w3school.com.cn/cssref/selector_focus.asp) | input:focus           | 选择获得焦点的 `<input>` 元素。                                |
| [:hover](https://www.w3school.com.cn/cssref/selector_hover.asp) | a:hover               | 选择鼠标悬停其上的链接。                                     |
| [:in-range](https://www.w3school.com.cn/cssref/selector_in-range.asp) | input:in-range        | 选择具有指定范围内的值的 `<input>` 元素。                      |
| [:invalid](https://www.w3school.com.cn/cssref/selector_invalid.asp) | input:invalid         | 选择所有具有无效值的 `<input>` 元素。                          |
| [:lang(*language*)](https://www.w3school.com.cn/cssref/selector_lang.asp) | p:lang(it)            | 选择每个 lang 属性值以 "it" 开头的 `<p>` 元素。                |
| [:last-child](https://www.w3school.com.cn/cssref/selector_last-child.asp) | p:last-child          | 选择作为其父的最后一个子元素的每个 `<p>` 元素。                |
| [:last-of-type](https://www.w3school.com.cn/cssref/selector_last-of-type.asp) | p:last-of-type        | 选择作为其父的最后一个 `<p>` 元素的每个 `<p>` 元素。             |
| [:link](https://www.w3school.com.cn/cssref/selector_link.asp) | a:link                | 选择所有未被访问的链接。                                     |
| [:not(*selector*)](https://www.w3school.com.cn/cssref/selector_not.asp) | :not(p)               | 选择每个非` <p> `元素的元素。                                  |
| [:nth-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-child.asp) | p:nth-child(2)        | 选择作为其父的第二个子元素的每个 `<p>` 元素。                  |
| [:nth-last-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-child.asp) | p:nth-last-child(2)   | 选择作为父的第二个子元素的每个`<p>`元素，从最后一个子元素计数。 |
| [:nth-last-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-of-type.asp) | p:nth-last-of-type(2) | 选择作为父的第二个`<p>`元素的每个`<p>`元素，从最后一个子元素计数 |
| [:nth-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-of-type.asp) | p:nth-of-type(2)      | 选择作为其父的第二个 `<p>` 元素的每个 `<p>` 元素。               |
| [:only-of-type](https://www.w3school.com.cn/cssref/selector_only-of-type.asp) | p:only-of-type        | 选择作为其父的唯一 `<p>` 元素的每个 `<p>` 元素。                 |
| [:only-child](https://www.w3school.com.cn/cssref/selector_only-child.asp) | p:only-child          | 选择作为其父的唯一子元素的 `<p>` 元素。                        |
| [:optional](https://www.w3school.com.cn/cssref/selector_optional.asp) | input:optional        | 选择不带 "required" 属性的 `<input>` 元素。                    |
| [:out-of-range](https://www.w3school.com.cn/cssref/selector_out-of-range.asp) | input:out-of-range    | 选择值在指定范围之外的 `<input>` 元素。                        |
| [:read-only](https://www.w3school.com.cn/cssref/selector_read-only.asp) | input:read-only       | 选择指定了 "readonly" 属性的 `<input>` 元素。                  |
| [:read-write](https://www.w3school.com.cn/cssref/selector_read-write.asp) | input:read-write      | 选择不带 "readonly" 属性的 `<input>` 元素。                    |
| [:required](https://www.w3school.com.cn/cssref/selector_required.asp) | input:required        | 选择指定了 "required" 属性的 `<input>` 元素。                  |
| [:root](https://www.w3school.com.cn/cssref/selector_root.asp) | root                  | 选择元素的根元素。                                           |
| [:target](https://www.w3school.com.cn/cssref/selector_target.asp) | #news:target          | 选择当前活动的 #news 元素（单击包含该锚名称的 URL）。        |
| [:valid](https://www.w3school.com.cn/cssref/selector_valid.asp) | input:valid           | 选择所有具有有效值的 `<input>` 元素。                          |
| [:visited](https://www.w3school.com.cn/cssref/selector_visited.asp) | a:visited             | 选择所有已访问的链接。                                       |
| [::after](https://www.w3school.com.cn/cssref/selector_after.asp) | p::after              | 在每个 `<p>` 元素之后插入内容。                                |
| [::before](https://www.w3school.com.cn/cssref/selector_before.asp) | p::before             | 在每个 `<p>` 元素之前插入内容。                                |
| [::first-letter](https://www.w3school.com.cn/cssref/selector_first-letter.asp) | p::first-letter       | 选择每个 `<p>` 元素的首字母。                                  |
| [::first-line](https://www.w3school.com.cn/cssref/selector_first-line.asp) | p::first-line         | 选择每个 `<p>` 元素的首行。                                    |
| [::selection](https://www.w3school.com.cn/cssref/selector_selection.asp) | p::selection          | 选择用户选择的元素部分。                                     |

## 选择器的层级

如果有两条或两条以上指向同一元素的冲突 CSS 规则，则浏览器将遵循一些原则来确定哪一条是最具体的，并因此胜出。将特异性（specificity）视为得分/等级，能够确定最终将哪些样式声明应用于元素。每个选择器在特异性层次结构中都有其位置。以下四种类别定义了选择器的特异性级别：

- *style行内样式* - 行内（内联）样式直接附加到要设置样式的元素。

- *ID* - ID 是页面元素的唯一标识符，例如 #navbar。

- *类、属性和伪类* - 此类别包括 .classes、[attributes] 和伪类，例如：:hover、:focus 等。

- *元素和伪元素* - 此类别包括元素名称和伪元素，比如 h1、div、:before 和 :after。

从 0 开始，为 style 属性添加 1000，为每个 ID 添加 100，为每个属性、类或伪类添加 10，为每个元素名称或伪元素添加 1。

```css
A: h1    1
B: #content h1   101
C: <div id="content"><h1 style="color: #ffffff">Heading</h1></div>    1000
```

如果有多个 css 层级相同，则后定义的一项生效

```css
p {
    color:white
}
p {
    color:red  /* 生效 */
}
```

如果你想要某一项属性无视这些规则直接生效，可以添加 `!important` 属性

```css
p {
    color:white!important;  /* 生效 */
}
p {
    color:red ; /* 不生效 */
}
```

## CSS 继承

某些属性会自动继承其父元素的计算值，除非显式指定⼀个值。一般文字、字体相关属性都可以继承，盒模型宽度、高度、边距等都不可以继承。

如果一个属性不可继承，我们可以使用 inherit 这个关键字让它能从父级继承

```css
// box-sizing 这个属性是不可以继承的。这样设置之后，这个属性就要从父级开始读取。
* {
  box-sizing: inherit;
}
 
// 如果元素不单独设置 box-sizing 这个属性，那么所有元素都继承 html 的 box-sizing 属性。
html {
  box-sizing: border-box;
}
```

 在 CSS 中，每一个属性都有一个初始值。例如：

```css
// background-color 的初始值为 transparent
background-color: transparent;
 
// margin-left 的初始值为 0
margin-left: 0;
```

在CSS的值计算过程如下：

![img](https://img-blog.csdnimg.cn/3836c59fa7c7426c88bd6fbb9ccdcbe4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBATEFpbktI,size_20,color_FFFFFF,t_70,g_se,x_16)

## CSS单位

当需要表达一个属性某些值的大小的时候，css有多种方式进行表达，他们有不同的单位，具体如下：

- 相对单位

相对对单位就是相对于另一个长度的长度：

1. 常见的字体相对单位有：em、rem，1em 等于元素的font-size属性的值，em 是相对于父元素的字体大小进行计算的。如果当前对行内文本的字体尺寸未进行显示设置，则相对于浏览器的默认字体尺寸；rem相对于em就简单了很多，它是根据页面的根元素（根元素）的字体大小来计算的

2. ex 和 ch 都是排版用的单位，它们的大小取决于元素的font-size 和 font-family属性。ex 指的是所用字体中小写字母 x 的高度。ch 和 ex 类似，不过它是基于数字 0 的宽度计算的。

3. vw、vh、vmax 和 vmin 这四个单位都是视窗单位，所谓的视窗，在web端指的就是可视区域，移动端的视窗指的就是布局视窗。这四个单位指的是：

   - vw：视窗宽度的百分比。
   - vh：视窗高度的百分比。
   - vmax：较大的 vh 和 vw。
   - vmin：较小的 vh 和 vw。

   假如一个浏览器的高度是800px，那么1vh的值就是8px。vh和vw的大小总是和视窗的高度和宽度有关。

- 绝对单位

在 CSS 中，绝对单位包括：px 、pt 、pc、 cm 、 mm 、in 等。绝对单位是一个固定的值，它们的大小取决于值以及屏幕的分辨率

```css
1in = 25.4mm = 2.54cm = 6pc = 72pt =96px
```

- 频率单位

CSS中的频率单位有两个：赫兹（Hz）和千赫兹（kHz），频率单位使用在听或说级联样式表中。频率可以被用来改变一个语音阅读文本的音调。低频率就是低音，高频率就是高音。

- 时间单位

CSS中的时间单位有两个：秒（s）和毫秒（ms），时间单位主要用于过度和动画中

- 分辨率单位

CSS中的分辨率单位有三个：dpi、dpcm、dppx。分辨率单位主要用于媒体查询等操作：

1. dpi 全称为 dots per inch，表示每英寸包含的点的数量。普通屏幕通常包含 72或96个点，大于 192dpi 的屏幕被称为高分屏。
2. dpcm 全称为 dots per centimeter，表示每厘米包含的点的数量。
3. dppx 全称为 dots per pixel，表示每像素（px）包含点的数量。

```css
@media screen and (min-resolution: 96dpi) { ... }
```

- 角度单位

CSS中的角度单位有四个：deg、grad、rad、turn，通常情况下，一个完整的旋转就是360度。所以，所有的角度都在0-360度之间

1. deg 全称为 Degress，表示度，一个圆总共360度。
2. grad 全称为 Gradians，表示梯度，一个圆总共400梯度。
3. rad 全称为 Radians，表示弧度，一个圆总共2π弧度。
4. turn 全称为 Turns，表示圈（转），一个圆总共一圈（转）。

```css
90deg = 100grad = 0.25turn ≈ 1.570796326794897rad
```

- 百分比单位

百分比（%）也是我们比较常用的单位之一，所有接受长度值的属性都可以使用百分比单位。但是不同属性使用该单位的效果可能并不一样。但是都需要有一个参照值，也就是说百分比值是一个相对的值。



## 色彩

在css中，指定颜色是通过使用预定义的颜色名称，或 RGB、HEX、HSL、RGBA、HSLA 值。你可以为边框、字体、背景等设置颜色，他们分别是 border、color、background-color 属性：

1. 可以使用颜色名称来指定颜色：例如 Gray，Orange等
2. 可以使用下面的公式将颜色指定为 RGB 值：rgb(*red*, *green*, *blue*)，每个参数 (*red*、*green* 以及 *blue*) 定义了 0 到 255 之间的颜色强度。
3. RGBA 颜色值是具有 alpha 通道的 RGB 颜色值的扩展 - 它指定了颜色的不透明度。rgba(*red*, *green*, *blue*, *alpha*)
4. 在 CSS 中，可以使用以下格式的十六进制值指定颜色  *rrggbb*，其中 rr（红色）、gg（绿色）和 bb（蓝色）是介于 00 和 ff 之间的十六进制值
5. 在 CSS 中，可以使用色相、饱和度和明度（HSL）来指定颜色，格式如下：hsl(*hue*, *saturation*, *lightness*) , hsla(*hue*, *saturation*, *lightness*, *alpha*)，
   - 色相（*hue*）是色轮上从 0 到 360 的度数。0 是红色，120 是绿色，240 是蓝色。
   - 饱和度（*saturation*）是一个百分比值，0％ 表示灰色阴影，而 100％ 是全色。
   - 亮度（*lightness*）也是百分比，0％ 是黑色，50％ 是既不明也不暗，100％是白色 
   - *alpha* 参数是介于 0.0（完全透明）和 1.0（完全不透明）之间的数字：

```css
p {
    color:red;
}
p {
    color:rgba(0,0,0,1);
}
p {
    color:#000000;
}
p {
    color:hsl(0, 100%, 90%);;
}
```



## 文字样式

在css中，文本具有很多属性

- 文本对齐

`text-align` 属性用于设置文本的水平对齐方式。文本可以左对齐或右对齐，或居中对齐。当 `text-align` 属性设置为 "justify" 后，将拉伸每一行，以使每一行具有相等的宽度，并且左右边距是直的。

```css
h1 {
  text-align: center; /* 居中 */
}

h2 {
  text-align: left; /* 左对齐 */
}

h3 {
  text-align: right; /* 右对齐 */
}
```

`vertical-align` 属性设置元素的垂直对齐方式。可以让文字顶部对齐，中间对齐或者底部对齐：

```css
img {
  vertical-align: top; /* 顶部对齐 */
}

img {
  vertical-align: middle; /* 居中 */
}

img {
  vertical-align: bottom; /* 底部 */
}
```

- 文本方向

`direction` 和 `unicode-bidi` 属性可用于更改元素的文本方向，常用是如下的设置可以使得文字反方向：

```css
p {
  direction: rtl;
  unicode-bidi: bidi-override;
}
```

- 文字装饰

`text-decoration` 属性用于设置或删除文本装饰。

```css
h1 {
  text-decoration: overline; /* 顶部横线 */
}

h2 {
  text-decoration: line-through; /* 穿过文字的横线 */
}

h3 {
  text-decoration: underline; /* 下划线 */
}
```

- 文本转换

`text-transform` 属性用于指定文本中的大写和小写字母。它可用于将所有内容转换为大写或小写字母，或将每个单词的首字母大写：

```css
p.uppercase {
  text-transform: uppercase;/* 大写 */
}

p.lowercase {
  text-transform: lowercase;/* 小写 */
}

p.capitalize {
  text-transform: capitalize;/* 首字母大写 */
}
```

- 间距

`text-indent` 属性用于指定文本第一行的缩进：

```css
p {
  text-indent: 50px;
}
```

`letter-spacing` 属性用于指定文本中字符之间的间距。

```css
h1 {
  letter-spacing: 3px;
}
```

`line-height` 属性用于指定行之间的间距：

```css
p {
  line-height: 0.8;
}
```

`word-spacing` 属性用于指定文本中单词之间的间距：

```css
h1 {
  word-spacing: 10px;
}
```

`white-space` 属性指定元素内部空白的处理方式：

|          | 换行符 | 空格和 Tab | 文本超出容器宽度 |
| :------- | :----: | :--------: | :--------------: |
| nomal    |  忽略  |    折叠    |       换行       |
| nowrap   |  忽略  |    折叠    |      不换行      |
| pre      |  换行  |  保持原样  |      不换行      |
| pre-wrap |  换行  |  保持原样  |       换行       |
| pre-line |  换行  |    折叠    |       换行       |

```css
p {
  white-space: nowrap;
}
```

- 文字阴影

`text-shadow` 属性为文本添加阴影，设置阴影是一个三维的过程，分别需要设置阴影的，水平阴影，垂直阴影，模糊效果（扩散距离），颜色，其中第三项可以省略

```css
h1 {
  text-shadow: 2px 2px 5px red;
}
```

- 字体

选择正确的字体会对网站的用户体验产生巨大影响，在 CSS 中，我们使用 font-family 属性规定文本的字体，如果字体名称不止一个单词，则必须用引号引起来，多个字体名称应以逗号分隔：

```css
.p1 {
  font-family: "Times New Roman", Times, serif;
}
```

- 字体样式

`font-style` 属性主要用于指定斜体文本，此属性可设置三个值：

- normal - 文字正常显示
- italic - 文本以斜体显示
- oblique - 文本为“倾斜”（倾斜与斜体非常相似，但支持较少）

```css
p.normal {
  font-style: normal;
}

p.italic {
  font-style: italic;
}

p.oblique {
  font-style: oblique;
}
```

`font-weight` 属性指定字体的粗细，它可以填写 normal，lighter，bold 三种值，也可以填写从 100开始的整百的数字 ：

```css
p.normal {
  font-weight: normal;
}

p.thick {
  font-weight: bold;
}
p.thicker {
  font-weight: 900;
}
```

`font-variant` 属性指定是否以 small-caps 字体（小型大写字母）显示文本。在 small-caps 字体中，所有小写字母都将转换为大写字母。但是，转换后的大写字母的字体大小小于文本中原始大写字母的字体大小。

- 字体大小

`font-size` 属性设置文本的大小。

```css
h1 {
  font-size: 40px;
}
```

## 盒子模型

盒模型是CSS控制页面布局的一个非常重要的概念，页面上的所有元素，包括文本、图像、超级链接、div块等，都可以被看作盒子。网页页面布局的过程可以看作在页面空间中摆放盒子的过程。盒模型由内到外依次分为内容（content）、填充（padding）、边框（border）和边界（margin）4部分。

![3](/bit-dance/img/3.jpg)

CSS中的盒子可分为block类型与inline类型，也就是块级和内联，使用display属性来定义。 block类型是独占一行，而inline是可一个多个在一行。

- 内容

内容（content）是盒子里的“物品”，是盒模型中必须有的部分，可以是网页上的任何元素，如文本、图片、视频等各种信息。

盒子模型的内容需要设置其 `height` 和 `width` 属性，他们是元素的高度和宽度，任何元素都具有高度和宽度，如果不在css设置，其默认值是 auto 将由我们的浏览器为我们计算

`overflow` 属性让我们定义如果内容超出了其长度或者宽度，我们应该怎么处理，常见的值有：auto（默认） | visible （可见）| hidden （隐藏）| scroll （出现滚动条）

同时这个属性我们可以扩展为 `overflow-x` : 水平溢出；`overflow-y` : 垂直溢出； `text-overflow` : 文字溢出；文字溢出中如果设置为 ellipsis，则可以使用 ... 省略号，是我们常用的手段

- 填充

填充（padding）用来设置内容和盒子边框之间的距离，你可以用以下的方式为四个方向分别设置 padding，或者你的padding 可以设置四个值，按照从 top 开始的顺时针方向发生作用 ：

![4](/bit-dance/img/4.jpg)

- 边界

边界（margin）是盒模型与其他盒模型之间的距离，使用margin属性定义，规则同上

- 边框

边框（border）是盒模型中介于填充（padding）和边界（margin）之间的分界线，你可以为边框设置（1）边框样式（2）边框宽度（3）边框颜色：

```css
.b1 {
	border-style: inset;
	border-width: 10px;
	border-color: rgb(100%, 0%, 0%);
}
.b3 {
	border: groove thin rgb(255, 255, 0);
}
```

使用 `border-radius` 属性用于向元素添加圆角边框：

```css
p {
  border: 2px solid red;
  border-radius: 5px;
}
```



- 阴影

box-shadow 属性让盒在显示时产生阴影效果，其使用和文字阴影一致

- box-sizing

使用box-sizing属性，可以指定用width属性与height属性指定的宽度值与高度值是否包含元素的填充区域（padding）与边框（border）的宽度与高度，从而实现更为精确的定位。

1. content-box，这是默认值，width 与 height 只包括内容的宽和高， 不包括边框（border），内边距（padding），外边距（margin）
2. border-box，width 和 height 属性包括内容，内边距和边框，但不包括外边距

```css
div {
  width: 160px;
  height: 80px;
  padding: 20px;
  border: 8px solid red;
  background: yellow;
}

.content-box {
  box-sizing: content-box;
  /* Total width: 160px + (2 * 20px) + (2 * 8px) = 216px
     Total height: 80px + (2 * 20px) + (2 * 8px) = 136px
     Content box width: 160px
     Content box height: 80px */
}

.border-box {
  box-sizing: border-box;
  /* Total width: 160px
     Total height: 80px
     Content box width: 160px - (2 * 20px) - (2 * 8px) = 104px
     Content box height: 80px - (2 * 20px) - (2 * 8px) = 24px */
}
```

- 背景

我们可以为我们的盒子填充背景，使得更加美观，其背景有如下几种属性

1. `background-color` 属性指定元素的背景色。

```css
body {
  background-color: lightblue;
}
```

2. `background-image` 属性指定用作元素背景的图像。默认情况下，图像会重复，以覆盖整个元素。

```css
body {
  background-image: url("paper.gif");
}
```

3. `background-repeat` 用于定义背景图片的重复，默认情况下，`background-image` 属性在水平和垂直方向上都重复图像。

```css
   body {
     background-image: url("gradient_bg.png");
     background-repeat: repeat-x;/* 水平重复 */
   }
   body {
     background-image: url("tree.png");
     background-repeat: repeat-y; /* 垂直重复 */
   }
   body {
     background-image: url("tree.png");
     background-repeat: no-repeat; /* 无重复 */
   }
```

4. `background-position` 属性用于指定背景图像的位置。

```css
body {
  background-image: url("tree.png");
  background-repeat: no-repeat;
  background-position: right top; /* 右上角 */
}
```

5. `background-attachment` 属性用于指定背景图像的位置。

```css
body {
  background-attachment: fixed; /* 固定背景图像 */
}
body {
  background-attachment: scroll; /* 背景图像应随页面的其余部分一起滚动 */
}
```

6. `background-size` 属性用于规定背景图像的尺寸

| 值           | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| *length*     | 设置背景图像的高度和宽度。第一个值设置宽度，第二个值设置高度。如果只设置一个值，则第二个值会被设置为 "auto"。 |
| *percentage* | 以父元素的百分比来设置背景图像的宽度和高度。第一个值设置宽度，第二个值设置高度。如果只设置一个值，则第二个值会被设置为 "auto"。 |
| cover        | 把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。背景图像的某些部分也许无法显示在背景定位区域中。 |
| contain      | 把图像图像扩展至最大尺寸，以使其宽度和高度完全适应内容区域。 |

7. `background-clip` 属性规定背景的绘制区域。

| 值          | 描述                   |
| :---------- | :--------------------- |
| border-box  | 背景被裁剪到边框盒。   |
| padding-box | 背景被裁剪到内边距框。 |
| content-box | 背景被裁剪到内容框。   |

## FLEX布局

Flex是[Flexible](https://so.csdn.net/so/search?q=Flexible&spm=1001.2101.3001.7020) Box的缩写 flex布局表示弹性布局，可以为盒状模型提供最大的灵活性，你可以设置 flex 属性来创建弹性布局

```css
.wrap{
    display : flex;
}
```

常用属性设置在容器上，分别为：

- flex-direction 

flex-direction 属性设置容器主轴的方向，row: 默认值，表示沿水平方向，由左到右。row-reverse ：表示沿水平方向，由右到，column：表示垂直方向，由上到下，column-reverse:表示垂直方向，由下到上

```css
.wrap{
    flex-wrap:nowrap | wrap | wrap-reverse;
}
```

- flex-wrap

flex-wrap属性用于设置当项目在容器中一行无法显示的时候如何处理。nowrap：表示不换行，wrap：正常换行，第一个位于第一行的第一个，wrap-reverse：向上换行，第一行位于下方

```css
.wrap{
    flex-wrap:nowrap | wrap | wrap-reverse;
}
```

- flew-flow

flex-flow属性是flex-deriction和flex-wrap属性的简写

- justify-content，space-between：两端对齐

justify-content属性用于设置项目在容器中的对齐方式。flex-start：默认值，左对齐，flex-end：右对齐，center：居中对齐，space-between：两端对齐，space-around：每个项目两侧的间距相等

```css
.wrap{
    justify-content: flex-start | flex-end | center |space-between | space-around
}
```

- align-items

align-items定义了项目在交叉轴上是如何对齐显示的，flex-start:交叉轴的起点对齐，flex-end 交叉轴的终点对齐，center 交叉轴居中对齐，baseline 项目的第一行文字的基线对齐，stretch：默认值：如果项目未设置高度或者高度为auto，将占满整个容器的高度

```css
.wrap{
    align-items:flex-start | flex-end | center | baseline | stretch
}
```

- align-content

align-content属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。flex-start：与交叉轴的起点对齐。flex-end：与交叉轴的终点对齐。center：与交叉轴的中点对齐。space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。stretch（默认值）：轴线占满整个交叉轴。

```css
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

- order 

order属性设置项目排序的位置，默认值为0，数值越小越靠前

- flex-grow

flex-group属性用来控制当前项目是否放大显示。默认值为0，表示即使容器有剩余空间也不放大显示。如果设置为1，则平均分摊后放大显示。

- flex-shrink

flex-shrink属性表示元素的缩小比例。默认值为1，如果空间不够用时所有的项目同比缩小。如果一个项目的该属性设置为0，则空间不足时该项目也不缩小。

- flex-basis

flex-basis属性表示表示项目占据主轴空间的值。默认为auto，表示项目当前默认的大小。如果设置为一个固定的值，则该项目在容器中占据固定的大小。

- flex

flex属性是 flex-grow属性、flex-shrink属性、flex-basis属性的简写。默认值为：0 1 auto

```css
.item{
    flex:(0 1 auto) | auto(1 1 auto) | none (0 0 auto)
}
```

- align-self 

align-self属性表示当前项目可以和其他项目拥有不一样的对齐方式。auto 表示和父元素一致，其他属性和 align-items 一致

```css
.item{
    align-self: flex-start | flex-end | center | baseline | stretch 
}
```



  ## GRID布局

在代码中我们只要把某个元素的 display 属性设置为 grid或者 inline-grid，那么这个元素就会成为 Grid 父容器 ，它下面直接的子元素将成为 Grid子元素 。网格布局从名字上应该就能理解大致的样子，由行和列组成的表格，就和Excel差不多。网格布局中由一条条水平和竖直的的网格线围成一个个格子被称为单元格 。

![img](https://www.wangbase.com/blogimg/asset/201903/1_bg2019032502.png)

- grid-template-columns 和 grid-template-rows

`grid-template-columns` 用于定义每一列的宽度； `grid-template-rows` 用于定义每一行的高度。

重复写同样的值非常麻烦，尤其网格很多时。这时，可以使用`repeat()`函数，简化重复的值。`repeat()`接受两个参数，第一个参数是重复的次数，第二个参数是所要重复的值。

有时，单元格的大小是固定的，但是容器的大小不确定。如果希望每一行（或每一列）容纳尽可能多的单元格，这时可以使用`auto-fill`关键字表示自动填充。

为了方便表示比例关系，网格布局提供了`fr`关键字（fraction 的缩写，意为"片段"）。如果两列的宽度分别为`1fr`和`2fr`，就表示后者是前者的两倍。

`minmax()`函数产生一个长度范围，表示长度就在这个范围之中。它接受两个参数，分别为最小值和最大值。

`auto`关键字表示由浏览器自己决定长度。

`grid-template-columns`属性和`grid-template-rows`属性里面，还可以使用方括号，指定每一根网格线的名字，方便以后的引用。

```css
.container {
  display: grid;
  grid-template-columns: 100px 100px;
  grid-template-rows:repeat(3, 100px);
}
.container {
  display: grid;
  grid-template-columns: repeat(3, 33.33%);
  grid-template-rows: repeat(auto-fill, 100px);
}

.container {
  display: grid;
  grid-template-columns: 1fr 2fr;
}
.container {
  display: grid;
  grid-template-columns: repeat(3,minmax(100px, 1fr));
}
.container {
  display: grid;
  grid-template-columns: auto;
  grid-template-rows: [c1] 100px [c2] 100px [c3] auto [c4];
}

```

- grid-gap 

`grid-row-gap`属性设置行与行的间隔（行间距），`grid-column-gap`属性设置列与列的间隔（列间距）。`grid-gap`属性是`grid-column-gap`和`grid-row-gap`的合并简写形式

```css
.container {
  grid-gap: 20px 20px;
}
```

- grid-template-areas

网格布局允许指定"区域"（area），一个区域由单个或多个单元格组成。`grid-template-areas`属性用于定义区域

```css
.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-template-areas: 'a b c'
                       'd e f'
                       'g h i';
}
```

如果某些区域不需要利用，则使用"点"（`.`）表示。

```css
grid-template-areas: 'a . c'
                     'd . f'
                     'g . i';
```

- grid-auto-flow

划分网格以后，容器的子元素会按照顺序，自动放置在每一个网格。默认的放置顺序是"先行后列"，即先填满第一行，再开始放入第二行，即下图数字的顺序。这个顺序由`grid-auto-flow`属性决定，默认值是`row`，即"先行后列"。也可以将它设成`column`，变成"先列后行"。`row dense`，表示"先行后列"，并且尽可能紧密填满，尽量不出现空格。`column dense`，表示"先列后行"，并且尽量填满空格。

- 对齐

`justify-items`属性设置单元格内容的水平位置（左中右），`align-items`属性设置单元格内容的垂直位置（上中下）。`place-items`属性是`align-items`属性和`justify-items`属性的合并简写形式

```css
.container {
  justify-items: start | end | center | stretch;
  align-items: start | end | center | stretch;
}
start：对齐单元格的起始边缘。
end：对齐单元格的结束边缘。
center：单元格内部居中。
stretch：拉伸，占满单元格的整个宽度（默认值）。
```

`justify-content`属性是整个内容区域在容器里面的水平位置（左中右），`align-content`属性是整个内容区域的垂直位置（上中下）。`place-content`属性是`align-content`属性和`justify-content`属性的合并简写形式。

```css
.container {
  justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
  align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}
start - 对齐容器的起始边框。
end - 对齐容器的结束边框。
center - 容器内部居中。
stretch - 项目大小没有指定时，拉伸占据整个网格容器。
space-around - 每个项目两侧的间隔相等。所以，项目之间的间隔比项目与容器边框的间隔大一倍。
space-between - 项目与项目的间隔相等，项目与容器边框之间没有间隔。
space-evenly - 项目与项目的间隔相等，项目与容器边框之间也是同样长度的间隔
```

- grid-auto-columns 和 grid-auto-rows 

有时候，一些项目的指定位置，在现有网格的外部。比如网格只有3列，但是某一个项目指定在第5行。这时，浏览器会自动生成多余的网格，以便放置项目。

`grid-auto-columns`属性和`grid-auto-rows`属性用来设置，浏览器自动创建的多余网格的列宽和行高。它们的写法与`grid-template-columns`和`grid-template-rows`完全相同。如果不指定这两个属性，浏览器完全根据单元格内容的大小，决定新增网格的列宽和行高。

- 项目的位置

网格布局内部的位置是可以指定的，具体方法就是指定项目的四个边框，分别定位在哪根网格线。

```css
.item-1 {
  grid-column-start: 2;
  grid-column-end: 4;
}
```

这四个属性的值还可以使用`span`关键字，表示"跨越"，即左右边框（上下边框）之间跨越多少个网格。

```css
.item-1 {
  grid-column-start: span 2;
}
```

`grid-column`属性是`grid-column-start`和`grid-column-end`的合并简写形式，`grid-row`属性是`grid-row-start`属性和`grid-row-end`的合并简写形式。

```css
.item-1 {
  grid-column: 1 / 3;
  grid-row: 1 / 2;
}
/* 等同于 */
.item-1 {
  grid-column-start: 1;
  grid-column-end: 3;
  grid-row-start: 1;
  grid-row-end: 2;
}
```

`grid-area`属性指定项目放在哪一个区域

```css
.item-1 {
  grid-area: e;
}
```

`grid-area`属性还可用作`grid-row-start`、`grid-column-start`、`grid-row-end`、`grid-column-end`的合并简写形式

- 项目对齐

`justify-self`属性设置单元格内容的水平位置（左中右），跟`justify-items`属性的用法完全一致，但只作用于单个项目。

`align-self`属性设置单元格内容的垂直位置（上中下），跟`align-items`属性的用法完全一致，也是只作用于单个项目。`place-self`属性是`align-self`属性和`justify-self`属性的合并简写形式。

## FLOAT

CSS中的float属性是一个频繁用到的属性：浮动，设置了float属性的元素会根据属性值向左或向右浮动，我们称设置了float属性的元素为浮动元素。浮动元素会从普通文档流中脱离，但浮动元素影响的不仅是自己，它会影响周围的元素对齐进行环绕 , 浮动对前面的html元素没影响，对后面的html元素有影响。

float有四个可用的属性值：

- left：元素向左浮动

- right：元素向右浮动

- none：默认值。元素不浮动，并会显示在其在文本中出现的位置。

- inherit： 规定应该从父元素继承 float 属性的值。

如下的例子：span元素周围的文字会围绕着span元素，而设置了float属性的span元素变成了一个块级元素的感觉，可以设置width和height属性。

```html
<div class="box">
        <span class="float-ele">
            浮动元素
        </span>
        普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流普通文档流
</div>
<style>
    .box { background: #00ff90; padding: 10px; width: 500px; }
	.float-ele { float: left; margin: 10px; padding: 10px; background: #ff6a00; width: 100px; text-align: center; }
</style>

```

浮动元素的包含块就是离浮动元素最近的块级祖先元素，前面叙述的例子中，div.box就是span元素的包含块。不管一个元素是行内元素还是块级元素，如果被设置了浮动，那浮动元素会生成一个块级框，可以设置它的width和height

- 浮动元素在浮动的时候，其margin不会超过包含块的padding
- 如果有多个浮动元素，后面的浮动元素的margin不会超过前面浮动元素的margin
- 如果两个元素一个向左浮动，一个向右浮动，左浮动元素的marginRight不会和右浮动元素的marginLeft相邻。
- 浮动元素顶端不会超过包含块的内边界底端，如果有多个浮动元素，下一个浮动元素的顶端不会超过上一个浮动元素的底端
  这条规则简单说就是如果有多个浮动元素，后面的元素高度不会超过前面的元素，并且不会超过包含块。
- 如果有非浮动元素和浮动元素同时存在，并且非浮动元素在前，则浮动元素不会不会高于非浮动元素
- 浮动元素会尽可能地向顶端对齐、向左或向右对齐

有的时候，我们不希望一些元素会被旁边的浮动元素影响到，这个时候就需要用到clear属性了。clear属性：确保当前元素的左右两侧不会有浮动元素。clear的设置必须是针对某一盒子之外的浮动设置，但如果本盒子也设置了浮动则不会对本盒子消除。

- clear:left;      消除左浮动 
- clear:right;	 消除右浮动 
- clear:both;		 消除全部浮动



## POSITION

position的含义是指定位类型，对于一个元素，我们可以设置它的 left、top、right、bottom 来改变它的定位，这些属性可以让元素相对其定位原始点的左，上，右，下偏移一定的距离：

- position: static

static(没有定位)是position的默认值，元素处于正常的文档流中，会忽略left、top、right、bottom和z-index属性。

- position: relative

relative(相对定位)是指给元素设置相对于原本位置的定位，元素并不脱离文档流，因此元素原本的位置会被保留，其他的元素位置不会受到影响。

- position: absolute

absolute(绝对定位)是指给元素设置绝对的定位，相对定位的对象可以分为两种情况：

1) 设置了absolute的元素如果存在有祖先元素设置了position属性为relative或者absolute，则这时元素的定位对象为此已设置position属性的祖先元素。

2) 如果并没有设置了position属性的祖先元素，则此时相对于body进行定位。

- position: fixed

可以简单说fixed是特殊版的absolute，fixed元素总是相对于body定位的。

- position: sticky

设置了sticky的元素，在屏幕范围（viewport）时该元素的位置并不受到定位影响，当该元素的位置将要移出偏移范围时，定位又会变成fixed，根据设置的left、top等属性成固定位置的效果。

 	

## 参考文档

https://blog.csdn.net/qq_51999757/article/details/120255410

http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html

https://www.runoob.com/cssref/pr-class-display.html

https://www.w3school.com.cn/index.html
