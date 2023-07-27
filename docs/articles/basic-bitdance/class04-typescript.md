# class4 - TypeScript

TypeScript 是 JavaScript 的语法超集，它添加了静态类型，近几年的发展中，也受到诸多开发者的喜爱。Typescript 的社区逐渐壮大，日趋完善，为越来越多前端开发者提供服务，本节课将主要为解读 TypeScript 的优势及其主要使用的工具。

## 什么是TS

静态类型  vs 动态类型： 动态类型只有运行的时候才做类型匹配，静态类型只有编译完成后才能运行

弱类型 vs 强类型 ：弱类型执行时会进行隐式类型转行，强类型不能不同类型一切操作

ts 是静态类型，弱类型的语言，同时它是 js 的超集，支持一切js的特性和语法，支持你渐进式引入

## 基本语法

- 基本数据类型

TypeScript支持与JavaScript几乎相同的数据类型，此外还提供了实用的枚举类型方便我们使用。我们在定义一个变量的时候，在函数中传值的时候，都需要给出它的数据类型。

1. 布尔值：最基本的数据类型，就是简单的true/false值

```typescript
let isDone: boolean = false;
```

2. 数字：和JavaScript一样，TypeScript里的所有数字都是浮点数。 这些浮点数的类型是`number`。 除了支持十进制和十六进制字面量，Typescript还支持ECMAScript 2015中引入的二进制和八进制字面量。

```typescript
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

3. 字符串： 像其它语言里一样，我们使用`string`表示文本数据类型。 和JavaScript一样，可以使用双引号（`"`）或单引号（`'`）表示字符串；	你还可以使用*模版字符串*，它可以定义多行文本和内嵌表达式。 这种字符串是被反引号包围（`` `），并且`${ expr }`这种形式嵌入表达式

```typescript
let name: string = "bob";
name = "smith";
let name: string = `Gene`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ name }.
```

4. 数组：TypeScript像JavaScript一样可以操作数组元素。 有两种方式可以定义数组。 第一种，可以在元素类型后面接上`[]`，表示由此类型元素组成的一个数组，第二种方式是使用数组泛型，`Array<元素类型>`：

```typescript
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];
```

5. 元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。

```typescript
let x: [string, number];
x = ['hello', 10];
```

6. any ：有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用`any`类型来标记这些变量：

```typescript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

7. 空：`void`类型像是与`any`类型相反，它表示没有任何类型。一个`void`类型的变量只能为它赋予`undefined`和`null`，同时`undefined`和`null`两者各自有自己的类型分别叫做`undefined`和`null`。 默认情况下`null`和`undefined`是所有类型的子类型。 就是说你可以把`null`和`undefined`赋值给`number`类型的变量。

   声明为 `Null` 类型表示对象值缺失，比如你定义了一个值，不像给他如何的初值，那么它就是 null

   声明为 `undefined` 类型表示一个未定义的值，举个例子，你定义了一个数组，如果访问越界访问，那么得到的值就是 undefined

```typescript
let unusable: void = undefined;
let u: undefined = undefined;
let n: null = null;
let a: string = null;
let list: number[] = [1, 2, 3];
list[1000] //undefined
```

8. 对象 object，这和 js 的对象一致，不再赘述

类型断言：如果你觉得你比TypeScript更了解某个值的详细信息。 通常这会发生在你清楚地知道一个实体具有比它现有类型更确切的类型。通过*类型断言*这种方式可以告诉编译器某个数的类型：

```typescript
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;
//两种写法一样
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

- 定义变量

1. var 和 let

var 关键字定义 JavaScript 变量是常用的手段，看下面的例子：变量`x`是定义在*`if`语句里面*，但是我们却可以在语句的外面访问它。 这是因为`var`声明可以在包含它的函数，模块，命名空间或全局作用域内部任何位置被访问。

```typescript
function f(shouldInitialize: boolean) {
    if (shouldInitialize) {
        var x = 10;
    }
    return x;
}
f(true);  // returns '10'
f(false); // returns 'undefined'
```

