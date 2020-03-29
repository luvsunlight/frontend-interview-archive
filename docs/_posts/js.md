## 函数的三种定义方式

<details>
<summary>查看解析</summary>
1.函数声明

```
function foo() {}
function () {}
() => {}
```

2.函数字面量

```
var foo = function() {}
```

3.构造函数

```
const add = new Function('a', 'b', 'return a + b')
```

4.三种方法的比较

* 构造函数的方法不推荐，因为会导致解析两次代码
* 函数声明的方式有预解析，并且使其在执行任何代码前可用
* 如果是`var foo = function bar() { .. }`这种形式，bar的声明会消失

> js代码运行有两个阶段，编译和执行。对于像`var a = 2`这样的语句，会被分为两个部分，一个是`var a`，这个声明会被提升，还有一个是`a = 2`这个只有在运行到代码处才会被执行。对于函数直接量也是这样，函数名foo会被提升，但是foo的内容只有在运行时才会被赋予，但如果是函数声明式，它会比变量提升更早，并且允许我们再执行任何代码前都可以执行该函数

```
foo()
var foo = 2
foo = function() {
    console.log(1)
}
function foo() {
    console.log(2)
}
foo()

// 2
// 1
```
</details>

## 判断this的指向

请分析如下代码输出结果

```
var number = 10;
function fn() {
  console.log(this.number);
}
var obj = {
  number: 2,
  show: function(fn) {
    this.number = 3;
    fn();
    arguments[0]();
  }
};
obj.show(fn);
```

<details>
<summary>查看解析</summary>
js中this有四种绑定形式

* 默认绑定，浏览器环境中它被绑定在全局变量里。node环境中它被绑定为{}
* 隐式绑定

```
var foo = {
    a: 3,
    bar: function() {
        console.log(this.a)
    }
}
foo.bar()
```

* 显式绑定


```
var bar = function() {
    console.log(this.a)
}
var foo = {a: 3}
bar.call(foo)
```

* new 

当我们显式绑定的上下文是undefined或者null时，实际上应用的还是默认绑定。除此之外，还有箭头函数的特殊情况

在本例中，很显然第一个`fn()`是默认绑定，它不符合任何一种情况

</details>

## 遍历一个对象

<details>
<summary>查看解析</summary>

| 方法 | 特性 |
| --- | --- |
| for..in | 遍历自身和继承的可枚举属性(不包含Symbol) |
| for..of |  |
| Object.keys | 遍历自身（不包括继承）所有可枚举属性（不包含symbol） |
| Object.getOwnPropertyNames | 遍历自身的所有可枚举和不可枚举的属性（不包括symbol） |
| Reflect.ownKeys | 遍历自身的所有可枚举和不可枚举的属性（包括symbol） |
| Reflect.enumerable | 返回一个Iterator对象，遍历自身和继承的所有可枚举属性(不包含symbol) |

* 只有Object.getOwnPropertySymbols和Reflect.ownKeys可以拿到symbol属性
* 只有Reflect.ownKeys可以拿到不可枚举的属性

</details>

## 异步加载js的方法有哪些

<details>
<summary>查看解析</summary>
1. script里写入defer或者async

defer延迟，该属性表示脚本在执行时不会影响，全部被延迟到整个页面解析完毕之后再运行（立即下载，但延迟执行）。h5规范要求延迟的脚本需要按顺序执行，并且在window.onload之前执行完毕，但是事实并非总是如此（见红宝书p13），所以最好`只包含一个延迟文本`。并且defer只适用于外部脚本，会自动忽略掉具有defer属性的内嵌脚本。

asnyc异步，同样也只适用于外部脚本。但是它没有整个页面解析完后才执行的规矩，它是立马异步执行。不过异步的代码之前不保证相互顺序。

总结，不管是延迟还是异步，都不能绝对保证代码的执行顺序，所以最好代码里不要包含DOM操作，更不要存在先后依赖。两者的区别在于defer是等待页面解析完再执行脚本，而asnyc是立马异步执行脚本

