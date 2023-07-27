# class8 - Next.js项目开发技巧

这节课分享一些在开发过程种使用的开发技巧，来源是字节跳动青训营的直播课

## CSS多媒体适配

媒体查询（Media Query）是CSS3新语法。使用@media查询，可以针对不同的媒体类型定义不同的样式，他的语法是：

```css
@media not|only mediatype and (mediafeature and|or|not mediafeature) {
  CSS-Code;
}
```

其中 mediatype 是指媒体设备，默认的 all 类型，指的是全部设备，常用的还有：

- screen 除打印设备和屏幕阅读设备以外的所有设备
- speech 能够“读出”页面的屏幕阅读设备，通常供残疾人士使用
- print 在打印和打印预览的时候生效

mediafeature 是指媒体功能，他表示这条 css 在什么情况下才会触发，常用的有：

| 值                      | 描述                                                         |
| :---------------------- | :----------------------------------------------------------- |
| aspect-ratio            | 定义输出设备中的页面可见区域宽度与高度的比率                 |
| color                   | 定义输出设备每一组彩色原件的个数。如果不是彩色设备，则值等于0 |
| color-index             | 定义在输出设备的彩色查询表中的条目数。如果没有使用彩色查询表，则值等于0 |
| device-aspect-ratio     | 定义输出设备的屏幕可见宽度与高度的比率。                     |
| device-height           | 定义输出设备的屏幕可见高度。                                 |
| device-width            | 定义输出设备的屏幕可见宽度。                                 |
| grid                    | 用来查询输出设备是否使用栅格或点阵。                         |
| height                  | 定义输出设备中的页面可见区域高度。                           |
| max-aspect-ratio        | 定义输出设备的屏幕可见宽度与高度的最大比率。                 |
| max-color               | 定义输出设备每一组彩色原件的最大个数。                       |
| max-color-index         | 定义在输出设备的彩色查询表中的最大条目数。                   |
| max-device-aspect-ratio | 定义输出设备的屏幕可见宽度与高度的最大比率。                 |
| max-device-height       | 定义输出设备的屏幕可见的最大高度。                           |
| max-device-width        | 定义输出设备的屏幕最大可见宽度。                             |
| max-height              | 定义输出设备中的页面最大可见区域高度。                       |
| max-monochrome          | 定义在一个单色框架缓冲区中每像素包含的最大单色原件个数。     |
| max-resolution          | 定义设备的最大分辨率。                                       |
| max-width               | 定义输出设备中的页面最大可见区域宽度。                       |
| min-aspect-ratio        | 定义输出设备中的页面可见区域宽度与高度的最小比率。           |
| min-color               | 定义输出设备每一组彩色原件的最小个数。                       |
| min-color-index         | 定义在输出设备的彩色查询表中的最小条目数。                   |
| min-device-aspect-ratio | 定义输出设备的屏幕可见宽度与高度的最小比率。                 |
| min-device-width        | 定义输出设备的屏幕最小可见宽度。                             |
| min-device-height       | 定义输出设备的屏幕的最小可见高度。                           |
| min-height              | 定义输出设备中的页面最小可见区域高度。                       |
| min-monochrome          | 定义在一个单色框架缓冲区中每像素包含的最小单色原件个数       |
| min-resolution          | 定义设备的最小分辨率。                                       |
| min-width               | 定义输出设备中的页面最小可见区域宽度。                       |
| monochrome              | 定义在一个单色框架缓冲区中每像素包含的单色原件个数。如果不是单色设备，则值等于0 |
| orientation             | 定义输出设备中的页面可见区域高度是否大于或等于宽度。         |
| resolution              | 定义设备的分辨率。如：96dpi, 300dpi, 118dpcm                 |
| scan                    | 定义电视类设备的扫描工序。                                   |
| width                   | 定义输出设备中的页面可见区域宽度。                           |

我们可以通过 not, and, or 和 only 构造复杂的媒体查询：

- **not**: not 运算符用于否定媒体查询，如果不满足这个条件则返回 true，否则返回 false
- **only**: only 运算符仅在整个查询匹配时才用于应用样式，并且对于防止较早的浏览器应用所选样式很有用
- **,**(逗号) 逗号用于将多个媒体查询合并为一个规则。 逗号分隔列表中的每个查询都与其他查询分开处理。列表的行为类似于逻辑或 or 运算符。
- **and**: and 操作符用于将多个媒体查询规则组合成单条媒体查询，当每个查询规则都为真时则该条媒体查询为真，它还用于将媒体功能与媒体类型结合在一起。
- **or**: or 仅仅用于多个 mediafeature  之间，表示多个联合的 mediafeature 只要有一个满足就可以触发

我们用的最多的就是根据当前的屏幕大小来设定我们的样式，一个例子如下,我们在屏幕的最大宽度是 500 400 300px 的时候 ，让main 的样式发生变化，来达到适配的目的

```css
@media only screen and (max-width: 500px) {
    .main {
        width: 100px;
    }
}
@media only screen and (max-width: 400px) {
    .main {
        width: 80px;
    }
}
@media only screen and (max-width: 300px) {
    .main {
        width: 60px;
    }
}
```



## JS 适配

