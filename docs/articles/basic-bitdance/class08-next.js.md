# class8 - Next.js SSR项目

这节课将讲述为什么要使用 ssr 渲染，以及如何使用 next.js 进行 ssr 项目的开发和部署

先给出 next.js 的官网，本教程只是进行简单的学习笔记的记录，更详细的开发需要你自行深入学习 

https://www.nextjs.cn/docs/getting-started

## SSR CSR SSG

- CSR 

CSR 是指客户端渲染，它是流程是：用户输入地址，客户端向服务器发送请求，服务器将页面的相关代码原封不动的传递给浏览器，浏览器解析文件，请求ajax向服务器再次请求一些数据，浏览器拿到ajax请求返回的数据后，将数据渲染在页面上，客户端必须等到所有 Javascript 代码加载后才能呈现页面并向用户显示内容。

- SSG

SSG 静态站点生成是指：当你创建一个站点时，我们需要预先提供应用程序的所有内容和数据。使用 SSG 意味着我们已经在构建步骤中生成了完整的 HTML 文件在，之后将页面放在 CDN 中，当用户打开你的站点后，直接返回给用户已经生成的页面。

- SSR

SSR 服务端渲染是指：服务端第一次只把渲染好的 HTML 发给客户端，这样客户端就能直接显示出来网页的样式，首次绘制就很快，然后，客户端再根据 HTML 需要的 js 向服务端请求 js，然后客户端再把 js 装到已经绘制好的 HTML 上

## 为什么要使用服务端渲染

尽管像 React 这样的现代基于 Javascript 的前端框架帮助我们开发了强大的动态网站，但它们有一个主要缺点：客户端必须等到所有 Javascript 代码加载后才能呈现页面并向用户显示内容。由于页面的加载速度会显着影响其用户体验和搜索引擎排名，这已成为 Web 开发中的主要问题。Next.js 框架提供服务器端渲染，无需任何配置。服务器渲染的 HTML 页面将被发送到客户端，让他们立即看到页面的主要内容。之后事件处理程序等可以附加到生成好的 HTML 上，使其具有交互性。

## SSR的实现

SSR 实现的关键就是：同构，一份代码，先通过服务端渲染(server-side rendering，ssr)，生成html字符串以及初始化数据，客户端拿到后，通过对html的dom进行patch和事件绑定对dom进行客户端激活(client-side hydration，csh)，这个整体的过程叫同构渲染。服务端将 HTML 片段提炼出来的过程就称为脱水，相应的，注水是在客户端要做的事，把只有样子的 HTML 恢复其功能。

## Next 预渲染模式

Next.js 最突出的特性是预渲染，接收到 Javascript 代码后在客户端渲染网页是一个缓慢的过程。Next 通过向客户端发送每个页面的预渲染版本来解决这个问题。next有两种预渲染的方式：静态生成和服务端渲染，他们之间的区别就是什么时候生成html页面。

- 静态生成，是在 build 应用的时候生成html, 然后每次请求的时候重用静态生成的页面

  当我们可以在用户请求一个页面之前，就知道页面具体的样子，比如一篇博客，一份文档，我们就尽可能使用静态生成 ，这样你的所有 page（页面）都可以只构建一次并托管到 CDN 上，这比让服务器根据每个页面请求来渲染页面快得多。

- 服务端渲染，就是每次请求的时候都去生成html页面。

  假设你的某个页面需要预渲染频繁更新的数据，比如一个实时监控界面，一份可以和用户交互的表单信息等等，你就需要使用服务端渲染，即SSR渲染

在开发模式下（即npm run dev），每次请求的时候都会重新渲染页面，不管页面是不是静态生成的。

## 初始化项目

为了初始化一个项目，我们需要配置一个 package.json 文件初始化一个项目，然后下载依赖，你也可以直接使用官方初始化完成的 demo：

```shell
npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn/tree/master/basics/learn-starter
```