当用`let`声明一个变量，它使用的是*词法作用域*或*块作用域*。块作用域变量在包含它们的块之外是不能访问的。它们不能在被声明之前读或写。 虽然这些变量始终“存在”于它们的作用域里，

```typescript
function f(input: boolean) {
    let a = 100;
    if (input) {
        // Still okay to reference 'a'
        let b = a + 1;
        return b;
    }
    // Error: 'b' doesn't exist here
    return b;
}
a++; // illegal to use 'a' before it's declared;
let a;
```

因为以上的不同，使用`var`声明时，它不在乎你声明多少次；你只会得到1个；`let`声明就不会这么宽松了。

```typescript
let x = 10;
let x = 20; // 错误，不能在1个作用域里多次声明`x`
```

并且我们也可以在其它函数内部访问相同的变量。如下，`g`可以获取到`f`函数里定义的`a`变量。 每当`g`被调用时，它都可以访问到`f`里的`a`变量。 即使当`g`在`f`已经执行完后才被调用，它仍然可以访问及修改`a`。

```javascript
function f() {
    var a = 10;
    return function g() {
        var b = a + 1;
        return b;
    }
}

var g = f();
g(); // returns 11;
```

所以在如下的情况下，会有超出预期的输出。如下： `setTimeout`在若干毫秒后执行一个函数，并且是在`for`循环结束后。 `for`循环结束后，`i`的值为`10`。 所以当函数被调用的时候，它会打印出`10`！

```javascript
for (var i = 0; i < 10 ; i++) {
    setTimeout(function() {console.log(i); }, 100 * i);
}
//10 10 10 10 10 ....
```

当`let`声明出现在循环体里时拥有完全不同的行为。 不仅是在循环里引入了一个新的变量环境，而是针对*每次迭代*都会创建这样一个新作用域。 这就是我们在使用立即执行的函数表达式时做的事：

```typescript
for (let i = 0; i < 10 ; i++) {
    setTimeout(function() {console.log(i); }, 100 * i);
}
//0 1 2 3 4 ....
```

正因为 let 对比 var 具有如上的优越性，所以在 ts 中习惯使用 let ，而不使用 var

2. const

`const` 声明是声明变量的另一种方式，它们拥有与`let`相同的作用域规则，但是它们被赋值后不能再改变，所有变量除了你计划去修改的都应该使用`const`：

```typescript
const kitty = {
    name: "Aurora",
    numLives: numLivesForCat,
}

// Error
kitty = {
    name: "Danielle",
    numLives: numLivesForCat
};
```

3. 解构

你可以像 js 中一样使用解构来获取数组，对象中的值，

```typescript
let input = [1, 2];
let [first, second] = input;
[first, second] = [second, first];
let [first, ...rest] = [1, 2, 3, 4];
console.log(first); // outputs 1
console.log(rest); // outputs [ 2, 3, 4 ]
let [, second, , fourth] = [1, 2, 3, 4];
```

你也可以解构对象

```javascript
let o = {
    a: "foo",
    b: 12,
    c: "bar"
}
let {a, b} = o;
```

- 函数

和JavaScript一样，TypeScript函数可以创建有名字的函数和匿名函数。函数类型包含两部分：参数类型和返回值类型。 当写出完整函数类型的时候，这两部分都是需要的。 

```typescript
function add(x: number, y: number): number {
    return x + y;
}
let myAdd = function(x: number, y: number): number { return x+y; };
```

TypeScript里的每个函数参数都是必须的。这不是指不能传递`null`或`undefined`作为参数，而是说编译器检查用户是否为每个参数都传入了值。 如果你不一定需要某个参数，在TypeScript里我们可以在参数名旁使用`?`实现可选参数的功能

```typescript
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

let result1 = buildName("Bob");  // works correctly now
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");  // ah, just right
```

在TypeScript里，我们也可以为参数提供一个默认值当用户没有传递这个参数或传递的值是`undefined`时

```typescript
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");    // works correctly now, returns "Bob Smith"
```

你想同时操作多个参数，或者你并不知道会有多少参数传递进来。你可以把所有参数收集到一个变量里：

