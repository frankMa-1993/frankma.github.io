# class3 - 如何写好JS

本节课从实践维度解读在实际编码过程中何种类型的 JavaScript 代码称之为“好代码”，并从 JS 出发，总结其他语言编码可遵循的共性原则，由浅入深，其三大原则是：

- 各司其职——html，css，js分离
- 组件封装——组件具备准确性，拓展性，复用性
- 过程抽象——函数式



## 各司其职

例子：使用 js 实现的切换背景颜色的例子

```js
//html
 <header>
        <button id="modeBtn">🌞</button> 
        <h1>深浅色模式切换</h1>
</header>
 
//css
#modeBtn {
      font-size: 2rem;
      float: right;
      border: none;
      background: transparent;
}

//js
window.onload=function(){
    document.getElementById("file-btn") 
    const btn = document.getElementById('modeBtn');
    btn.addEventListener('click', (e) => {
      const body = document.body; //获取页面body元素
      if (e.target.innerHTML === '🌞') //是🌞就将页面的背景色改成黑色，字体颜色改成白色，并将按钮元素变成🌜
      {
        body.style.backgroundColor = 'black';
        body.style.color = 'white';
        e.target.innerHTML = '🌜';
      } 
      else //是🌜就将页面背景色改为白色，字体改为黑色，并将按钮元素变成🌞
      {
        body.style.backgroundColor = 'white';
        body.style.color = 'black';
        e.target.innerHTML = '🌞';
      }
    });
}
```

存在问题：使用 js 来控制 css 属性，很难直观的理解需求的原始含义

修改方案：如下，将直接修改属性值编程修改class的名称

```js
//html
 <header>
        <button id="modeBtn"></button> 
        <h1>深浅色模式切换</h1>
 </header>
 
 //css
    body.night {
      background-color: black;
      color: white;
      transition: all 1s; //美观上做了一些调整，切换时有1秒的延时
    }

    #modeBtn::after { //各司其职，让css来实现图标的切换
      content: '🌞';
    }

    body.night #modeBtn::after {
      content: '🌜';
    }
    
//js
window.onload=function(){
   const btn = document.getElementById('modeBtn');
        btn.addEventListener('click', (e) => {
        const body = document.body;
        if (body.className !== 'night') { //通过className的'night'来显示深色模式，判断上更直观
            body.className = 'night';
        } else {
            body.className = '';
        }
    });
}

```

继续改进：使用纯粹的 css 来控制，使用 checkbox 来控制切换，因为样式就是 css 的事情，所以尽量使用纯 css 实现样式的事情，使用伪类选择器和隐藏 checkbox 的方式来实现

```js
//html
<input id="modeCheckBox" type="checkbox"> 
  <div class="content">
    <header>
      <label id="modeBtn" for="modeCheckBox"></label>
      <h1>深浅色模式切换</h1>
    </header>
  </div>
  
//css
    .content {
      padding: 10px;
      transition: background-color 1s, color 1s;
    }

    #modeCheckBox {
      display: none; //将大盒子外面的checkbox隐藏起来
    }

    #modeCheckBox:checked+.content { //通过 checkbox 的伪类选择器checked，点击checkbox就会触发这个伪类
      background-color: black;
      color: white;
      transition: all 1s;
    }

    #modeBtn {
      font-size: 2rem;
      float: right;
    }

    #modeBtn::after {
      content: '🌞';
    }

    #modeCheckBox:checked+.content #modeBtn::after {
      content: '🌜';
    }
```

改进要点：

1. html/css/js各司其职
2. 避免不必要的 js 操作样式
3. 追求纯交互 0 js的样式操作方案
4. 用不同的 class 来表示不同的状态 （不强求）

## 组件封装

例子：实现一个轮播图，我们使用 li 列表来实现轮播，之后将列表的切换逻辑封装成一个class