我们可以看到官方项目中有一个pages 文件夹，一个public 文件夹，一个styles 文件夹以及一些配置文件，如果是你自己创建的项目你需要自己创建一些项目，下面我们来讲每个文件夹的作用：

- public 文件夹

这个文件夹存放了你需要引入的静态资源，比如图片 logo 等，等你在项目中使用这些资源时，你可以直接从 public 文件夹开始寻找这些资源，例如官方 demo 中的，vercel.svg 这个文件就在 public文件夹中

```html
<img src="/vercel.svg" alt="Vercel"/>
```

- pages 文件夹

这个文件夹存放我们需要的页面，并且根据这个文件夹的逻辑配置我们的路由，而 index.js 文件可以作为某个路由的根进行配置，例如：pages/index.js 就需要你只需要直接访问 localhost:3000 就可以访问，而 pages/test/new.js 文件你需要使用 localhost:3000/test/new进行访问

- styles 文件夹

存放我们需要的样式，这部分将在后续进行讲解

## 配置页面

在 Next.js 中，一个 page（页面） 就是一个从 .js、jsx、.ts 或 .tsx 文件导出（export）的 React 组件 ，这些文件存放在 pages 目录下。每个 page（页面）都使用其文件名作为路由（route），比如我们可以看到在官方的 demo 中，我们的 index.js 就导出了一个 react 组件：

```javascript
export default function Home() {
  return (
    <div className={styles.container}>
      <Head>
        <title>Create Next App</title>
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <main>
         //省略
      </main>

      <style jsx>{`
      		//省略
      `}</style>
      <style jsx global>{`
        	//省略
      `}</style>
    </div>
  )
}

```

任何你想配置一个页面是头部内容，例如它的标题，你可以使用 next 提供的 head 组件来控制它：

```javascript
import Head from 'next/head';
export default function Home() {
  return (
      <Head>
        <title>Create Next App</title>
        <link rel="icon" href="/favicon.ico" />
      </Head>
  )
}
```

你可以在其他位置编写一个 react 组件然后再页面中使用他来降低一共页面的整体代码量以及良好的复用，比如我们创建 ccomponents / layout.js 文件编写一个组件：

```javascript
export default function Layout({ children }) {
    return <div>哈哈哈哈哈哈哈哈哈哈哈{children}</div>
}
```

然后我们可以在 index.js 里引入它：

```javascript
import Layout from '../components/layout'

export default function Home() {
  return (
    <Layout>
      <Head>
        <title>Create Next App</title>
        <link rel="icon" href="/favicon.ico" />
      </Head>
     //省略
    </Layout>
  )
}
```

## 配置样式

我们可以用多种方式来配置我们组件的样式，首先我们要知道的是， next.js 13 内置了css 和 scss 的模块，我们可以直接编写，不需要任何插件和依赖的下载，根据官方的例子，我们可以使用 style in jsx 的方式添加样式，也就是用如下的内容包裹样式文件：

```jsx
<style jsx>{`
   main {
          padding: 5rem 0;
        }
`}</style>
```

我们也可以选择使用 css.modules 来添加样式，用这种方法添加的样式在编译的时候会加上一个哈希后缀，这种方法可以有效避免类名重复的问题：

```css
.container {
  max-width: 36rem;
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}
```

引入

```javascript
import styles from './layout.module.css'

export default function Layout({ children }) {
  return <div className={styles.container}>{children}</div>
}
```

你可以使用 cn 模块动态配置你的类名是否生效，就想这样：

```javascript
import styles from './alert.module.css'
import cn from 'classnames'

export default function Alert({ children, type }) {
  return (
    <div
      className={cn({
        [styles.success]: type === 'success',
        [styles.error]: type === 'error'
      })}
    >
      {children}
    </div>
  )
}
```

如果你想要一套样式在所有页面生效，你需要配置全局样式，你有两者方法进行配置，你可以在 pages 文件夹下方创建一个 _app.js 文件，如下引入你写好的样式：