```typescript
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}
let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

同一个函数提供多个函数类型定义，就是对函数进行重载，系统会根据你传入的参数来选择使用哪一个函数

```typescript
function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];

let pickedCard2 = pickCard(15);
```



## OOP特性

- 类

和 ES6 一样，我们可以使用 ts 创建 class，其中可以包含变量和方法，如下：我们声明一个`Greeter`类。这个类有3个成员：一个叫做`greeting`的属性，一个构造函数和一个`greet`方法。我们使用`new`构造了Greeter类的一个实例。 它会调用之前定义的构造函数，创建一个`Greeter`类型的新对象，并执行构造函数初始化它。

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}
let greeter = new Greeter("world");
```

在TypeScript里，我们可以使用常用的面向对象模式，这个例子展示了TypeScript中继承的一些特征，与其它语言类似。 我们使用`extends`来创建子类。你可以看到`Horse`和`Snake`类是基类`Animal`的子类，并且可以访问其属性和方法。包含constructor函数的派生类必须调用`super()`，它会执行基类的构造方法。

```typescript
class Animal {
    name:string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

类似 java 的概念我们可以使用修饰符来规定谁可以访问 class 内的变量， 

1. 当成员被标记成`private`时，它就不能在声明它的类的外部访问。

```typescript
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("Cat").name; // Error: 'name' is private;
```

2. `protected`修饰符与`private`修饰符的行为很相似，但有一点不同，`protected`成员在派生类中仍然可以访问

```typescript
class Person {
    protected name: string;
    constructor(name: string) { this.name = name; }
}

class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name)
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch());
console.log(howard.name); // error
```

3. 在TypeScript里，每个成员默认为`public`的。不仅在内部可以访问它，如何它的实例也可以在外部访问它

4. 你可以使用`readonly`关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

```typescript
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // error! name is readonly.
```

TypeScript支持getters/setters来截取对对象成员的访问。 它能帮助你有效的控制对对象成员的访问。如下的例子，我们可以在修改 fullName 的时候通过 set 判定是不是符合要求

```typescript
let passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

我们也可以创建类的静态成员，这些属性存在于类本身上面而不是类的实例上，如下的例子：这里我们使用`Grid.`来访问静态属性

```typescript
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

let grid1 = new Grid(1.0);  // 1x scale
let grid2 = new Grid(5.0);  // 5x scale

console.log(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
console.log(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```

抽象类是供其它类继承的基类。 他们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。 `abstract`关键字是用于定义抽象类和在抽象类内部定义抽象方法。抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。 抽象方法的语法与接口方法相似。 两者都是定义方法签名不包含方法体。 然而，抽象方法必须使用`abstract`关键字并且可以包含访问符。

```typescript
abstract class Animal {
    abstract makeSound(): void;
    move(): void {
        console.log('roaming the earch...');
    }
}
```

- 接口

 在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。如下的例子：`LabelledValue`接口就好比一个名字， 它代表了有一个`label`属性且类型为`string`的对象。 

```typescript
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。 可选属性在应用“option bags”模式时很常用，即给函数传入的参数对象中只有部分属性赋值了。带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个`?`符号

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});
```

一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名前用`readonly`来指定只读属性，你可以通过赋值一个对象字面量来构造一个`Point`。 赋值后，`x`和`y`再也不能被改变了。

```typescript
let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!
```

如何我们需要一个类型带有任意数量的其它属性，那么我们可以这样定义它：

```typescript
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

 除了描述带有属性的普通对象外，接口也可以描述函数类型。为了使用接口表示函数类型，我们需要给接口定义一个调用签名。对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配。

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string): boolean {
  let result = src.search(sub);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

与使用接口描述函数类型差不多，我们也可以描述那些能够“通过索引得到”的类型，比如`a[10]`或`ageMap["daniel"]`。如下的例子： 这个索引签名表示了当用`number`去索引`StringArray`时会得到`string`类型的返回值。共有支持两种索引签名：字符串和数字

```typescript
interface StringArray {
  [index: number]: string;
}
let myArray: StringArray;
myArray = ["Bob", "Fred"];
let myStr: string = myArray[0];
```

你可以将索引签名设置为只读，这样就防止了给索引赋值：

```typescript
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // error!
```

TypeScript也能够用它来明确的强制一个类去符合某种契约。你也可以在接口中描述一个方法，在类里实现它

```typescript
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