```js
//html
<div id="my-slider" class="slider-list">
      <ul> 
        <li class="slider-list__item--selected"> 
        //slider表示组件名，list表示元素,item表示具体元素项，selected表示的是状态
          <img src="https://p5.ssl.qhimg.com/t0119c74624763dd070.png">
        </li>
        <li class="slider-list__item">
          <img src="https://p4.ssl.qhimg.com/t01adbe3351db853eb3.jpg">
        </li>
        <li class="slider-list__item">
          <img src="https://p2.ssl.qhimg.com/t01645cd5ba0c3b60cb.jpg">
        </li>
        <li class="slider-list__item">
          <img src="https://p4.ssl.qhimg.com/t01331ac159b58f5478.jpg">
        </li>
      </ul>
</div>
//css
#my-slider{
    position: relative;
    width: 790px;
  }

  .slider-list ul{
    list-style-type:none;
    position: relative;
    padding: 0;
    margin: 0;
  }

  .slider-list__item,
  .slider-list__item--selected{ 
    position: absolute;
    transition: opacity 1s;
    opacity: 0;
    text-align: center;
  }

  .slider-list__item--selected{
    transition: opacity 1s;
    opacity: 1;
  }

//js
class Slider{
    constructor(id){
      this.container = document.getElementById(id);
      this.items = this.container
      .querySelectorAll('.slider-list__item, .slider-list__item--selected');
    }
    getSelectedItem(){ 
    //得到当前轮播图正在显示的li
      const selected = this.container
        .querySelector('.slider-list__item--selected');
      return selected
    }
    getSelectedItemIndex(){ 
    //获取当前轮播图的显示li的下标
      return Array.from(this.items).indexOf(this.getSelectedItem());
    }
    slideTo(idx){ 
    //轮播到指定的idx下标的图片
      const selected = this.getSelectedItem();
      if(selected){ 
        selected.className = 'slider-list__item';
      }
      const item = this.items[idx];
      if(item){
        item.className = 'slider-list__item--selected';
      }
    }
    slideNext(){ 
    //下一张
      const currentIdx = this.getSelectedItemIndex();
      const nextIdx = (currentIdx + 1) % this.items.length;
      this.slideTo(nextIdx);
    }
    slidePrevious(){
    //上一张
      const currentIdx = this.getSelectedItemIndex();
      const previousIdx = (this.items.length + currentIdx - 1)
        % this.items.length;
      this.slideTo(previousIdx);  
    }
  }

  const slider = new Slider('my-slider');
  slider.slideTo(3);
  
//这样就可以通过手动调用API来使用轮播图了

const slider = new Slider('my-slider');
slider.slideTo(1);
slider.slideTo(2);
slider.slideNext();
slider.slidePrevious();

//还可以直接定义一个定时器，让他自动播放

const slider = new Slider('my-slider');
setInterval(() => { 
    slider.slideNext(); 
}, 1000);
```

实现的效果如下：

![126](/bit-dance/img/126.png)

之后我们需要为我们的添加左右切换按钮和下方的状态按钮，使用自定义事件来解耦合

```js
//html
  <a class="slide-list__next"></a>
  <a class="slide-list__previous"></a>
  <div class="slide-list__control">
    <span class="slide-list__control-buttons--selected"></span>
    <span class="slide-list__control-buttons"></span>
    <span class="slide-list__control-buttons"></span>
    <span class="slide-list__control-buttons"></span>
  </div>

//css
.slide-list__control-buttons, 
.slide-list__control-buttons--selected
{ 
  display: inline-block; 
  width: 15px; 
  height: 15px; 
  border-radius: 50%; 
  margin: 0 5px; 
  background-color: white; 
  cursor: pointer;  
}
.slide-list__control-buttons--selected 
{ 
  background-color: red; 
}

//js
      // 鼠标经过某个小圆点，就将此圆点对应的图片显示出来，并且停止循环轮播
      controller.addEventListener('mouseover', evt=>{
        const idx = Array.from(buttons).indexOf(evt.target);
        if(idx >= 0){
          this.slideTo(idx);
          this.stop();
        }
      });
      
      // 鼠标移开小圆点，就继续开始循环轮播
      controller.addEventListener('mouseout', evt=>{
        this.start();
      });
      
      // 注册slide事件，将选中的图片和小圆点设置为selected状态
      this.container.addEventListener('slide', evt => {
        const idx = evt.detail.index
        const selected = controller.querySelector('.slide-list__control-buttons--selected');
        if(selected) selected.className = 'slide-list__control-buttons';
        buttons[idx].className = 'slide-list__control-buttons--selected';
      })
    
    // 点击左边小箭头，翻到前一页
    const previous = this.container.querySelector('.slide-list__previous');
    if(previous){
      previous.addEventListener('click', evt => {
        this.stop();
        this.slidePrevious();
        this.start();
        evt.preventDefault();
      });
    }
    // 点击右边小箭头，翻到后一页
    const next = this.container.querySelector('.slide-list__next');
    if(next){
      next.addEventListener('click', evt => {
        this.stop();
        this.slideNext();
        this.start();
        evt.preventDefault();
      });
    }

```