但是有时候我们的 css 并不能帮助我们处理一些复杂场景，比如说，当你屏幕尺寸到达一定程度的时候，你需要更换整个内容的布局和展示形式，这个时候我们就需要使用 js 来进行我们的适配，以下是一个 react 的例子，我们在全局的 resize 事件上绑定一个方法，并且提供一个Context 将我们的全局信息分发到其他页面中，当我们的屏幕大小到达一定的尺寸时，更新当前环境信息的变量，其他的组件得到信息变化后将会自动适配，不懂 Context 是什么的可以去看我之前的笔记 react与响应式 这一节

```javascript
export const UserAgentProvider = ({ children }: IProps): JSX.Element => {
  const [userAgent, setUserAgent] = useState<Environment>(Environment.none); // 服务器渲染初始化渲染未必是预期效果，none缓冲切换视觉)

  // 监听本地缓存来同步不同页面间的主题（当前页面无法监听到，直接在顶部栏进行了类的切换)
  useEffect(() => {
    const checkUserAgent = (): void => {
      const width = document.body.offsetWidth;
      // 用宽度去判断，是为了适配不改机型，仅拉扯屏幕宽度的情况
      if (width < 768) {
        // 手机端
        setUserAgent(Environment.mobile);
      } else if (width >= 768 && width < 1200) {
        // ipad端
        setUserAgent(Environment.ipad);
      } else if (width >= 1200) {
        // pc端
        setUserAgent(Environment.pc);
      } else {
        setUserAgent(Environment.none); // 增加none类型来缓冲默认类型样式切换时的视觉突变
      }
    };
    checkUserAgent();
    window.addEventListener('resize', checkUserAgent); // 监听屏幕宽度变化，及时适配当前页面样式
    return (): void => {
      window.removeEventListener('resize', checkUserAgent);
    };
  }, [typeof document !== 'undefined' && document.body.offsetWidth]);

  return <UserAgentContext.Provider value={{ userAgent }}>{children}</UserAgentContext.Provider>;
};
```



## 大图优化

WebP（发音 weppy)，是一种支持有损压缩和无损压缩的图片文件格式，派生自图像编码格式 VP8。根据 Google 的测试，无损压缩后的 WebP 比 PNG 文件少了 45％ 的文件大小，即使这些 PNG 文件经过其他压缩工具压缩之后，WebP 还是可以减少 28％ 的文件大小。

但是他也有部分的缺点就是不是所有的浏览器都支持 webp 所以我们需要想明确我们项目的运行环境再决定我们使用的技术

## strapi

Strapi是一种灵活的、开放源码的无头CMS，开发者可以自由选择自己喜欢的工具和框架，编辑器也可以轻松地管理和分发内容。

简而言之就是它可以根据你的配置来自动生成一个管理平台，不需要你手动去编写逻辑，它的官方文档如下：

https://getstrapi.cn/developer-docs/latest/getting-started/introduction.html

使用它也很简单，首先使用脚手架来生成一个项目，配置基本信息，比如数据库等等，然后运行即可

```shell
npx create-strapi-app (项目名字)
npm run develop
```

运行后打开页面 localhost:1337/admin 我们可以创建自己的产品，配置它的字段，我们就可以直接拥有一个可以进行增删改查的管理平台，同时他也将开放端口提供我们外部调用。具体教程可以看官方文档，网上教程也很多

## 多媒体格式转换

我们经常遇到这样的情况就是：我们希望用户使用 markdown 编译器快捷方便的写一篇文章，然后我们再使用我们设定的样式来展示他，我们可以使用一个 showdown 的库来实现，我们只需要下载他，然后这样使用它，就可以把我们保存的 markdown 信息转换成 html，同时我们可以根据生成的 html 标签来配置我们的样式：

```javascript
const Article: NextPage<IArticleProps> = ({ content }) => {
  const converter = new showdown.Converter();
  return (
    <div className={styles.article}>
      <div dangerouslySetInnerHTML={{ __html: converter.makeHtml(content) }} />
    </div>
  );
};
```

## 主题切换

主题切换是一个老生常谈的问题了，很多网页现在都支持白天模式和黑夜模式，处理方法是我们提供一个 Context ，传递一个保存着当前状态的变量，以及一个改变状态的方法，当状态改变的时候，我们改变绑定在 html 根标签上的一个属性，而 css 中我们可以根据html不同的属性的选择器来切换我们的样式；同时我们需要把我们的信息存储在 localStorage 里来记住用户的选择，这样用户下一次访问的时候就可以保持它的选择：

```javascript
export const ThemeContextProvider = ({ children }: IProps): JSX.Element => {
  const [theme, setTheme] = useState<Themes>(Themes.light);

  // 监听本地缓存来同步不同页面间的主题
  useEffect(() => {
    const checkTheme = (): void => {
      const item = (localStorage.getItem('theme') as Themes) || Themes.light;
      setTheme(item);
      document.getElementsByTagName('html')[0].dataset.theme = item;
    };
    checkTheme();
    window.addEventListener('storage', checkTheme);
    return (): void => {
      window.removeEventListener('storage', checkTheme);
    };
  }, []);

  return (
    <ThemeContext.Provider
      value={{
        theme,
        setTheme: (currentTheme): void => {
          setTheme(currentTheme);
          localStorage.setItem('theme', currentTheme);
          document.getElementsByTagName('html')[0].dataset.theme = currentTheme;
        },
      }}
    >
      {children}
    </ThemeContext.Provider>
  );
};
```

