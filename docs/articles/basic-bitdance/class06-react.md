# class6 - React

本节课为前端框架 React 的基础课程讲解

## React的设计思路

- UI编程的特点

1. 状态更新的时候，`UI`不会自动更新，需要手动调用`DOM`接口进行更新
2. 欠缺基本的代码层面的封装和隔离，代码层面没有组件化
3. `UI`之间的数据依赖关系，需要手动维护，如果依赖链路长，则会遇到回调地狱

`React`的出现，就是为了解决这三大痛点，他做到了：

1. 状态更新，`UI`也会进行更新

2. 前端代码组件化，可复用，可封装

3. 状态之间的互相依赖关系，只需声明即可

- 响应式系统：

它使用了响应式编程的思想，通过监听事件，由消息驱动，需要有一个监控系统去关注事件，并对事件做出响应，更新`UI`界面：

![image-20230120094915053](/bit-dance/img/20.png)

- 组件化

可以用树状结构表示组件之间的关系：

![image-20230120100157567](/bit-dance/img/21.png)

1. 组件是组件的组合/原子组件

2. 组件内拥有自己的状态，外部不可见

3. 父组件可将状态传入组件内部

组件的设计：

1. 组件有 props （外部传入的）和 state（内部定义的） 两种状态
2. 组件的根据状态来返回一个 UI
3. 组件可以由其他组件拼装而成

- 状态归属和更新

React是**单项数据流**

如果想要两个组件的状态共享的话，他们的状态归属于最近的公共祖先，如上的例子中，当前价格属于根节点，因为所有的组件都需要可能影响到它。

当需要改变一个状态时，由于在`js`中，函数是一等公民，所以可以将函数也作为属性传递给子组件，那么就可以在`Root`组件中定义一个修改当前价格的函数，然后将这个函数传给子组件，当子组件需要修改当前价格时，就调用该函数即可。

## React的生命周期

![image-20230120102248566](/bit-dance/img/22.png)

1.Mounting 挂载时 ，就是初始化的时候，把我们定义组件对应的UI 挂载到真实的 dom 上

2.Updating 更新时 ，当状态更新的时候，怎么样更新组件，重新渲染再挂载上去

3.Unmounting 销毁时

## React （Hooks） 的写法

- 类组件和函数组件

根据组件的定义方式，可以分为：函数组件(Functional Component )和类组件(Class Component)；

类组件，顾名思义，也就是通过使用`ES6`类的编写形式去编写组件，该类必须继承`React.Component`，如果想要访问父组件传递过来的参数，可通过`this.props`的方式去访问，constructor 的存在是为了让我们获取 this，这是一个固定写法 ：

```javascript
class Welcome extends React.Component {
  constructor(props) {
    super(props)
  }
  render() {
    return <h1>Hello, {this.props.name}</h1>
  }
}
```

函数组件，顾名思义，就是通过函数编写的形式去实现一个`React`组件，是`React`中定义组件最简单的方式，函数第一个参数为`props`用于接收父组件传递过来的参数：

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

在 Hooks 出现之前，函数组件都是无状态组件，不能拥有自己的状态，hooks 的出现使得函数组件成为了主流，以下是几个常用的 hooks ：

- useState

`useState`可以用来定义一个状态。`useState`返回的是一个数组，第一个是当前状态的实际值，第二个用于更改该状态的函数，类似于`setState`。更新函数与`setState`相同的是都可以接受值和函数两种类型的参数，与`useState`不同的是，更新函数会将状态替换(replace)而不是合并(merge)。

```javascript
import React, { useState } from 'react'

function Example() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <span>{count}</span>
      <button onClick={()=> setCount(count + 1)}>+</button>
      <button onClick={() => setCount((count) => count - 1)}>-</button>
    </div>
  );
}
```

函数组件中如果存在多个状态，既可以通过一个`useState`声明对象类型的状态，也可以通过`useState`多次声明状态。

```javascript
// 声明对象类型的状态
const [count, setCount] = useState({
    count1: 0,
    count2: 0
});

// 多次声明
const [count1, setCount1] = useState(0);
const [count2, setCount2] = useState(0);
```

- useEffect

在函数式思想的React中，生命周期函数是沟通函数式和命令式的桥梁，你可以在生命周期中执行相关的副作用(Side Effects),例如: 请求数据、操作DOM等。React提供了useEffect来处理副作用。

```javascript
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`
    return () => {
      console.log('clean up!')
    }
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

我们会发现每次组件更新时，useEffect中的回调函数都会被调用，因此我们可以认为useEffect是componentDidMount和componentDidUpdate结合体，