实现效果如下：

![image-20230116153853179](/bit-dance/img/14.png)

上文的例子存在的问题是：它不够灵活，控制点和组件本身是绑定在一起的，如果你需要修改一个点，比如把需要去掉左右切换的箭头按钮，需要修改很多东西

改进方案是：将组件插件化——将控制元素抽取成插件，插件与组件之间通过依赖注入的方式建立联系

```js
 function pluginController(slider){
     const controller = slider.container.querySelector('.slide-list__control');
     if(controller){
       const buttons = controller.querySelectorAll('.slide-list__control-buttons, .slide-list__control-buttons--selected');
       controller.addEventListener('mouseover', evt=>{
         const idx = Array.from(buttons).indexOf(evt.target);
         if(idx >= 0){
           slider.slideTo(idx);
           slider.stop();
         }
       });
       controller.addEventListener('mouseout', evt=>{
         slider.start();
       });
       slider.addEventListener('slide', evt => {
         const idx = evt.detail.index
         const selected = controller.querySelector('.slide-list__control-buttons--selected');
         if(selected) selected.className = 'slide-list__control-buttons';
         buttons[idx].className = 'slide-list__control-buttons--selected';
       });
     }  
   }
   function pluginPrevious(slider){
     const previous = slider.container.querySelector('.slide-list__previous');
     if(previous){
       previous.addEventListener('click', evt => {
         slider.stop();
         slider.slidePrevious();
         slider.start();
         evt.preventDefault();
       });
     }  
   }
   function pluginNext(slider){
     const next = slider.container.querySelector('.slide-list__next');
     if(next){
       next.addEventListener('click', evt => {
         slider.stop();
         slider.slideNext();
         slider.start();
         evt.preventDefault();
       });
     }  
   }
```

这样的好处是，我就算新增加一个插件，只要把组件注册到代码里就行了。

缺点是：如果我们不要某个插件，还要把对应的代码删掉

改进：HTML模板化，我们用 js 去写一个组件的模板，我们只需要传递对应的数据就可以生成对应的html，这样改进以后 html 仅需要一行代码就可以生成我们的组件

```js
//html
<div id="my-slider"> </div>
//js
class Slider{
     constructor(id, opts = {images:[], cycle: 3000}){
       this.container = document.getElementById(id);
       this.options = opts;
       this.container.innerHTML = this.render();
       this.items = this.container.querySelectorAll('.slider-list__item, .slider-list__item--selected');
       this.cycle = opts.cycle || 3000;
       this.slideTo(0);
     }
     render(){
       const images = this.options.images;
       const content = images.map(image => `
         <li class="slider-list__item">
           <img src="${image}">
         </li>    
       `.trim());
       
       return `<ul>${content.join('')}</ul>`;
     }
     ...
   }
```

最后一次改进：抽象，我们可以将每个部分公共的部分抽取出来，编写一个抽象的顶层，让其他组件继承它，然后重载重写其中的核心方法来实现自己的功能

```js
class Component{
     constructor(id, opts = {name, data:[]}){
         this.container = document.getElementById(id);
         this.options = opts;
         this.container.innerHTML = this.render(opts.data);
     }
     registerPlugins(...plugins){
         plugins.forEach(plugin => {
             const pluginContainer = document.createElement('div');
             pluginContainer.className = `.${name}__plugin`;
             pluginContainer.innerHTML = plugin.render(this.options.data);
             this.container.appendChild(pluginContainer);
             plugin.action(this);
         });
     }
     render(data) {
         /* abstract */
         return ''
     }
 }
```