和类一样，接口也可以相互扩展，一个接口可以继承多个接口，创建出多个接口的合成接口。

```typescript
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

当接口继承了一个类类型时，它会继承类的成员但不包括其实现。 就好像接口声明了所有类中存在的成员，但并没有提供具体实现一样， 接口同样会继承到类的private和protected成员。 这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）。

```typescript
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control {
    select() { }
}
class TextBox extends Control {
    select() { }
}
class Image extends Control {
}
class Location {
    select() { }
}
```

## 高级类型

- 泛型

可以使用 泛型 来创建可重用的组件，一个组件可以支持多种类型的数据。 这样用户就可以以自己的数据类型来使用组件。例如，我们需要一种方法使返回值的类型与传入参数的类型是相同的。我们可以将传入的类型定义为 `T`，它帮助我们捕获用户传入的类型（比如：`number`），之后我们就可以使用这个类型。

```typescript
function identity<T>(arg: T): T {
    return arg;
}
```

我们定义了泛型函数后，可以用两种方法使用。 第一种是，传入所有的参数，包含类型参数；第二种方法更普遍。利用了*类型推论*，编译器会根据传入的参数自动地帮助我们确定T的类型：

```typescript
let output = identity<string>("myString");  // type of output will be 'string'
let output = identity("myString");  // type of output will be 'string'
```

我们有时候想操作某类型的一组值，并且我们知道这组值具有什么样的属性。例如，我们想访问`arg`的`length`属性，但是编译器并不能证明每种类型都有`length`属性，所以就报错了。相比于操作any所有类型，我们想要限制函数去处理任意带有`.length`属性的所有类型。 只要传入的类型有这个属性，我们就允许，就是说至少包含这一属性。 为此，我们需要列出对于T的约束要求。

我们可以创建一个包含`.length`属性的接口，使用这个接口和`extends`关键字还实现约束

```typescript
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
```

- 枚举

使用枚举我们可以定义一些有名字的数字常量。 枚举通过`enum`关键字来定义。

```typescript
enum Direction {
    Up = 1,
    Down,
    Left,
    Right
}
```

一个枚举类型可以包含零个或多个枚举成员。 枚举成员具有一个数字值，它可以是*常数*或是*计算得出的值* 当满足如下条件时，枚举成员被当作是常数：

- 不具有初始化函数并且之前的枚举成员是常数。 在这种情况下，当前枚举成员的值为上一个枚举成员的值加1。 但第一个枚举元素是个例外。 如果它没有初始化方法，那么它的初始值为`0`。
- 枚举成员使用常数枚举表达式初始化。 常数枚举表达式是TypeScript表达式的子集，它可以在编译阶段求值。 当一个表达式满足下面条件之一时，它就是一个常数枚举表达式：
  - 数字字面量
  - 引用之前定义的常数枚举成员（可以是在不同的枚举类型中定义的） 如果这个成员是在同一个枚举类型中定义的，可以使用非限定名来引用。
  - 带括号的常数枚举表达式
  - `+`, `-`, `~` 一元运算符应用于常数枚举表达式
  - `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `>>>`, `&`, `|`, `^` 二元运算符，常数枚举表达式做为其一个操作对象 若常数枚举表达式求值后为`NaN`或`Infinity`，则会在编译阶段报错。

```typescript
enum FileAccess {
    // constant members
    None,
    Read    = 1 << 1,
    Write   = 1 << 2,
    ReadWrite  = Read | Write
    // computed member
    G = "123".length
}
```

常数枚举只能使用常数枚举表达式并且不同于常规的枚举的是它们在编译阶段会被删除。 常数枚举成员在使用的地方被内联进来。 这是因为常数枚举不可能有计算成员。