`useEffect`为我们提供了第二个参数，如果第二个参数传入一个数组，仅当重新渲染时数组中的值发生改变时，`useEffect`中的回调函数才会执行。因此如果我们向其传入一个空数组，则可以模拟生命周期`componentDidMount`。

```javascript
//仅执行一次
useEffect(() => {
  document.title = `You clicked ${count} times`
  return () => {
    console.log('clean up!')
  }
},[]);

//只在count变化时调用
useEffect(() => {
  document.title = `You clicked ${count} times`
  return () => {
    console.log('clean up!')
  }
},[count]);
```

你可以在useEffect中定义 return 方法，它只在组件被销毁之前才会执行，常用于清理以下遗留垃圾，比如订阅或计时器 ID 等占用资源的东西。相当于生命周期的 componentWillUnMount：

```javascript
 useEffect(()=>{
    console.log('我执行了')
    return ()=>{
      console.log('我销毁了')
    }
  },[])
```

要注意的是 useEffect 的函数会在组件渲染到屏幕之后执行
而 useLayoutEffect 则是在DOM结构更新后、渲染前执行，相当于有一个防抖效果，他们的用法是一样的，不一样是只是执行的时机

- 父子组件交互

react 中，父组件可以把状态或者函数方法传递给子组件：

```javascript
//父组件传参
<Hearders name={name}  />

//子组件获得参数
function Hearders(props) {
    const {name} =props
}
```

父组件也可以传递一个方法给子组件，子组件可以通过这个方法来修改子组件的值：

```javascript
//父组件
const Parent = () => {
    const onClick = (value) => {
        console.log(value,'点击了')
    }
    return(
        <div>
            <Child
                click={onClick}
            />
        </div>
    )
}

//子组件
const Child = (props) => {
    const handleClick = (value) => {
        props.click(value)
    }
    return(
        <div onClick={()=>{handleClick(1)}}>
            子组件
        </div>
    )
}
```

借助Hook `useContext`可以帮助我们跨越组件层级直接传递变量，实现数据共享。

```javascript
import React,{useContext, useState, createContext} from 'react';
import {Button} from 'antd';
import '../../App.css';
 
const CountContext = createContext();
 
const TestContext = () =>{
    const [count, setCount] = useState(0);
    return(
      <div>
          <p>父组件点击次数：{count}</p>
          <Button type={"primary"} onClick={()=>setCount(count+1)}>点击+1</Button>
          <CountContext.Provider value={count}>
            <Counter/>
          </CountContext.Provider>
      </div>
  )
};
```

不止在子组件，在子组件的下一级孙子组件，再下一级中，都可以获取到响应的值，只要是被 Context.Provider  包裹的内容中，都可以如下方法使用 Context 里的值：

```javascript
const CountContext = createContext();
const Counter = () => {
    const count = useContext(CountContext);
    return (
        <div>
            <p>子组件获得的点击数量：{count}</p>
        </div>
    );
};
```

- ### useRef