![image-20230116160948863](/bit-dance/img/16.png)

这样做的好处是：即插即用,形成了一个抽象的通用的模型

总结：我们实现一个组件的步骤是：结构设计、展现效果、行为设计

三次重构：1.插件化 2. 模板化 3. 抽象化

注意：这样做并不改变各司其职的原则，虽然最后一次重构后将 html 生成放在了 js 中，但是本质上 html 还是做着生成页面元素的工作，js 还是做着控制交互的工作，并没有改变职能。

## 过程抽象

过程抽象是，用来控制局部细节的方法，是函数式编程的基础，典型例子是 react hooks，

还是通过应该例子来讲说：有一个列表，生成了一系列的 buttons ，点击按钮可以将它从列表中移除，但是他有一个2秒的动画，但是如果我们点击多次一个按钮，可能就会产生问题，所以我们希望我们的请求只执行一次，我们可以封装一个只执行一次的函数：

```js
function once(fn){
  return function(...args){
    if(fn){
      const ret=fn.apply(this,args);
      fn=null;
      return ret;
    }
  }
}
```

这个函数是一个**高阶函数**，它传入一个函数，返回一个函数，如果函数第一次调用，我们用当前的函数和参数来调用它，并且返回结果，再将函数的值设置为 null，当我们再次调用的时候函数已经不存在了，所以可以控制我们的函数只调用一次。

常用的高阶函数有：

节流函数：控制事件的触发频率，如果按钮的响应频率等

```js
function throttle(fn,time=5000){
  let timer
  return function(...args){
    if(timer== null){
      fn.apply(this,args);
      timer=setTimeout(()=>{
        timer=null
      },time)
    }
  }
}
```

防抖函数：比如我们要实现一个自动保存的功能，但是每次变化都保存会有一个很大的负担，我们需要等内容不变化才保存：

```js
function debounce(fn,dur){
  dur=dur||100;
  let timer
  return function(...args){
    clearTimeout(timer);
    timer=setTimeout(()=>{
      fn.apply(this,args)
    },dur)
      
  }
}
```

为什么要使用高阶函数？纯函数是结果可以预期的函数，如果我们输入 a 返回一定是 b ，并且没用副作用，即不会改变外部状态，如果给他们写测试会非常麻烦，但是高阶函数一般是纯函数，使用高阶函数可以提高代码的可维护性。

- 编程范式

**命令式**：让代码去做一件事，并告诉他怎么去做。

```js
let array = [1,2,3];
for(i=0;i<array.length;i++)
  console.log(array[i]);
```

**声明式**：让代码去做一类事（做什么），具体的方法（如何去做）被抽象到高阶函数中。

```js
let array = [1,2,3];
array.forEach((e)=>console.log(e);)
```

声明式更加简介，使用**纯函数**不会改变外部环境也不依赖外部变量

## **代码优化**

代码优化方向有很多：

- 风格
- 效率
- 约定
- 使用场景
- 设计

以下是几个例子来说明不同方向的优化：

-  Leftpad 的优化

还是用一个例子来说，如下是 npm 里的一个模块 Leftpad ：

```js
function leftpad(str, len, ch) {
  str = String(str);
  var i = -1;
  if(!ch && ch !== 0) ch = ' ';
  while(++i < len){
      str = ch + str;
  }
  return str;
} 
```