```typescript
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]
//编译后生成的： [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

- 联合类型

偶尔你会遇到这种情况，一个代码库希望传入`number`或`string`类型的参数。 我们可以使用联合类型做为参数，联合类型表示一个值可以是几种类型之一， 我们用竖线（`|`）分隔每个类型：

```typescript
function padLeft(value: string, padding: string | number) {
    // ...
}
```

- 交叉类型

交叉类型与联合类型密切相关，但是用法却完全不同，一个交叉类型，例如`Person & Serializable & Loggable`，同时是`Person`*和*`Serializable`*和*`Loggable`。 就是说这个类型的对象同时拥有这三种类型的成员。 

```typescript
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U>{};
    for (let id in first) {
        (<any>result)[id] = (<any>first)[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            (<any>result)[id] = (<any>second)[id];
        }
    }
    return result;
}

class Person {
    constructor(public name: string) { }
}
interface Loggable {
    log(): void;
}
class ConsoleLogger implements Loggable {
    log() {
        // ...
    }
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();
```

- 类型别名

类型别名会给一个类型起个新名字。起别名不会新建一个类型 - 它创建了一个新*名字*来引用那个类型

```typescript
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    }
    else {
        return n();
    }
}
```

- symbol

自ECMAScript 2015起，`symbol`成为了一种新的原生类型，就像`number`和`string`一样。Symbols是不可改变且唯一的。一个Symbol类型的变量只是为了标记一块唯一的内存而存在的。因为这是 es6 的概念不多赘述，可以自己查阅文档

```typescript
let sym1 = Symbol();
let sym2 = Symbol("key"); // 可选的字符串key
```

当一个对象实现了[`Symbol.iterator`](https://www.runoob.com/manual/gitbook/TypeScript/_book/doc/handbook/Symbols.html#symboliterator)属性时，我们认为它是可迭代的，一些内置的类型如`Array`，`Map`，`Set`，`String`，`Int32Array`，`Uint32Array`等都已经实现了各自的`Symbol.iterator`。

`for..of`会遍历可迭代的对象，调用对象上的`Symbol.iterator`方法

```typescript
let someArray = [1, "string", false];

for (let entry of someArray) {
    console.log(entry); // 1, "string", false
}
```

`for..in`迭代的是对象的 *键* 的列表，而`for..of`则迭代对象的键对应的值。

```typescript
let list = [4, 5, 6];

for (let i in list) {
    console.log(i); // "0", "1", "2",
}

for (let i of list) {
    console.log(i); // "4", "5", "6"
}
```

## this

在JavaScript里（还有TypeScript），`this`关键字的行为与其它语言相比大为不同。

当JavaScript里的一个函数被调用时，你可以按照下面的顺序来推断出`this`指向的是什么（这些规则是按优先级顺序排列的）：

- 如果这个函数是`function#bind`调用的结果，那么`this`指向的是传入`bind`的参数
- 如果函数是以`foo.func()`形式调用的，那么`this`值为`foo`
- 如果是在严格模式下，`this`将为`undefined`
- 否则，`this`将是全局对象（浏览器环境里为`window`）

丢失`this`上下文的典型症状包括：

- 类的某字段（`this.foo`）为`undefined`，但其它值没有问题
- `this`的值指向全局的`window`对象而不是类实例对象（在非严格模式下）
- `this`的值为`undefined`而不是类实例对象（严格模式下）
- 调用类方法（`this.doBa()`）失败，错误信息如“TypeError: undefined is not a function”，“Object doesn't support property or method 'doBar'”或“this.doBar is not a function”

可以通过一些方法来保持`this`的上下文。

1. 代替TypeScript里默认的原型方法，你可以使用一个实例箭头函数来定义类成员

```typescript
class MyClass {
    private status = "blah";

    public run = () => { // <-- note syntax here
        alert(this.status);
    }
}
var x = new MyClass();
$(document).ready(x.run); // SAFE, 'run' will always have correct 'this'
```

2. Function.bind