[useRef ](https://so.csdn.net/so/search?q=useRef&spm=1001.2101.3001.7020)可以用来拿到 dom 节点的引用，拿到引用后可以进行一系列操作

```javascript
function Example() {
    const inputEl = useRef();
    const onButtonClick = () => {
        inputEl.current.focus();
    };
    return (
        <>
            <input ref={inputEl} type="text" />
            <button onClick={onButtonClick}>Focus the input</button>
        </>
    );
}
```

`useRef` 也可以接受一个默认值，并返回一个含有`current`属性的可变对象，该可变对象会将持续整个组件的生命周期。它有什么用处呢，例子如下：

在like为6的时候, 点击 alert , 再继续增加like到10, 弹出的值为 6, 而非 10.当我们更改状态的时候，React会重新渲染组件，每次的渲染都会拿到独立的like值，并重新定义个handleAlertClick函数，每个handleAlertClick函数体里的like值也是它自己的，所以当like为6时，点击alert，触发了handleAlertClick，此时的like是6，哪怕后面继续更改like到10，但alert时的like已经定下来了。可见不同渲染之间无法共享state状态值

```javascript
import React, { useState } from "react";
const LikeButton: React.FC = () => {
    const [like, setLike] = useState(0)
    function handleAlertClick() {
        setTimeout(() => {
            alert(`you clicked on ${like}`) 
            //形成闭包，所以弹出来的是当时触发函数时的like值
        }, 3000)
    }
    return (
        <>
            <button onClick={() => setLike(like + 1)}>{like}赞</button>
            <button onClick={handleAlertClick}>Alert</button>
        </>
    )
}
export default LikeButton
```

采用useRef，在like为6的时候, 点击 alert , 再继续增加like到10, 弹出的值为10。useRef 在更新的时候不会使得组件重新渲染， useRef 每次都会返回相同的引用

```javascript
import React, { useRef } from "react";
const LikeButton: React.FC = () => {
  // 定义一个实例变量
  let like = useRef(0);
  function handleAlertClick() {
    setTimeout(() => {
      alert(`you clicked on ${like.current}`);
    }, 3000);
  }
  return (
    <>
      <button
        onClick={() => {
          like.current = like.current + 1;
        }}
      >
        {like.current}赞
      </button>
      <button onClick={handleAlertClick}>Alert</button>
    </>
  );
};
export default LikeButton;
```

`useImperativeHandle`用于自定义暴露给父组件的`ref`属性。需要配合`forwardRef`一起使用。

```javascript
function Example(props, ref) {
    const inputRef = useRef();
    useImperativeHandle(ref, () => ({
        focus: () => {
            inputRef.current.focus();
        }
    }));
    return <input ref={inputRef} />;
}

export default forwardRef(Example);

class App extends Component {
  constructor(props){
      super(props);
      this.inputRef = createRef()
  }
  
  render() {
    return (
        <>
            <Example ref={this.inputRef}/>
            <button onClick={() => {this.inputRef.current.focus()}}>Click</button>
        </>
    );
  }
}

```

-   useCallback 和 useMemo

都是react可用于性能优化的内置hooks。两者的区别在于：useCallback缓存的是一个函数，而useMemo缓存的是计算结果。

```javascript
// useCallback
// 第一个参数是一个回调函数，useCallback会缓存这个函数，返回缓存的回调函数
// 第二个参数是依赖项，只有当依赖项改变时，才会重新创建这个函数
const memorizedCallback = useCallback(()=>{
    doSomething(a,b);
},[a,b])
 
// useMemo
// 第一个参数是一个函数，useMemo会缓存函数运行返回的值，返回缓存的值
// 第二个参数是依赖项，只有当依赖改变时，才会重新计算这个值
const memorizedValue = useMemo(()=>computeValue(a,b),[a,b])
```

他们的用处是：在函数式组件中，每次UI的变化，都是通过重新执行整个函数来完成的，这和传统的类组件有很大区别：函数组件中并没有一个直接的方式在多次渲染之间维持一个状态。在重新执行整个函数组件的过程中，其中的函数和引用类型的变量会创建新的（指向新的引用），函数组件在重新渲染前后，其中函数和引用类型变量是不相等的，这又会导致其他非必要的重新渲染。

如下：当Counter组件因为其他数据（非count）发生变化而导致重新渲染的时候，重新执行整个Counter函数，会创建新的 handleIncrement 函数,而子组件 Button 会由于 props-handleClick 传入的 handleIncrement 函数改变而重新渲染，但其实这个渲染是不必要的，因为只有在count发生变化时，才应该导致Button组件的渲染。

```javascript
// 需要做到：只有当count发生变化时，才需要重新定一个回调函数-useCallback
function Counter() {
  const [count, setCount] = useState(0);
  const handleIncrement = useCallback(
      () => setCount(count + 1),
      [count]  
  )  
// 只有当依赖项count改变时，才会重新生成函数，不然都是返回的缓存的回调函数，不会触发Button子组件的重绘
  return <Button handleClick={handleIncrement}/>
}
```

- 自定义 hooks

React 允许我们创建自定义Hook来封装共享状态逻辑。所谓的自定义Hook是指以函数名以`use`开头并调用其他Hook的函数。

```javascript
// 自定义一个hook  功能判断当前的网络情况
// 函数名要以use开头
// 函数中必须要用到内置hook函数
const useOnline = () => {
  const [online, setOnline] = useState(navigator.onLine)

  // 让它在第1次挂载时执行
  useEffect(() => {
    const onlineFn = () => setOnline(true)
    const offlineFn = () => setOnline(false)

    // js提供的监听事件
    window.addEventListener('online', onlineFn, false)
    window.addEventListener('offline', offlineFn, false)

    return () => {
      window.removeEventListener('online', onlineFn, false)
      window.removeEventListener('offline', offlineFn, false)
    }
  }, [])
  return online
}


const App = () => {
  const online = useOnline()
  return (
    <div>
      {
        online
          ?
          <div style={{ color: 'green' }}>在线</div>
          :
          <div>离线</div>
      }
    </div>
  );
}
```

## React的实现原理

- 虚拟DOM

React 使用 JavaScript 对象表示 DOM 信息和结构，当状态变更的时候，重新渲染这个 JavaScript 的对象结构。这个 JavaScript 对象称为virtual dom；

使用它的原因是 DOM 操作很慢，轻微的操作都可能导致页面重新排版，非常耗性能。相对于DOM对象，js对象处理起来更快，而且更简单。通过diff算法对比新旧vdom之间的差异，可以批量的、最小化的执行 dom 操作，从而提高性能。

- diff 算法

   diff算法的本质就是：找出两个对象之间的差异，目的是尽可能做到节点复用。传统的 diff 算法遍历整个结构逐一对比，效率很低，React用三大策略将 O(n3) 复杂度转化为 O(n) 复杂度

1. tree diff 

   React 通过 updateDepth 对 Virtual DOM 树进行层级控制。对树分层比较，两棵树只对同一层次节点进行比较。如果该节点不存在时，则该节点及其子节点会被完全删除，不会再进一步比较。只需遍历一次，就能完成整棵DOM树的比较。

2. component diff 

   React对不同的组件间的比较：同一类型的两个组件，按原策略（层级比较）继续比较Virtual DOM树即可，同一类型的两个组件，组件A变化为组件B时，可能Virtual DOM没有任何变化，如果知道这点（变换的过程中，Virtual DOM没有改变），可节省大量计算时间，所以用户可以通过 shouldComponentUpdate() 来判断是否需要判断计算。不同类型的组件，将一个（将被改变的）组件判断为dirtycomponent（脏组件），从而替换整个组件的所有节点。

3. element diff 

    当节点处于同一层级时，diff提供三种节点操作：删除、插入、移动：组件 C 不在集合（A,B）中，需要插入；组件 D 在集合（A,B,D）中，但 D的节点已经更改，不能复用和更新，所以需要删除 旧的D ，再创建新的。组件D之前在集合（A,B,D）中，但集合变成新的集合（A,B）了，D 就需要被删除。组件D已经在集合（A,B,C,D）里了，且集合更新时，D没有发生更新，只是位置改变，如新集合（A,D,B,C），D在第二个，无须像传统diff，让旧集合的第二个B和新集合的第二个D 比较，并且删除第二个位置的B，再在第二个位置插入D，而是 （对同一层级的同组子节点） 添加唯一key进行区分，移动即可。

## React 状态管理库

状态管理库的就是将转换抽取到 UI 外部进行统一管理

- redux

由于react的单向数据流问题，导致state状态传递和复用十分困难。比如一个组件向兄弟组件传递信息时，需要先传入父组件，再传到兄弟组件，十分的不方便。或者在不太相关的一个A组件中，使用B组件的状态，都是难以实现的。

redux想出一个办法：**将所有需要复用的状态集中存放在一起，就可以在任意组件中调用需要的状态。** 而存放这些state的一个集中的库，我们就叫它`store`。

```javascript
import { createStore } from 'redux'
const store = createStore(reducer)
```

action 指的是视图层发起的一个操作，告诉 Store 我们需要改变。比如用户点击了按钮，我们就要去请求列表，列表的数据就会变更。每个 action 必须有一个 type 属性，这表示 action 的名称，然后还可以有一个 payload 属性，这个属性可以带一些参数，用作 Store 变更：

```javascript
const action = {
  type: 'ADD_ITEM',
  payload: 'new item', // 可选属性
}
```

Action不会自己主动发出变更操作到Store，所以这里我们需要一个叫dispatch的东西，它专门用来发出action，在redux里面，store.dispatch()是 View发出 Action 的唯一方法

```javascript
store.dispatch({
  type: 'ADD_ITEM',
  payload: 'new item', // 可选属性
})
```

当 dispatch 发起了一个 action 之后，会到达 reducer，这个reducer就是用来计算新的store的，reducer接收两个参数：当前的state和接收到的action，然后它经过计算，会返回一个新的state：

```javascript
const reducer = function(prevState, action) {
  ...
  return newState;
};
```

下面是一个完整的例子：

store.js文件

```javascript
//该文件专门用于暴露一个store对象，整个应用只有一个store对象

//引入createStore,，专门用于创建redux中最核心的store对象
import {createStore, applyMiddleware} from 'redux';
//引入为Count组件服务的reducer
import countReducer from './count_reducer'
//引入redux-thunk，用于支持异步action
import thunk from 'redux-thunk'
const store = createStore(countReducer, applyMiddleware(thunk))
//暴露store
export default store
```

constant.js

```javascript
/*
 该模块是用于定义action对象中type类型的常量值，目的只有一个：便于管理的同时防止在书写单词的时候，出现错误
*/

export const INCREMENT = 'increment'
export const DECREMENT = 'decrement';
```

count_reducer.js

```javascript
/*
1、该文件是用于创建一个Count组件服务的reducer，reducer的本质就是一个函数
2、reducer函数会接到两个参数，分别为：之前的状态（preState）,动作对象（action）
3、reducer被第一次调用时，是store自动触发的，传递的preState是undefined,传递的action是类似于：{
 type: '@@REDUX/INIT_a.2.b.4'
}
*/
const {INCREMENT, DECREMENT} from './constant'
const initState = 0;//初始化状态，推荐写法

export default function countReducer (preState = initState, action) {
//if(preState === undefined) preState = 0
//从action对象中获取：type、data
 const { type, data } = action
 //根据type决定如何加工数据
 switch (type) {
  case INCREMENT://如果是加
    return preState + data;
   case DECREMENT://如果是减
    return preState - data;
   default: 
    return preState
 }
}
```

count_action.js

```javascript
/*
 该文件专门为Count组件生成action对象
*/

function createIncrementAction(data) {
 return {
  type:'increment',
  data
 }
}

function createDecrementAction(data) {
 return {
  type:'decrement',
  data
 }
}

//改造之后
const {INCREMENT, DECREMENT} from './constant'

//同步action，就是指action的值为Object类型的一般对象
export const createIncrementAction = data => ({type:INCREMENT,data})
export const createDecrementAction = data => ({type:DECREMENT,data})

//异步action，就是指action的值为函数,异步action中一般都会调用同步action，异步action不是必须要用的。
export const createIncrementAsyncAction = (data, time) => {
 return (dispatch) => {
  setTimeout(() => {
   //函数体
   dispatch(createDecrementAction(data));
  }，time)
 }
}
```

引入：

```javascript
//引入store，用于获取redux中保存的状态
import store from '../../redux/store'
//引入actionCreator专门用于创建action对象
import {createIncrementAction,createDecrementAction,createIncrementAsyncAction} from '../../redux/count_action'

//加法
increment = () => {
 const { value } = this.selectNumber;
 //store.dispatch({type:'increment', date : value * 1});
 store.dispatch(createIncrementAction(value * 1));
}
//减法
decrement = () => {
 const { value } = this.selectNumber;
 //store.dispatch({type:'decrement', date : value * 1});
 store.dispatch(createDecrementAction(value * 1));
}

incrementAsync = () => {
 const { value } = this.selectNumber;
 store.dispatch(createIncrementAsyncAction(value * 1 , 500))
}

render() {
 return (
   <div>
     <h1>当前和为: {store.getState()}</h1>
   </div>
 )
}
```

监听redux变化

```javascript
import React from "react";
import ReactDOM from "react-dom";
import App from './App';
import store from './store/store';
ReactDOM.render(<App/>,document.getElementById('root'))
// 在这里需要明确的是：redux只是一个状态的管理机制，它不会自动的触发页面的更新，需要我们自己去写
store.subscribe(() => ReactDOM.render(<App/>,document.getElementById('root')))
```

- useReducer

在React hooks 中，可以使用 useReducer 作为状态管理的工具，接收两个参数：第一个参数是reducer函数，没错就是我们上一篇文章介绍的。第二个参数是初始化的state。返回值为最新的state和dispatch函数（用来触发reducer函数，计算对应的state）。

```javascript
    // 官方 useReducer Demo
    // 第一个参数：应用的初始化
    const initialState = {count: 0};

    // 第二个参数：state的reducer处理函数
    function reducer(state, action) {
        switch (action.type) {
            case 'increment':
              return {count: state.count + 1};
            case 'decrement':
               return {count: state.count - 1};
            default:
                throw new Error();
        }
    }

    function Counter() {
        // 返回值：最新的state和dispatch函数
        const [state, dispatch] = useReducer(reducer, initialState);
        return (
            <>
                // useReducer会根据dispatch的action，返回最终的state，并触发rerender
                Count: {state.count}
                // dispatch 用来接收一个 action参数「reducer中的action」，用来触发reducer函数，更新最新的状态
                <button onClick={() => dispatch({type: 'increment'})}>+</button>
                <button onClick={() => dispatch({type: 'decrement'})}>-</button>
            </>
        );
    }
```