它效率低， 时间复杂度O(n)，但通用，使用场景性能优化空间有限，我们可以使用如下的方式来优化它，它的时间复杂度就到了 O(logn）：

```js
function leftpad(str, len, ch) {
  str = "" + str;
  const padLen = len - str.length;
  if(padLen <= 0) {
    return str;
  }
  return (""+ch).repeat(padLen)+str;
}
function repeat(string,count){
  var n=count;
  if(n<0||n==Infinity){
    throw RangeError("string.ptototype.repeat argument must be greater than or equal to 0 and Infinity")
  }
  var result='';
  while(n){
    if(n%2==1){
      result+=string;
    }
    if(n>1){
      string+=string
    }
    n>>=1;
  }
  return result;
}
```

但是实际上，在这样一个小部件里进行优化，对于这个函数的使用场景来说，只有在字符串特别大的时候还有明显效果，意义不大。

- 红绿灯的例子

我们需要将红绿灯每隔一秒钟进行切换，但是实现出来的代码十分臃肿：

```js
(function reset(){
  traffic.className = 's1';
  setTimeout(function(){
      traffic.className = 's2';
      setTimeout(function(){
        traffic.className = 's3';
        setTimeout(function(){
          traffic.className = 's4';
          setTimeout(function(){
            traffic.className = 's5';
            setTimeout(reset, 1000)
          }, 1000)
        }, 1000)
      }, 1000)
  }, 1000);
})();
```

我们可以将数据的切换过程进行抽象，然后实现一个列表保存我们要切换的过程，将数据传入函数即可完成切换：

```js
const traffic = document.getElementById('traffic');

const stateList = [
  {state: 'wait', last: 1000},
  {state: 'stop', last: 3000},
  {state: 'pass', last: 3000},
];

function start(traffic, stateList){
  function applyState(stateIdx) {
    const {state, last} = stateList[stateIdx];
    traffic.className = state;
    setTimeout(() => {
      applyState((stateIdx + 1) % stateList.length);
    }, last)
  }
  applyState(0);
}

start(traffic, stateList);
```

我们也可以使用过程抽象实现这个功能，我们将所有的功能进行抽象，然后将要执行的内容传入函数，它写的代码很多，但是抽取了通用的方法，其中的方法可以使用在其他地方，具有复用性和通用性：

```js
const traffic = document.getElementById('traffic');

function wait(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

function poll(...fnList){
  let stateIndex = 0;
  
  return async function(...args){
    let fn = fnList[stateIndex++ % fnList.length];
    return await fn.apply(this, args);
  }
}

async function setState(state, ms){
  traffic.className = state;
  await wait(ms);
}

let trafficStatePoll = poll(setState.bind(null, 'wait', 1000),
                            setState.bind(null, 'stop', 3000),
                            setState.bind(null, 'pass', 3000));

(async function() {
  // noprotect
  while(1) {
    await trafficStatePoll();
  }
}());
```

如上的内容可以通过异步和函数式编程的方法简化:

```js
const traffic = document.getElementById('traffic');

function wait(time){
  return new Promise(resolve => setTimeout(resolve, time));
}

function setState(state){
  traffic.className = state;
}

async function start(){
  //noprotect
  while(1){
    setState('wait');
    await wait(1000);
    setState('stop');
    await wait(3000);
    setState('pass');
    await wait(3000);
  }
}
start();
```

- 判断是否是 4 的幂

1. 最简单的一种方式就是不断的除以4，如果最后等于1则是4的幂，否则则不是4的幂，原理比较简，但是性能不优秀

```js
function isPowerOfFour(int num) {
     //负数不可能是4的幂
     if (num <= 0)
         return false;
     //1是4的0次幂
     if (num == 1)
         return true;
     //如果不能够被4整除，肯定不是4的幂
     if (num % 4 != 0)
        return false;
    //如果能被4整除，除以4然后递归调用
    return isPowerOfFour(num / 4);
}
```

2. 二进制位运算实现，在32位的二进制中，只要有一个位置是1（不能是符号位），其他位置都是0，那么这个数就是2的幂，如果一个数是2的幂，并且二进制从右边数奇数位是1的一定是4的幂

```js
return num > 0 && (num & (num - 1)) == 0 && (num & 0x55555555) == num;
```

3. 基于我们是 js 二进制转字符串，正则判断
4. 最后我们可以使用公式来计算，我们来观察一下4的幂次方的一些特点，4的幂次方不好观察，我们来研究一下4的幂次方减1，研究这个特点之前一定要明白这样一条定律：任何连续的n个自然数的乘积一定能被n整除。如果一个数是2的幂，并且减1还能被3整除，那么这个数一定是4的幂：

```js
 return num > 0 && (num & (num - 1)) == 0 && (num - 1) % 3 == 0;
```

这个例子的道理是：我们可以针对不同语言的 api 选择不同的优化方式

- 洗牌算法

有几张牌张牌，用js来进行乱序排列，要保持公平性

1. 这里用sort方法，用随机数控制是不是交换，但是实际上结果是：越小的数值排布在前面的概率越大，因为这个算法，越在前面的数，交换到最后的概率是越低的，它每次交换到下一个位置的概率都是递减的，所以算法是不科学的

```js
const cards = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];

function shuffle(cards) {
  return [...cards].sort(() => Math.random() > 0.5 ? -1 : 1);
}

console.log(shuffle(cards));
```

2. 这里的思路是抽取随机一张牌，放在最后一张牌的后面，在剩下的牌里进行抽取，继续放到最后一张牌的后面。这样每张牌抽到每个位置的概率是一样的：

```js
const cards = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];

function shuffle(cards) {
  const c = [...cards];
  for(let i = c.length; i > 0; i--) {
    const pIdx = Math.floor(Math.random() * i);
    [c[pIdx], c[i - 1]] = [c[i - 1], c[pIdx]];
  }
  return c;
}
console.log(shuffle(cards));
```

3. 对于上述的方法，我们可以用生成器来做，每次抽取其中一张牌，把抽出的牌通过 yield 方法进行抽出去，做成一个可迭代对象，最后通过 ... 展开操作符进行展开。

```js
1const cards = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];

function * draw(cards){
    const c = [...cards];

  for(let i = c.length; i > 0; i--) {
    const pIdx = Math.floor(Math.random() * i);
    [c[pIdx], c[i - 1]] = [c[i - 1], c[pIdx]];
    yield c[i - 1];
  }
}

const result = draw(cards);
console.log([...result]);
```

这里例子是道理是：你需要先保证代码的正确性，再进行代码的优化

- 分红包的算法

 给定红包金额和红包数量，公平随机给红包分配金额数

1. 切西瓜：每次把金额分成两部分，挑选出大的切两份，在所有的里面找最大的，以此类推，每次分最大的红包，时间复杂度是 O( nm ) 

   这样的缺点是：这样分出来的都比较均匀，不够刺激

```js
function generate(amount, count){
  let ret = [amount];
  
  while(count > 1){
    //挑选出最大一块进行切分
    let cake = Math.max(...ret),
        idx = ret.indexOf(cake),
        part = 1 + Math.floor((cake / 2) * Math.random()),
        rest = cake - part;
    
    ret.splice(idx, 1, part, rest);
    
    count--;
  }
  return ret;
}
generateBtn.onclick = function(){
  let amount = Math.round(parseFloat(amountEl.value) * 100);
  let count = parseInt(countEl.value);
  
  let output = [];
  
  if(isNaN(amount) || isNaN(count) 
     || amount <= 0 || count <= 0){
    output.push('输入格式不正确！');
  }else if(amount < count){
    output.push('钱不够分')
  }else{
    output.push(...generate(amount, count));
    output = output.map(m => (m / 100).toFixed(2));
  }
  resultEl.innerHTML = '<li>' + 
                        output.join('</li><li>') +
                       '</li>';
}
```

2. 切竹子：假设 100 元，从1号到9999号（以分为单位），取10个位置切，更容易随机到更大的数

   利用生成器：

```js
function * draw(cards){
  const c = [...cards];

  for(let i = c.length; i > 0; i--) {
    const pIdx = Math.floor(Math.random() * i);
    [c[pIdx], c[i - 1]] = [c[i - 1], c[pIdx]];
    yield c[i - 1];
  }
}

function generate(amount, count){
  if(count <= 1) return [amount];
  const cards = Array(amount - 1).fill(0).map((_, i) => i + 1);
  const pick = draw(cards);
  const result = [];
  for(let i = 0; i < count; i++) {
    result.push(pick.next().value);
  }
  result.sort((a, b) => a - b);
  for(let i = count - 1; i > 0; i--) {
    result[i] = result[i] - result[i - 1];
  }
  return result;
}
```



## 总结

1. 写代码的时候要根据使用场景来评估一个代码的好坏
2. 不论是前端后端客户端，算法都是需要的，需要自己的算法意识和数学基础，来提高自己算法的效率，用对方法一个复杂的问题会变的很简单