```javascript
import '../styles/global.css'

export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

你也可以选择使用全局的 style jsx 来进行全局样式的编写，就想例子里一样：

```javascript
<style jsx global>{`
        html,
        body {
          padding: 0;
          margin: 0;
          font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto,
            Oxygen, Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue,
            sans-serif;
        }
        * {
          box-sizing: border-box;
        }
`}</style>
```

## 设置路由

前文已经说过了，我们可以通过 pages 文件夹的目录来配置我们的路由，pages/test/new.js 文件你需要使用 localhost:3000/test/new 进行访问，我们可以在页面中使用 link 组件实现页面的跳转，这个组件将直接将路由变成 href 指向的位置

```html
import Link from 'next/link'

<div><Link href="/test/new">去测试页面</Link></div>
```

你也可以使用 Router 组件进行路由的跳转，它在 js 里实现

```html
import { useRouter } from 'next/router'
const router = useRouter()

<button onClick={()=>{router.push('/test/new')}}>去测试页面</button>
```

你可以在跳转的路由中设置路径参数，目前只支持 /test/new?id=1 这样形式的路径参数，你可以使用 router 来获取你需要的路径参数

```javascript
//传递参数
<Link href={{pathname:'/test/new',query:{id:1}}><a>博客详情</a></Link>
router.push({
     pathname:"/test/new",
     query:{
         id:1
     }
})

//获取参数
router.query.id
```

当然除了使用路径参数的方式以外，next 也支持我们使用动态路由，如果你需要某一级的路由是动态的，你需要用 [ ] 把它包裹起来，比如，page/[id]/[pid].js，其中的 [id] 和 [pid] 两级都是动态路由，你可以使用任何形式匹配它，比如 localhost:3000/123/000 这样的形式，动态路由的参数将被处理为路径参数一起绑定在query 上，我们还是可以用 router 模块来获取它：

```javascript
import { useRouter } from 'next/router'

const Post = () => {
  const router = useRouter()
  const { pid } = router.query
  const { id } = router.query

  return <p>{id}: {pid}</p>
}

export default Post
//显示123: 000
```

你可以使用 [...slug] 这样的命名法，那么这个参数将会捕获所有满足的路由，即使它有下一级路由，举个简单的例子：当你使用 pages/test/[...slug].js 来命名时

```javascript
// pages/test/a
{ "slug": ["a"] }
// pages/test/a/b
{ "slug": ["a", "b"] }
```



此外我们的路由模块还可以绑定处理不同阶段的事件，这里简单介绍，更多信息可以查阅官网文档：

https://www.nextjs.cn/docs/routing/introduction

```javascript
//路由发生变化时
Router.events.on('routeChangeStart',(...args)=>{
})

//路由结束变化时
Router.events.on('routeChangeComplete',(...args)=>{
})

//浏览器 history触发前
Router.events.on('beforeHistoryChange',(...args)=>{
})

//路由跳转发生错误时
Router.events.on('routeChangeError',(...args)=>{
})
```

## 页面数据获取

在静态生成预渲染阶段，我们的页面组件需要使用到外部数据接收参数，通常使用到Next.js提供的函数，有三种常用的函数：

1. getStaticProps（静态生成）：在构建时获取数据。
2. getStaticPaths（静态生成）：指定动态路由以根据数据预渲染页面。
3. getServerSideProps（服务器端渲染）：获取每个请求的数据。

**两种预渲染的区别请查看 Next 预渲染这节的内容**

- getStaticProps

如果从页面export 名为 getStaticProps 的异步函数，Next.js 将在构建时使用 getStaticProps 返回的props预渲染此页面。你可以在 getStaticProps  逻辑里获取需要的数据，再将数据传递给页面，因为你必须先获取数据再渲染界面，所以你的方法需要用 async  包裹：

```javascript
// 根据传入的数据渲染界面
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// 此函数在构建时被调用
export async function getStaticProps() {
  // 假设这是一个api 可以获取一些 json数据
  const res = await fetch('https://...')
  const posts = await res.json()

  // 通过返回 { props: { posts } } 对象，Blog 组件
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```

如果你希望在动态路由中使用上述的方案，那么你需要使用 getStaticPaths ，因为构建动态路由所对应的内容时可能需要从外部获取数据，但是 next 并不知道那些动态路由页面需要渲染，getStaticPaths 函数在构建时被调用，并允许你指定要预渲染的路径。

```javascript
// 此函数在构建时被调用
export async function getStaticPaths() {
  // 假设这是一个api ，可以获取博文列表
  const res = await fetch('https://...')
  const posts = await res.json()

  // 据博文列表生成所有需要预渲染的路径
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))
  return { paths, fallback: false }
}