```typescript
var x = new SomeClass();
// SAFE: Functions created from function.bind are always preserve 'this'
window.setTimeout(x.someMethod.bind(x), 100);
```

3. 使用本地胖箭头

```typescript
var x = new SomeClass();
someCallback((n, m) => x.doSomething(n, m));
```



## 模块与命名空间

- 模块

从ECMAScript 2015开始，JavaScript引入了模块的概念。TypeScript也沿用这个概念。

任何声明（比如变量，函数，类，类型别名或接口）都能够通过添加`export`关键字来导出。你也可以使用 as 来对导出的内容重命名

```typescript
export interface StringValidator {
    isAcceptable(s: string): boolean;
}

export const numberRegexp = /^[0-9]+$/;

class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export { ZipCodeValidator };
export { ZipCodeValidator as mainValidator };
```

我们经常会去扩展其它模块，并且只导出那个模块的部分内容， 重新导出功能并不会在当前模块导入那个模块或定义一个新的局部变量。

```typescript
export {ZipCodeValidator as RegExpBasedZipCodeValidator} from "./ZipCodeValidator";
```

模块的导入操作与导出一样简单。 可以使用以下`import`形式之一来导入其它模块中的导出内容。同样导入也可以重命名

```typescript
import { ZipCodeValidator } from "./ZipCodeValidator";
let myValidator = new ZipCodeValidator();
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
let myValidator = new ZCV()
```

一些模块会设置一些全局状态供其它模块使用。 这些模块可能没有任何的导出或用户根本就不关注它的导出。 使用下面的方法来导入这类模块：

```typescript
import "./my-module.js";
```

每个模块都可以有一个`default`导出。 默认导出使用`default`关键字标记；并且一个模块只能够有一个`default`导出。 需要使用一种特殊的导入形式来导入`default`导出。

```typescript
declare let $: JQuery;
export default $;

//另一个文件
import $ from "JQuery";
```

类和函数声明可以直接被标记为默认导出。 标记为默认导出的类和函数的名字是可以省略的。

```typescript
export default class ZipCodeValidator {
    static numberRegexp = /^[0-9]+$/;
    isAcceptable(s: string) {
        return s.length === 5 && ZipCodeValidator.numberRegexp.test(s);
    }
}

//另一个文件
import validator from "./ZipCodeValidator";
let myValidator = new validator();
```

模块设计的原则是如果仅导出单个 class 或 function，使用 export default，对用户来说这是最理想的。他们可以随意命名导入模块的类型，如果要导出多个对象，把它们放在顶层里导出，用户可以明确地列出导入的名字

```typescript
//导出单个
export default class SomeType {
  constructor() { ... }
}
//另一个文件
import t from "./MyClass";

//导出多个
export class SomeType { /* ... */ }
export function someFunc() { /* ... */ }
//另一个文件
import { SomeType, SomeFunc } from "./MyThings";
import * as myLargeModule from "./MyThings";
```

CommonJS和AMD都有一个`exports`对象的概念，它包含了一个模块的所有导出内容。TypeScript模块支持`export =` 语法以支持传统的 CommonJS 和AMD的工作流模型。

```typescript
let numberRegexp = /^[0-9]+$/;
class ZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;

//另一个文件
import zip = require("./ZipCodeValidator");
```

- 命名空间

随着代码量的增加，我们需要一种手段来组织代码，以便于在记录它们类型的同时还不用担心与其它对象产生命名冲突，我们可以定义一个命名空间，将所有有关联的代码放在同一个命名空间下

```typescript
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
    const lettersRegexp = /^[A-Za-z]+$/;
    /****/
}
```

命名空间可以做到，就算不在一个文件里，只要用命名空间包裹，他们还在一个命名空间下

```typescript
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
//另一个文件
/// <reference path="Validation.ts" />
namespace Validation {
    const lettersRegexp = /^[A-Za-z]+$/;
    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }
}
```

你需要通过 命名空间.内容 的形式来调用对于的内容

