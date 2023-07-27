# class7 - Node入门

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境，Node.js 是 JavaScript 的后端运行环境，官方网站：https://nodejs.org/zh-cn/

Node.js为JavaScript提供了一些服务器级别的操作API，可以用来进行：文件读写、网络服务的构建、网络通信、构建http服务器等操作

## node 的应用场景

-  前端工程化

  打包工具 bundle，uglify等等都使用 node

- web服务端应用

  学习曲线平滑，效率高，其他后端语言可以实现的 node 都可以实现，有用丰富的工具和生态，与前端结合场景有优势，比如 ssr

- 跨端桌面应用

  比如 electron，大型公司内的效率工具

## node运行时

node的运行结构如下：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c3f2dcaaf68145a6b532685b02e3bdc4~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image?)

它运行时有三个特点：异步IO，单线程，跨平台

- 异步IO

当Node.js执行I/O操作时，会在响应返回后恢复操作，而不是阻塞线程并占用额外的内存等待

- 单线程

原因是 js 这个语言是单线程的，node底层是 JS线程+ uv线程 +V8任务线程池 + V8 Inspector线程。这样的好处是不用考虑多线程状态同步问题，同时还能比较有效的利用系统资源；确定点是阻塞会产生更多的负面影响

解决办法是使用多线程或者多进程工具

- 跨平台

node 封装了大部分操作系统的api 提供我们调用，但是也有部分某些平台特有的 api，比如linux特有的，因为 nodejs 跨平台，js无需编译环境，所以开发成本低，整体学习成本低

## node 的模块系统

为了让Node.js的文件可以相互调用，Node.js提供了一个简单的模块系统。模块是Node.js 应用程序的基本组成部分，文件和模块是一一对应的。换言之，一个 Node.js 文件就是一个模块。