// 在构建时也会被调用
export async function getStaticProps({ params }) {
  // params 包含此片博文的 `id` 信息。假设我们的 api 根据id返回数据，就可以这样传入了
  const res = await fetch(`https://.....?=${params.id}`)
  const post = await res.json()

  // 通过 props 参数向页面传递博文的数据
  return { props: { post } }
}
```

对于需要服务端渲染的界面，你可以编写 `getServerSideProps` 获取该数据并将其传递给 `Page` ，其用法和getStaticProps 如出一辙：

```javascript
function Page({ data }) {
  // Render data...
}

export async function getServerSideProps() {
  // 假设这是一个api 可以获取一些 json数据
  const res = await fetch(`https://...`)
  const data = await res.json()

  // 传递数据
  return { props: { data } }
}

export default Page
```

## API路由

API 路由为使用 Next.js 构建自己的 API 提供了一种简单的解决方案。也就是说，你可以将 next 应用作为提供html 页面的前端，也作为操作数据库进行数据查询修改的后端。

在 pages/api 目录下的任何文件都将作为 API 端点映射到 /api/ ，例如请求接口 `/api/user` next就会去pages/api文件夹下找到 `user.js / ts` 文件。

在接口文件中，我们 export default 一个方法来接收数据，然后操作后返回数据，方法提供两个实例res 和 req，用法和之前介绍的 node 的框架是基本一致的，详细的使用可以查看：

- `req`: 一个 [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage) 实例，以及一些 [预先构建的中间件](https://www.nextjs.cn/docs/api-routes/api-middlewares)
- `res`: 一个 [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse) 实例，以及一些 [辅助函数](https://www.nextjs.cn/docs/api-routes/response-helpers)

```javascript
export default (req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'application/json')
  res.end(JSON.stringify({ name: 'John Doe' })) // 接口会返回这条数据
}
```

同样的 api 路由也支持动态路由，使用方法和页面路由是一样的，不过我们使用 req 来接收参数

```javascript
export default function handler(req, res) {
  const { pid } = req.query
  res.end(`Post: ${pid}`)
}
```

和 node 类似 API 路由也提供了内置的中间件，有哪些常用的中间键具体可以查看官方文档 

https://www.nextjs.cn/docs/api-routes/api-middlewares

## 部署

Next.js 可以部署到任何安装 Node.js 服务器。可以看到 package.json 中：

```json
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

执行一次 `npm run build` 脚本，可以文件夹中构建生产应用程序 `.next`；

运行 `npm run start` 就能启动一个支持混合页面的 Node.js 服务器，为静态生成的和服务器端呈现的页面以及 API 路由提供服务。

## 项目

这里提供一些字节的官方的教学提供的项目：

- SSR 实现： [github.com/czm12904337…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fczm1290433700%2Fssr-server)
- CMS 仓库地址：[github.com/czm12904337…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fczm1290433700%2Fnextjs-cms)
- Demo 仓库地址： [github.com/czm12904337…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fczm1290433700%2Fnextjs-demo)

更多的原理性的内容可以查看：https://juejin.cn/book/7137945369635192836