```typescript
namespace Shapes {
    export namespace Polygons {
        export class Triangle { }
        export class Square { }
    }
}

import polygons = Shapes.Polygons;
let sq = new polygons.Square(); // Same as "new Shapes.Polygons.Square()"
```

我们可以通过 `tsc --outFile` 把不同命名空间下的代码结合在一起。你也可以把所有依赖都放在HTML页面的`<script>`标签里。

```html
    <script src="Validation.js" type="text/javascript" />
    <script src="LettersOnlyValidator.js" type="text/javascript" />
    <script src="ZipCodeValidator.js" type="text/javascript" />
    <script src="Test.js" type="text/javascript" />
```

- 模块解析

模块解析就是指编译器所要依据的一个流程，用它来找出某个导入操作所引用的具体值。 假设有一个导入语句`import { a } from "moduleA"`; 为了去检查任何对`a`的使用，编译器需要准确的知道它表示什么，并且会需要检查它的定义`moduleA`。

TypeScript是模仿Node.js运行时的解析策略来在编译阶段定位模块定义文件，TypeScript在Node解析逻辑基础上增加了TypeScript源文件的扩展名（`.ts`，`.tsx`和`.d.ts`）。TypeScript在`package.json`里使用字段`"typings" `来表示类似 `"main"` 的意义。比如，有一个导入语句`import { b } from "./moduleB"`在`/root/src/moduleA.ts`里，会以下面的流程来定位`"./moduleB"`：

1. `/root/src/moduleB.ts`
2. `/root/src/moduleB.tsx`
3. `/root/src/moduleB.d.ts`
4. `/root/src/moduleB/package.json` (如果指定了`"typings"`属性)
5. `/root/src/moduleB/index.ts`
6. `/root/src/moduleB/index.tsx`
7. `/root/src/moduleB/index.d.ts`

## 工程配置

目前，我们经常在各类框架，项目中使用 ts ，如下是在工程项目中使用 ts 的简单讲解：

如果一个目录下存在一个`tsconfig.json`文件，那么它意味着这个目录是TypeScript项目的根目录。`tsconfig.json`文件中指定了用来编译这个项目的根文件和编译选项。

不带任何输入文件的情况下调用`tsc`，编译器会从当前目录开始去查找`tsconfig.json`文件，逐级向上搜索父目录。不带任何输入文件的情况下调用`tsc`，且使用命令行参数`--project`（或`-p`）指定一个包含`tsconfig.json`文件的目录，可以运行指定目录的文件。

```json
{
    "compileOnSave": true,
    "compilerOptions": {
        "module": "commonjs",
        "noImplicitAny": true,
        "removeComments": true,
        "preserveConstEnums": true,
        "outFile": "../../built/local/tsc.js",
        "sourceMap": true
    },
    "files": [
        "core.ts",
        "sys.ts",
        "types.ts",
        "scanner.ts",
        "parser.ts",
        "utilities.ts",
        "binder.ts",
        "checker.ts",
        "emitter.ts",
        "program.ts",
        "commandLineParser.ts",
        "tsc.ts",
        "diagnosticInformationMap.generated.ts"
    ],
    "include": [
        "src/**/*"
    ],
    "exclude": [
        "node_modules",
        "**/*.spec.ts"
    ]
}
```

`"compilerOptions"`可以被忽略，这时编译器会使用默认值。在这里查看完整的[编译器选项](https://www.runoob.com/manual/gitbook/TypeScript/_book/doc/handbook/Compiler Options.html)列表。`"files"`指定一个包含相对或绝对文件路径的列表。 `"include"`和`"exclude"`属性指定一个文件glob匹配模式列表。 在最顶层设置`compileOnSave`标记，可以让IDE在保存文件的时候根据`tsconfig.json`重新生成文件。

更多的配置信息请查看官方文档。https://www.runoob.com/manual/gitbook/TypeScript/_book/doc/handbook/Compiler%20Options.html

想要查看其他工具与 ts 整合的教程可以查看。 https://www.runoob.com/manual/gitbook/TypeScript/_book/doc/handbook/Integrating%20with%20Build%20Tools.html