Node.js 模块系统使用 [CommonJS](https://so.csdn.net/so/search?q=CommonJS&spm=1001.2101.3001.7020) 规范，并且分为三大类：

- 内置模块：由 node.js 官方提供的，例如 fs, path, http 等
- 自定义模块：用户创建的每一个  .js 文件，都是自定义模块
- 第三方模块：由第三方开发出来的模块，并非官方提供的内置模块，也不是用户创建的自定义模块，使用前需要下载。

### 模块导入

node 的模块使用 require() 方法加载，模块在第一次加载后会被缓存，多次调用 require() 不会导致模块的代码被执行多次，不同的模块有不同的加载规则：

1. 内置模块是由Node.js 官方提供的模块，内置模块的加载优先级最高
2. 使用 require() 加载自定义模块时，必须指定以 **./** 或 ../ 开头的路径标识符。如果省略了文件的扩展名，则Node.js 会按照 .js , .json , .node 分别尝试，如果没用符合要求的内容，则报错
3. node 会从 /node_modules 文件夹中加载第三方模块
4. 当把目录作为模块标识符时，会在被目录下查找一个叫做package.json的文件，并寻找 main属性，作为 require()加载的入口，如果没用则加载目录下的 index.js 文件，如果以上两步都失败了则报错

```javascript
// 加载内置的模块
const  fs= require('fs')

// 加载用户的自定义模块
const  test = require('./test.js')

// 加载第三方模块
const  moment= require('moment')
```

### 模块的导出

而在自定义模块中，可以使用 module.exports 对象，将模块内的成员共享出去，供外界使用，使用 require() 方法导入模块时，导入的结果永远是module.exports 指向的对象。

```javascript
//导出单个数据
module.exports.msg = 'test'
//导出一个对象
module.exports = {
	msg: 'test',
	say(){
		console.log('test')
	}
}
```

## 原生模块

### fs 文件系统模块

fs 模块是Node.js 官方提供的，用来操作文件的模块。它提供了一系列的方法和属性，用来满足用户对文件的操作需求。

fs.readFile 用于读取指定文件的内容，他有三个参数：

 	1. 字符串，表示文件路径；
 	2. 可选参数，表示以什么编码格式来读取文件
 	3. 传入一个函数，表示文件读取完，通过回调函数拿到读取的结果 

 fs.readFileSync() 则是上述方法的同步版本，就是方法必须执行完毕才会进行吓一语句，因为是同步的方法，所以它没用第三个也就是函数的参数作为回调，而是直接返回读取到的内容

```javascript
// 导入 fs 模块，
const fs = require('fs');

//异步读取文件
fs.readFile( 'a.txt' , 'utf8', (err, data)=>{
     //如果出错  则抛出错误
     if(err)  throw err
     console.log(data.toString());
})
```

fs.writeFile() 是向文件中写入内容的函数，它的三个参数是：

1. 字符串，表示文件存放的路径；
2. 可选参数，表示要写入的内容
3. 回调函数

同样这个方法也有它的同步版本 fs.writeFileSync() 

```javascript
// 写入文件
fs.writeFile("a.txt","测试",(err)=>{
   // 如果写入成功 err 会打印 null，
    console.log(err);
})
```

与上面的函数用法相似，fs.appendFile() 用于在不改变原来内容的情况下追加内容，而 writeFile 则会覆盖原来的所有内容

fs.mkdir() 用于创建目录，第二个参数可选用于设置权限

```javascript
fs.mkdir('./css',777,(err)=>{
    if(err) throw err;
    console.log("成功");
}
```

fs.stat() 用于检测是文件还是目录

fs.readdir()  读取目录中的所有文件，返回一个数组

fs.rmdir() 用于删除目录

fs.unlink() 用于删除文件

上述函数用法类似，都是传入一个路径和一共回调函数

```javascript
fs.stat('./a.txt',(err, data)=>{
    console.log(`是文件：${data.isFile()}`);  //布尔值
    console.log(`是目录：${data.isDirectory()}`); //布尔值
})
fs.readdir('./html',(err,data)=>{
    if(err) throw err;
    console.log("读取目录成功",data);
})
```

fs.rename() 用于对一个文件进行改变，可以进行重命名也可以进行路径的移动，它的三个参数是：

1. 字符串，表示文件存放的位置；
2. 字符串，表示文件修改的位置和名字；
3. 回调函数

```javascript
fs.rename('./css/index.html','./css/index.css',(err)=>{
    if(err) throw err;
    console.log("重命名成功");
})
 
fs.rename('./html/hh.css','./css/www.css',(err)=>{
    if(err) throw err;
    console.log("移动文件并重命名成功");
})
```

fs.createReadStream() ，fs.createWriteStream() 分别用于打开读取和写入文件流，通常对大文件的拷贝，写入等，会采用文件流的方式进行操作

```javascript
var readStream = fs.createReadStream('./a.txt');
// 读取
readStream.on('data',data=>{
     console.log(data)
})
// 取完完毕
readStream.on('end',()=>{
	 console.log('end')
})

var writeStream = fs.createWriteStream('./a.txt');
//写入
writeStream.write('test')
writeStream.end();
//写入完毕回调
writeStream.on('finish',()=>{
    console.log('end');
})
```

### path 路径模块

node 可以通过 path 模块获取路径，并且通过函数将路径进行操作：

通过`__dirname` 可以获取当前文件所处的路径

path.resolve 把一个路径或路径片段的序列解析为一个绝对路径

path.relative 则是相对路径

path.join 则是可以拼接路径片段的序列

path.normalize 则可以格式化路径

```javascript
 var absPath=path.resolve("foo/bar","tmp/file/","..","./js");
// foo/bar/tmp/js
 var relPath=path.relative("foo/bar/baz","foo/bar/tmp");
// ../tmp
 var joinPath=path.join("foo","/bar","../","tmp");
 // foo/tmp
var norPath=path.normalize("foo//bar/baz/..");
 // foo/bar
```

path.basename() 获取一个路径中的文件名称，你可以指定后缀然后只获取名字去掉后缀；path.extname() 则是获取文件拓展名；path.dirname 返回文件所处的文件夹

```javascript
 var basePath=path.basename("foo/bar/tmp/img.png");
// img.png
 var basePath=path.basename("foo/bar/tmp/img.png","png");
// img
 var extPath=path.extname("img.png");
 // .png
 var dirPath=path.dirname("foo/bar/tmp");
 // foo/bar
```

### http 模块

http 模块用于启动一个 http 服务器，可以通过它接收前端请求，操作服务器、返回需要的数据：

```javascript
//加载 http 请求
var http = require("http");
// 创建http请求服务器
let server = http.createServer();
// 设置端口号。默认是80
server.listen("8080",function(){
});
```

如果服务器收到客服端请求，会调用 server.on() 为服务器绑定 request 事件处理函数。你可以获取一个 req 对象来获取这个请求的相关数据：
req.baseUrl：获取路由当前安装的URL路径

req.body / req.cookies：获得「请求主体」/ Cookies,//post请求参数获取

req.params：获取路由的parameters

req.path：获取请求路径

req.query：获取URL的查询参数串 

req.url 是客户端请求的 URL地址

req.method 是客户端请求的 method 类型

```javascript
server.on('request',(req)=>{
    // req 是请求对象
    const url = req.url
    console.log(str);
})
```

我们可以使用 res 响应对象返回我们的数据

```javascript
server.on('request',(req, res)=>{
    const str = "test";
    //res.end() 方法，向客户端发送指定内容
    res.end(str)
})
```

## 第三方模块

### NPM下载

node 通过了一个工具 npm ，我们可以使用它在下载其他发布的模块，我们下载的内容会保存在node_modules 文件夹下，require() 导入第三方包时，就是从这个目录中查找并加载包。npm 的常用命令如下：

```javascript
npm init [-y] //用来初始化项目，并生成一个package.json文件
npm install //用于安装当前项目的所有依赖，指的是package.json文件中所记录的依赖项。
npm install pagckagename --save 或 -S //下载某个包，并把该包的信息记录到生产环境依赖中（dependencies）
npm install pagckagename --save-dev 或 -D //下载某个包，并把该包的信息记录到开发环境依赖中（devDependencies）。同样需要注意的是-D是大写的。
npm install pagckagename --global 或 -g //全局安装某个包，指安装到你的电脑本地，其他人不能使用
npm -v //查看当前电脑上安装的npm版本号
npm view pagckagename version //查看远程仓储上某个package的正式版本的、最新的版本号
npm view pagckagename versions //查看远程仓储上某个package的所有历史版本。包括alpha版、beta版等等
npm uninstall pagckagename  //卸载某个包，参数和上述install一致
```

我们可以使用 package.json 文件的 dependencies 节点（生产环境）和 devDependencies 节点（开发环境）来记录项目中安装了哪些包，其他用户使用 npm install 命令就可以依次安装 package.json 指定的包，

同时当我们使用 npm 下载的资源之后，我们会获得一个 package-lock.json 配置文件，用于记录 node_modules 目录下的每一个包的下载信息

### 发布包

我们可以将自己写的 node 模块整合成包发布到官方仓库提供其他用户使用，一个规范的包，它的组成结构，必须符合以下3点要求:

1. 包不能重名
2. 包的顶级目录下要必须包含package.json这个包管理配置文件
3. package.json 中必须包含name，version，main这三个属性，分别代表包的名字、版本号、包的入口。package.json 文件的其他配置给出一个博客供大家参考：https://blog.csdn.net/song_6666/article/details/123739622

我们可以使用 npm login 登录到官方，当然你需要先注册一个账号，官网地址： https://www.npmjs.com/，之后运行 npm publish 命令将包推送到官方仓库

## Node 框架

现在常用的后端框架是 express 和 koa，这里分别给出简单的例子

### express

Express是基于nodejs的一个框架，它把原先的许多操作变的简单灵活， 使用Express 可以快速地搭建一个完整功能的网站。[express](https://so.csdn.net/so/search?q=express&spm=1001.2101.3001.7020)官方网址：[www.expressjs.com.cn](https://blog.csdn.net/no10086/article/details/88958282)

- 引入express

我们首先下载它的依赖，创建一个 app.js 的文件，之后在项目中引入其模块就可以使用express了：

```javascript
const express = require('express');		//引入express模块
var app=express();
```

只要给我们绑定的 express 的提供一个监听的端口就可以启动一个服务：

```javascript
app.listen(8080);
```

之后使用 `node app.js` 启动服务

- Express路由方法

Express路由是用一个方法包裹一个路径，当你收到指定路径的对应方法的请求的时候，调用其回调方法

Express方法源于 HTTP 方法之一。它可请求的方法包括：get、post、put、head、delete、options、trace、copy、lock、mkcol、move、purge、propfind、proppatch、unlock、report、mkactivity、checkout、merge、m-search、notify、subscribe、unsubscribe、patch、search 和 connect。

Express 路径包含三种表达形式，分别为字符串、字符串模式、正则表达式

```javascript
app.get("/test",function(req,res){
})
app.get("/ab+cd",function(req,res){
})
app.get(/^a/,function(req,res){
})
```

express 还提供了动态路，当路径匹配到一个模式的路由时，可以获得对应模式的参数，例如：

```javascript
app.get("/login/:aid",function(req,res){
});
//你可以输入 /login/123  这种模式来匹配它
```

- 获得请求参数

我们最常用的接口请求是 get 和 post 他们有不同的参数获取方式

get 请求的数据一般存放在请求路径中，可以通过 query 参数获取

```javascript
//http://localhost:8080/login?goods1=0001&goods2=0002
app.get("/login",function(req,res){
	console.log(req.query.user);
});
```

post 方法的参数一般在 req.body 中，一般是json格式的数据

```javascript
//http://localhost:8080/login
app.get("/login",function(req,res){
	console.log(req.body);
});
//{ name:'1111',pwd:'1222'}
```

Express中也可以使用 body-parser 作为请求体解析post数据，这个模块也能解析：JSON、Raw、文本、URL-encoded格式的请求体。
```javascript
const bodyParser=require("body-parser");

// 解析以 application/json 和 application/x-www-form-urlencoded 提交的数据
var jsonParser = bodyParser.json();
var urlencodedParser = bodyParser.urlencoded({ extended: false });

app.post("/",urlencodedParser,function(req,res){
	res.send(req.body);
});
```

- 中间件

中间件是进行中间代理操作的，大多数情况下，中间件就是在做接收到请求和发送响应中间的一系列操作，使用 app.use 可以获得写入应用级别中间键，在访问你的回调函数之前，你可以不通过路径使得全部的接口都调用你的中间键，或者提供一个路径，使得对应路径的接口才触发你的中间键，中间键可以拿到接口的参数信息等，也可以直接进行返回，其中有一个next是使得它进行下一步操作的方法

```javascript
app.use((err, req, res, next) =>{
	console.log("访问之前");
	next();
});
app.use("/user",function(err,req,res,next){
	console.log("用户登录");
	next(err);
}
```

路由级中间件和应用级中间件类似，不同的是它触发的位置是路由匹配之前，而后者是路由匹配之后

```javascript
var router = express.Router()
router.use("/",(err, req, res, next) =>{
	console.log("匹配前");
	next();
});
```

express.static 是 Express的唯一内置中间件。通过express.static我们可以指定要加载的静态资源。root代表加载静态资源的路径，options作为可选参数可以配置属性，具体可以查阅官方文档

- cookie和session

浏览器会将我们的操作信息保存到cookie上面。进而言之，cookie就是储存在用户本地终端上的数据。你可以通过express 在接口中可以设置和获取cookie

```javascript
const cookieParser=require("cookie-parser");
app.use(cookieParser());
//设置cookie
app.get("/set",function(req,res){
	res.cookie("userName",'张三',{maxAge: 20000, httpOnly: true});
	res.send("设置cookie成功");
});

//获取cookie
app.get("/get",function(req,res){
	console.log(req.cookies.userName);
	res.send("获取cookie成功，cookie为："+ req.cookies.userName);
});
```

session是另一种记录客户状态的机制，与cookie保存在客户端浏览器不同，session保存在服务器当中，当客户端访问服务器时，服务器会生成一个session对象，对象中保存的是key:value值，同时服务器会将key传回给客户端的cookie当中；当用户第二次访问服务器时，就会把cookie当中的key传回到服务器中，最后服务器会吧value值返回给客户端。

```javascript
const session=require("express-session");
app.use(session({
	 secret: "keyboard cat",
	 resave: false,
	 saveUninitialized: true,
	 cookie: ('name', 'value',{maxAge:  5*60*1000,secure: false})
}));
app.use('/login',function(req,res){
	//设置session
	req.session.userinfo='张三';
    //获得session
    console.log(req.session.userinfo)
    //销毁session
	req.session.destroy(function(err){
	})
});
```

seesion的配置项如下：

```xml
1. name - cookie的名字（原属性名为 key）。（默认：’connect.sid’）
2. store - session存储实例
3. secret - 用它来对session cookie签名，防止篡改
4. cookie - session cookie设置 （默认：{ path: ‘/‘, httpOnly: true,secure: false, maxAge: null }）
5. genid - 生成新session ID的函数 （默认使用uid2库）
6. rolling - 在每次请求时强行设置cookie，这将重置cookie过期时间（默认：false）
7. resave - 强制保存session即使它并没有变化 （默认： true, 建议设为：false）
8. proxy - 当设置了secure cookies（通过”x-forwarded-proto” header ）时信任反向代理。当设定为true时，
”x-forwarded-proto” header 将被使用。当设定为false时，所有headers将被忽略。当该属性没有被设定时，将使用Express的trust proxy。
9. saveUninitialized - 强制将未初始化的session存储。当新建了一个session且未设定属性或值时，它就处于未初始化状态。在设定一个cookie前，这对于登陆验证，减轻服务端存储压力，权限控制是有帮助的。（默认：true）
10. unset - 控制req.session是否取消（例如通过 delete，或者将它的值设置为null）。这可以使session保持存储状态但忽略修改或删除的请求（默认：keep）
```

- 连接数据库

一般的，后端需要有连接数据库功能，使得你交互的数据可以持久化，node 可以连接多种不同种类的数据库，这里简单介绍 mysql 数据库的连接，只需要下载一个 mysql 的依赖再配置数据库就可以运行了：

```javascript
const mysql = require("mysql");
var db=mysql.createConnection({host: "localhost",  //地址 
							port: "3306",          //端口
							user: "root",          //用户名
							password: "123456",    //密码
							database: "test"}); //数据库名称
```

我们可以通过 query 语句提交 sql 语句来执行操作，之后通过回调函数来处理结果，以下是一个简单的例子，假设我们传入 name 和 pwd字段，我们通过模板字符串拼接成一个完整的sql语句，然后执行它：

```javascript
db.query(`INSERT INTO students (name,pwd) VALUES('${req.query.name}','${req.query.pwd}')`,function(err,data){
			if(err){
				res.status(500).send('创建失败').end();
			}else{
				res.status(200).send('创建成功').end();
            }
});
```

- 上传文件

node 除了可以操作数据库，也可以对服务器上的文件进行操作，通过 multer  可以帮助我们快速处理文件类型的数据，我们只需要引入他，然后配置文件存放的地址，最后配置它即可

```javascript
const multer=require('multer');
var upload=multer({dest:'./upload/'});  //dest是文件存放的路径
var fs = require('fs');
const singleMidle = upload.single("singleFile"); //一次处理一张，参数的名字是你type="file"组件的name字段
const arrMidle  = upload.array("arrayFile", 5);//一次最多处理5张
const fieldsMidle  = upload.fields([
  {name:"fieldSingleFile", maxCount:1},
  {name:"fieldArrayFile", maxCount:4}
]);//可同时处理多个上传控件的上传

//单个文件的处理
app.use("/",singleMidle,function(req,res,next){
	var oldFile=req.file.destination+req.file.filename;	//指定旧文件
	var newFile=req.file.destination + req.file.originalname;	//指定新文件
	fs.rename(oldFile,newFile,function(err){
		if(err){
			res.send('上传失败！');
		}else{
			res.send('上传成功！');
		}
	});
	next();
});

//这是文件里的上传模块html
<input type="file" name="singleFile" >

//多个文件的处理
app.use("/",arrMidle,function(req,res,next){
	req.files.forEach(function(ele,index){
		console.log(ele);
		var oldFile=ele.destination+ele.filename;	//指定旧文件
		var newFile=ele.destination+ele.originalname;	//指定新文件
		fs.rename(oldFile,newFile,function(err){
			err?console.log('上传失败！'):console.log('上传成功！');
		});
	});
	res.send("成功上传");
});

//这是文件里的上传模块html
<input type="file" name="arrayFile" multiple>
```

Multer通过使用limits这个对象来对数据进行限制

```javascript
var upload=multer({dest:'./upload/',limits:{fileSize: 1024 * 1024 * 20,files: 5}});
```

- 下载文件

通过 res.download() 就可以实现服务器上文件的下载：

```javascript
res.download('/1.pdf');
res.download('/1.pdf', function(err){
});
```

- 模板引擎

和大多数后台语言一样，nodejs 可以指定网页渲染的模板引擎。这里简单介绍常用的模板引擎 ejs ，想要具体了解的可以自行查阅文档。

https://ejs.bootcss.com/#docs

我们首先下载依赖，然后创建一个 .ejs 文件，标记<% %>和<%= %>来当做模板，前者是可以包裹一段 js 代码，后者则是可以输出一个对应的值：

```javascript
<html>
<body>
    <ul>
        <%
            for(var i = 0; i < students.length; i++){
        %>
           <li><%= students[i] %></li>
        <%
            }
        %>
    </ul>
</body>
</html>
```

之后在 app.js 中添加模板引擎相关代码

```javascript
// 设置模板引擎渲染界面的存放文件夹
app.set('views', path.join(__dirname, 'views'));
//设置模板引擎为ejs
app.set('view engine', 'ejs');

//这个接口告诉我们，当我们访问指定路由的时候，express会找到设定文件夹下方的index.ejs文件，然后使用我们传入的数据来渲染这个界面
app.get("/",(req,res)=>{
    res.render("index",{
        "students" : ["小明","小红","小强"],
    },function(err, html) {
  		console.log(html);//这是渲染完成的html
	}));   
});
```

### koa

koa是express同一个团队开发的一个新的Web框架，Koa旨在为Web应用程序和API提供更小、更丰富和更强大的能力，以及更强大的异步处理能力。

- koa的引入

同样，我们只需要下载依赖然后导入它就可以使用我们的 koa 框架

```javascript
const Koa = require('koa')
const app = new Koa()
```

- 中间件

和 express 一样，koa也提供了中间件的能力，你可以在 koa 框架写入一个函数，这个函数有两个参数，`context` 指的是上下文环境对象，封装了一些属性，该对象类似原生http中的 req + res，具体的详细属性可以查看官网 https://koajs.com/#context，后续会随着例子简单介绍一些属性；`next` 用于把中间件的执行权交给下游的中间件，在当前中间件中位于 `next()` 之后的代码会暂停执行，直到最后一个中间件执行完毕，再自下而上依次执行每个中间件中 `next` 值周的代码，类似于栈的先进后出。

```javascript
app.use((ctx, next) => {
  console.log(ctx.request.url); //请求地址
  console.log(ctx.request.method);  //请求方法，GET，POST等
  next()
})
```

- 路由

koa 使用了专门的 koa-router 模块来控制路由，你还是需要先下载依赖，其接口的写法和 express 的基本一致

```javascript
const Koa = require('koa')
const app = new Koa()
const userRouter = require('./routers/users')
// 把返回的函数注册为中间件
app.use(userRouter.routes())
// 捕获不支持的请求，注册为中间件 客户端接收到：405 Method Not Allowed,501 Not Implemented等等
app.use(userRouter.allowedMethods())
app.listen(8000, () => {
})
```

routers/users.js：

```javascript
const Router = require('koa-router')
// prefix 前缀
const router = new Router({ prefix: '/users' })

router.get('/', (ctx, next) => {
  //返回状态码
  ctx.response.status = 200
  //返回值
  ctx.response.body = 'test'
})

router.post('/list', (ctx, next) => {
  ctx.response.body = 'list'
})

module.exports = router 
```

- 参数解析

koa 支持解析我们的路径参数，get 的请求参数

```javascript
//请求地址 http://localhost:8000/users/test?id=1
router.get('/:path', (ctx, next) => { 
  console.log(ctx.request.params); //{ path: 'test' }
  console.log(ctx.request.query); //{ id: 1 }
})
```

koa默认没有内置解析post的 `json`和`urlencoded`的中间件，我们还是推荐我们使用 koa-bodyparser

```javascript
const Koa = require('koa')
const bodyParser = require('koa-bodyparser')
const app = new Koa()

// 使用第三方库帮助解析 json和urlencoded
app.use(bodyParser())

app.use((ctx, next) => {
  console.log('ctx.request.body: ', ctx.request.body);  //body是post传递参数的位置
  next()
})
```

- 文件和静态资源

koa 有 `koa-multer` 这个库来是实现文件上传，其用法和 express 的基本一致，如果想使用express 的static 这个库的，express需要额外下载模块 `koa-static`

```javascript
const koaStatic = require('koa-static')
const app = new Koa()
app.use(koaStatic('./dist'))
```

- 数据库和模板引擎

数据库的连接操作和 express 完全一致，因为连接数据库使用的是独立的包，express 和 koa 仅仅在编写接口处理逻辑时有所不同，数据库的操作全部依赖 mysql 这个模块进行操作，所以完全一致，不再赘述；模板引擎相关的操作涉及到代码逻辑部分 express 和 koa也完全一致，所以也不再赘述

- 异常处理

与 express 不同的是，express 中的操作的回调函数中，都会有一个 err 选项，你可以从中接收错误进行处理；koa中你可以使用 try catch的语法来进行错误的捕捉，使用 app.on 监听异常可以捕获到抛出的异常进行处理

```javascript
router.post('/list', (ctx, next) => {
 	    try{
        //.....
        }catch(error){
            error.message = '异常代码101';
            throw error;    // 抛出处理后的异常
        }
	}
})
app.on('error', err =>  
    log.error('server error', err)  
);
```

后续可能会更新一个 express 相关项目，先放出 git 地址：https://github.com/aiai0603/nodejs_work



### 两者的对比

根据上文可以看出，express 和 koa 基本上用法是没有太大的差异的，但是前文说过，koa 在异步处理和性能上更加优秀，这里简单谈谈两者的区别和其产生的原因：

1. 异步和错误处理上：

Express是基于回调进行处理，也是node中最常见的 `Error-First` 的模式，操作的回调函数中，都会有一个 err 选项，

Koa是使用的号称异步终极解决方案的`Async/Await`，也就是基于[Promise](https://so.csdn.net/so/search?q=Promise&spm=1001.2101.3001.7020) 进行异步的处理，所以可以使用Try-Catch来捕获错误

2. 中间件的区别

Express的中间件是线性模型：中间件一个接一个按照顺序执行，上一个中间件会通过next触发下一个中间件，response响应写在最后一个中间件中，遇到http请求，根据path和method判断触发哪些中间件；

Koa的中间件是洋葱模型，通过async await实现的，中间件之间通过next函数联系，当一个中间件调用next()后，会将控制权交给下一个中间件，直到下一个中间件不再执行next()后，会沿路返回，将控制权交给前一个中间件。

## node 调试

V8 Inspector：开箱即用，特性丰富强大，与前端开发一致、跨平台

- node --inspect
- 打开 `http://localhost:9229/json` 进入devtoolsFrontendUrl后的url
- 打开 chrome://inspect 然后点击Open dedicated DevTools for Node

用途：

- 查看console.log内容
- breakpoint
- 高CPU、死循环：cpuprofile
- 高内存占用：heapsnapshot
- 性能分析

## 部署

- 需要解决的问题
  - 守护进程：当进程退出时，重新垃圾
  - 多进程：cluster便捷的利用多进程
  - 记录进程状态，用于诊断
- 容器环境
  - 通常有健康检查手段，只需考虑多核cpu利用率即可