![](https://image-static.segmentfault.com/28/4a/284aec5bb7f16b3ef4e7482110c5ddbb_articlex)


2.动态创建脚本

只有在`document.body.append(script)`才会执行该脚本

3.xhr+eval异步加载和执行脚本

</details>

## 谈一谈JS中的异步编程

<details>
<summary>查看解析</summary>

从脚本层面来说，首先可以在script标签上加入defer或者async的tag

然后是JS运行时中的异步编程，包含回调，事件监听，Promise，生成器，async

> 生成器，generator

生成器的特点是核心部分由协程完成，协程即比线程更细粒度的运行机制，一个线程上可以有多个协程，但是只能同时运行一个线程，协程之与线程就好像接力赛跑

生成器的实现不多赘述，这里只讲最核心的部分

* 首先要区分`生成器`和`迭代器`的区别，然后`迭代器`内部可以通过`yield`关键字将程序执行的控制权转交出去，然后再在外部通过`迭代器`的`next`方法取回控制权
* 然后要搞清楚迭代器yield操作符的作用机制，它会运行yield操作符后的操作数，并取得其返回值，将这个返回值作为返回的一部分返回给主线程，每一次调用next方法时，其返回值格式为{value: xxx, done: false/true}
* yield关键字具有双向通信功能，比如yield 100，这是第一步，可以将100这个直接量传给主线程，然后主线程通过g.next(50)即赋予了`field 100`这个表达式以50的值，要注意`var a = yield 100`，如果主线程没有传参，a是不会等于100的（这一点和async不一样，async是做了封装的）
* 迭代器内部也可以做错误处理，抛出的错误通过迭代器的`throw`方法来完成回调
* 迭代器的缺点就是需要不停地调用及传值，很多时候我们都会封装一个thunk函数来run一个迭代器

> await和async

从表现上看，async和yield很像，它们都支持让异步的操作以同步的形式编写，就连形式也很像，无非是把*换成了async，把yield换成了await，那么这两者有什么区别呢，或者说async有什么特点呢

* asnyc可以将其理解成Promise+async的结合，并且自带执行器，generator还需要co或者thunk来保证异步函数的执行返回，async不需要
* 在协程的控制权交接这一点上，两者比较接近，await先对其操作数进行求值，求完的值保留下来，但是剩下的部分以微任务的形式进任务队列
* 生成器中是通过调用next方法来转移主线程的控制权，而async中是通过异步函数的决议来进行

</details>

## 说一说你对js执行上下文栈和作用域链的理解

<details>
<summary>查看解析</summary>
作用域，实际上就是js引擎根据变量名获取对应值权限的机制。作用域分两种词法作用域和动态作用域，词法作用域是根据书写代码时变量和函数声明的位置来决定的，而动态作用域则是会在函数运行的过程中动态改变作用域。（with和eval可以修改词法作用域，但是影响性能和增加不确定性，不推荐）
</details>

## 可迭代对象有哪些特点

<details>
<summary>查看解析</summary>

一个数据结构，只要具有`Symbol.iterator`属性，那么就认为其是可以迭代的

可迭代对象的特点

* 具有Symbol.iterator属性
* 可以使用for...of进行循环
* 可以通过Array.from被转换为数组

原生的具有Iterator接口的数据结构

* Array
* Map
* Set
* String
* TypedArray
* arguments
* NodeLists对象

</details>

## 原型链的理解

<details>
<summary>查看解析</summary>
简单的原型链，首先要说明的是几乎所有对象都有一个内置属性[[prototype]]，这个属性在es5之前是无法访问的，部分浏览器用__proto__属性来提供了这个接口，更安全的做法是Object.getPrototypeOf(obj)，关于这个属性更多的方法还有isPrototypeOf,setPrototypeOf等。

prototype链的作用就是当一个对象找不到某个属性时，它可以从原型链中寻找

```
function Foo() {}
Foo.prototype.name = "123"
var obj = new Foo()
```

关于Foo.prototype的理解，我们称之为原型对象，当我们使用new操作符调用原函数时，我们就创建了该对象并且将其绑定在了Foo.prototype上，调用函数返回的对象的[[prototype]]属性就绑定到了Foo.prototype上。但是我们要知道，Foo对象的[[prototype]]属性链接的并不是Foo.prototype

最基础的原型链由三个部分构成，构造函数Foo，Foo.prototype,和Foo构造出来的实例对象obj.当我们创建函数Foo的时候，其Foo.prototype就自带属性constructor指向Foo，但是这种链接是不稳定的，因为该属性并非只读，而是不可遍历，我们可以随时修改该属性

```
Foo.prototype === Foo.prototype
Foo.prototype.constructor = Foo

obj.__proto__ === Foo.prototype // true
Object.getPrototypeOf(obj) === Foo.prototype // true
```

然后我们引入更复杂的原型链系统，说起来复杂，但其实只要把握好三点

* __proto__永远是实例指向原型对象
* 任何对象的原型链顶端都是Object.prototype
* 任何函数都是Function的实例对象

![](http://images2015.cnblogs.com/blog/857465/201703/857465-20170305142000048-2065062538.gif)

在更复杂的原型链系统中，我们新增如下规律

* `{}.__proto__ === Object.prototype`.普通对象[[prototype]]就是指向Object.prototype
* `new Function().__proto__ === Function.prototype`.任何函数对象的[[prototype]]都是指向Function.prototype
* `Foo.prototype.__proto__ === Object.prototype`.因为它不是函数对象，只是普通对象，普通对象的[[prototype]]就是指向Object.prototype
* `Function.prototype.__proto__ === Object.prototype`.因为Function.prototype是原型对象，它不是函数对象，只是普通对象，普通对象的[[prototype]]就是指向Object.prototype
* `Object.__proto__ === Function.prototype`.因为Object是函数对象，所以指向Funciton.prototype
* `Function.__proto__ === Function.prototype`.因为Function是函数对象，所以指向Funciton.prototype
* `Foo.__proto__ === Function.prototype`.因为它是函数对象，函数对象的[[prototype]]就是指向Function.prototype

当对象没有被访问的属性时，我们就会从其__proto__对象中寻找，我们使用hasOwnProperty方法来判断该对象自己是否具有该属性

</details>

## JS的数据类型分类和判断

<details>
<summary>查看解析</summary>
JS一共7中基本类型

* Undefined
* Null
* Boolean
* Number
* String
* Object
* Symbol

判断类型有两个方法，typeof和instanceof

typeof => undefined, boolean, number, string, object, symbol, null的typeof的结果是object，这是typeof的机制导致的，null的地址前几位是0，则判断为object

instanceof则为判断对象/直接量和构造函数的关系


| 对象/字面量          | 构造函数    | 返回结果 |
|-----------------|---------|------|
| new Numebr(1)   | Number  | true |
| new String()    | String  | true |
| new Boolean()   | Boolean | true |
| var bar = Foo() | Foo     | true |
| [1, 2]          | Array   | true |

</details>

## 回流和重绘

<details>
<summary>查看解析</summary>
回流也叫重排，即我们对DOM的修改引发了DOM几何尺寸的变化，浏览器需要重新计算元素的几何属性，这个过程叫重排

重绘即样式的修改，整体布局不发生变化

常见的会导致回流的元素

* 常见的几何属性有 width、height、padding、margin、left、top、border 等等。
* 最容易被忽略的操作：获取一些需要通过即时计算得到的属性,当你要用到像这样的属性：offsetTop、offsetLeft、 offsetWidth、offsetHeight、scrollTop、scrollLeft、scrollWidth、scrollHeight、clientTop、clientLeft、clientWidth、clientHeight 时，浏览器为了获取这些值，也会进行回流。
* 当我们调用了 getComputedStyle 方法，或者 IE 里的 currentStyle 时，也会触发回流。原理是一样的，都为求一个“即时性”和“准确性”。

避免方式

* 避免逐条改变样式，使用类名去合并样式（ant-design里用类名去区分样式，原来是为了避免重排）
* 减少DOM操作，用fragment操作集合起来

部分浏览器缓存了一个 flush 队列，把我们触发的回流与重绘任务都塞进去，待到队列里的任务多起来、或者达到了一定的时间间隔，或者“不得已”的时候，再将这些任务一口气出队。但是当我们访问一些即时属性时，浏览器会为了获得此时此刻的、最准确的属性值，而提前将 flush 队列的任务出队。

</details>

## 从输入URL到浏览器接收的过程发生了什么事情

<details>
<summary>查看解析</summary>
[Refer](http://fex.baidu.com/blog/2014/05/what-happen/)

简略版

* 输入网址
* DNS解析，tcp请求先经过DNS服务器，解析成响应的IP地址后再和对应的服务器通信
* 与服务器建立TCP连接
* 浏览器向web服务器发送http请求
* 服务端响应请求
* 浏览器接收数据，以html为例，先HTML解析成DOM树
* 建立CSSOM
* 在位图里构建网页的样式
* 将位图中的数据渲染至网页中

</details>

## 从敲下一行js代码到这行代码被执行，中间发生了什么

<details>
<summary>查看解析</summary>
根据你不知道的js，最核心的步骤应该分为三步

* 词法分析，将代码切分成最小的单元。将输入的代码提取出有用的词，并且找出不同类型的词，比如关键字，保留字，操作符，变量
* 建立抽象语法树AST
* 根据AST生成机器能读懂的代码，代码生成。

将高级语言编程机器能够读懂的语言，这个过程，我们称之为`编译`

不同浏览器的编译器实现各不相同，其中以chrome的v8引擎最为出名和优秀。v8的编译性能特别优秀，它是怎么做到的呢

* 脚本流。以往的chrome中，js解析是交给浏览器主线程来做的，但是我们熟悉事件循环的都知道，浏览器的主线程要包含UI渲染和js解析两个步骤，而且还是交替进行的，属于伪异步。在v8中，js的解析单独交给了流解析器，不会受UI渲染的阻塞
* 字节码缓存。首次访问页面时，js代码会被编译成字节码并且进行缓存，当再次访问时，会直接复用该字节码，省去了下载，解析编译的过程
* 内联。即引擎会自动分析可以精简的代码，比如简短的函数，它会自动合并或者内联至较大的函数中
* 隐藏类。我们都知道在机器中存储不同类型的数据占用的字节数是不一样的，而连续的内存空间有利于数据的读取，v8引擎中会针对每个对象自动创建隐藏类，为的就是每次查询类成员属性时，可以更快地查找
* 热点函数会被直接编译成机器码。机器码执行效率比字节码更快，但是如果在后面的运行中，类型再次发生变化，v8又会回退到字节码

```
var Person = {
    add() {...}
}
Person.name = "li"

var Person = {
    add() {...},
    name: "li"
}
```

上例中，第二个例子的执行效率要高于前者，原因就是方法一中动态添加属性会新生成一个新的隐藏类，如果add函数已经被转换为机器码，那么对于方法一来说，就没有办法复用了

> 所以函数参数类型越稳定，对象内部属性越稳定，v8的执行效率就越高

</details>

## [] == [] 输出的结果是？为什么？

<details>
<summary>查看解析</summary>
    这里考察的不是`==`和`===`，而是V8中数据的存储
    引用类型的数据是用堆存储的，所以哪怕两个完全一致的数据也是在堆中有不同的地址
</details>

## 说一下 == 的规则

<details>
<summary>查看解析</summary>

* undefined和null只有在相互之间判断才为true，其他任何操作都是false
* Boolean类型首先转化为Number
* NaN出现在任意一边都返回false
* String和Number运算时会优先将String转化为Number
* 如果一方为引用类型，则会优先调用其ToPrimitive操作

</details>

## 事件委托

<details>
<summary>查看解析</summary>
委托本身的定义是将函数可以当做参数在函数间传递，这样可以大大增加函数的复用性

但是这里的委托的意思是利用事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件

事件委托指的是，不在事件的发生地（直接dom）上设置监听函数，而是在其父元素上设置监听函数，通过事件冒泡，父元素可以监听到子元素上事件的触发，通过判断事件发生元素DOM的类型，来做出不同的响应

</details>

## target和currentTarget的区别

<details>
<summary>查看解析</summary>
很简单，就是在事件委托中，target是触发事件的元素（可以是子元素），currentTarget指的绑定事件的元素（指父元素）
</details> 


## JS中的尾递归优化

<details>
<summary>查看解析</summary>

> 严格意义来说，尾递归的实现就是迭代的思想。要注意这里是指迭代的思想，而非迭代在JS中的实现

首先我们来看尾递归的由来，在JS中深度很高的递归会造成调用栈的爆满，一般引擎会自动判断是否爆栈。但是我们知道递归大部分情况下引用的都是同一个函数，只是每一次都需要创建一个新的执行上下文罢了，那么有没有什么比较好的办法来解决这个问题呢

尾递归技术是指，函数的返回值如果只包含另外一个（也可以是本函数）的调用，那么进入新的函数调用时不会开辟新的执行上下文，而是选择就地复用当前执行上下文（或者是将当前上下文出栈，再新建上下文），总之调用栈的深度不会增加。尾递归技术实际上就是给Lisp这样没有循环的纯函数式语言准备的

尾递归的出现形式如下

```
function sum(n) {
    ...
    return 1 + sum(n-1) // 不行，因为还包含了+1这个操作
}

function sum(n) {
    ... 
    return sum(n-1) // 可以
}

function sum() {
    ... // 没有显式return也是不行的，因为会默认返回return undefined
}
```

尾递归当然可以应用于一个函数调用另外一个函数，但是大部分这种情况都不是很需要优化，真正需要优化的是一个函数调用自身，即递归深度过深，这个时候我们才会选择尾递归优化

比如这样一段代码

```
const sum = (n) => {
  if (n <= 1) return n;
  return n + sum(n-1)
}
```

显然，它是不符合尾递归调用的规则的，我们需要对其进行改造，改造规则就是一开始说的，递归变迭代

```
const sum = (n, result) => {
    if(n === 1)return 1
    return sum(n - 1, result + n)
}
```

此外还要注意尾递归优化的支持程度，很多浏览器实际上是不支持的，比如上述代码，在chrome中还是会不断创建新的调用栈

> 现在只有在ES6的严格模式下，才会存在尾递归优化，

但是我们可以对递归函数，做这样的处理

```
const sum0 = (n, result = 0) => {
    if (n <= 1) return n + result
    return () => sum0(n - 1, n + result)
}
const trampoline = f => (...args) => {
    let result = f(...args)
    while (typeof result === "function") {
        result = result()
    }
    return result
}
const sum = trampoline(sum0)
```

我们使迭代函数不直接调用自身，而是返回一个调用自身的函数，同时我们要注意返回的函数里没有引用函数里的变量，所以也不会产生闭包，这样就可以完美解决上述问题

</details>

## for...in 和 for...of的区别

<details>
<summary>查看解析</summary>

对于前者，实际上考察的是`in`操作符的定义，这个操作符可以判断某个属性(该属性作为操作数时，需要时字符串类型)是否为该对象或者该对象的原型上的一个s属性

那么对于`for...in`这个语句，它的作用是`循环某个对象及其原型链上所有除Symbol类型外的可枚举属性`，很好理解，就是在in操作数的基础上加入了enumerable判断

for...in语句适合对象的遍历，而不适合数组的遍历

for...of这个语句就没有of这个操作数了，它限定于访问具有内置迭代器的对象（可迭代对象）

关于迭代器存在两个协议，一个是`可迭代协议`，一个是`迭代器协议`

可迭代协议用来控制一个对象的迭代行为，比如for...of语句能否套用在对象里，JS里很多内置类型已经默认存在`可迭代协议了`，比如`String`类型，`Array`类型，`Map`类型，`Set`。但是`Object`类型不可以

要实现可迭代协议，必须拥有@@iterator方法，即对象或者对象的原型链具有[Symbol.iterator]属性，该属性方法调用是不传入参数，然后返回一个`迭代器`，换句话说该属性必须要是一个生成器

迭代器协议即定义了一个怎么样的对象可以称为`迭代器`，通常来说需要满足两点

* 该对象具有next方法
* next方法需要返回一个对象，该对象具有done和value两个属性

大部分情况下我们自定义的对象是同时满足迭代器协议和可迭代协议的，但是我们要知道这两个协议并不一定是天然绑定在一起的

比如字符串直接量"123"具有[Symbol.iterator]属性，说明她符合可迭代协议，但是它没有next方法，"123"[Symbol.iterator]属性是一个方法，该方法调用后返回的值为满足迭代器协议的对象。内置的几种类型基本上都是这种情况，符合可迭代协议，不符合迭代器协议。

> ...扩展运算符在操作数为数组或者函数参数时，默认就是只能操作可迭代对象，比如[...obj]如果obj不符合可迭代协议，会抛出语法错误的

*生成器函数的调用值为一个迭代器，那么这个迭代器显然是符合`迭代器协议`的，因为它具有next方法，且有标准的返回值，那么它符不符合`可迭代协议呢`,如果只是去思考它有没有[Symbol.iterator]属性的话，那么一时半会也想不出来，但是要知道一个生成器对象是可以用于for...of语句的，那么答案很明显了，是符合`可迭代协议`的。其Symbol.iterator属性就等于自己，回想一下生成器的实现，好像也确实这样

```
function* name() {
    yield 100
    yield 200
    yield 300
}

let gen = name()
console.log(gen[Symbol.iterator]() === gen) // true
for (let i of gen) {
    console.log(i) // 100, 200, 300
}
```

除此之外，和遍历以及访问属性有关的操作有比如hasOwnProperty，这个判断是判断一个属性是否只属于该对象

Object.keys可以获取对象上（不包含原型链）的所有可枚举属性

Object.getOwnPropertyNames可以获取对象(不包含原型链上)所有属性，无论是否可枚举
</details>

## ajax,axios,fetch的使用

<details>
<summary>查看解析</summary>

按照发展顺序来，首先是ajax的介绍，ajax即async js and XML，即JS异步编程。ajax的出现是为了解决网页不用重新加载页面即可更新页面内容。我个人的理解是ajax提供了js运行时级别的数据响应，从而可以通过js操作DOM来完成页面的变化，而传统的方法可能是通过表单的提交来实现这一点。ajax是目的，然后传统的ajax的实现方法是XHR，XHR的实现这里不细说，只说一下XHR也即ajax的缺点

* 它是基于事件的异步编程，配置和调用形式比较混乱
* ajax的整体实现实际上有点面向过程的感觉，它不符合`关注点分离`这样的特性，比如创建请求和对请求的响应包括对请求的设置全部都写在一起，高耦合
* 即使封装了，基于回调的异步模型也会使代码可读性降低很多
* 不够优雅

fetch是现代浏览器提出了异步网络编程的另一种解决方案，它是基于Promise的，也就是说fetch函数的返回值会为一个Promise实例。它接收两个参数，一个是url，另一个为一个init对象，里面可以设置本次请求的详细配置

可以选择的配置有

* method
* url
* headers
* body
* credentials(omit不携带cookie，还有same-origin（后来修改的默认）和include即总是携带cookie)

!>除了传入两个参数以外，还可以传入一个Request对象，里面是和init一样的配置（还加上了url）

```
let req = new Request('https://www.baidu.com')
req.method = "POST"
fetch(new Request(req))
```

fetch不完善的地方

* 默认不带cookie
* 服务器返回 400，500 错误码时并不会 reject，只有网络错误这些导致请求不能完成时，fetch 才会被 reject

fetch目前也越来越完善了，以前的一些Promise的缺陷，一方面Promise也在修改，另一方面fetch也增加了AbortSignal接口

总结，fetch的特点

* 基于Promise
* 可以在fetch里配置很多东西
* 默认不带cookie
* 对于400和500的返回不reject，只是会把resolve里的ok设为false，只有在网络错误才会
* fetch不是Promise+XHR的封装，我看MDN好像说是ServiceWorker
* 不支持Progress
* 不支持timeout

axios是基于Promise的网络请求封装库。为什么它会比fetch更适用于业务开发中呢？一方面fetch的兼容性永远是一个问题，另一方面axios也提供了很多fetch没有实现的拓展功能，比如

* 本质是Promise+ajax的封装（浏览器环境下）
* 拦截了请求和响应
* 取消请求（Cancel了之后会抛出一个错误，方法是先注册一个Abort对象，然后注入到axios请求中）
* 防御CSRF

</details>

## 前端性能优化

<details>
<summary>查看解析</summary>
首先我们要知道性能优化是一个基于系统的问题，也就是说是具体问题具体分析，但是我们也可以提取一些总则，大概可以分为首屏渲染和交互阶段的性能优化

* 首屏渲染
    * 资源的下载
        * 需不需要下载
            * 缓存
            * SW
        * 资源的大小
            * 压缩
            * 移除注释内容
        * 资源的个数
            * 合并
            * 内联
            * async
            * defer
        * 资源的请求时间
            * CDN
    * 合理放置资源的位置
    * 白屏的渲染
        * 骨架屏
    * 资源的执行
        * 减少重排和重绘
            * 重排，重绘，合成的优先级
            * 合并DOM操作的请求
                * class
                * raf
            * 离线
                * vdom
                * fragment
                * display:none
        * 加速JS的执行过程或者减少JS的执行过程 
            * js碎片化
            * web worker
            * 避免动态作用域
            * 避免布局抖动  
</details>

## 箭头函数的特点

<details>
<summary>查看解析</summary>

* 它相当于一个匿名函数
* 没有this，arguments
* 但是是有自己的执行上下文的
* 没有自己的原型对象
* 不能通过强制绑定来修改内部this的指向
* 不可以用作构造函数（原型和this都没有）
* 不可以做为生成器函数

</details>

## 前端现在发展的方向（新技术）

<details>
<summary>查看解析</summary>
1. PWA（包括与之相关的本地化的新技术）
2. WebAssembly
3. WebComponents
4. Serverless
5. ssr
</details>