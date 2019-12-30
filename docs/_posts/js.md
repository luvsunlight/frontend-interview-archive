# ![](https://static001.geekbang.org/resource/image/6a/9b/6aec0a09381a2f74014ec604ef99c19b.png)

## 1. 函数

### 1.1 函数的三种定义方式

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

### 1.2 判断this的指向

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

### 1.3 call,apply和bind

<details>
<summary>查看解析</summary>
这三者都是在解决this指向时，可以硬绑定的函数。主要区别在于细节的逻辑不同

apply接受n个参数，第一个参数是上下文，第二个参数是函数使用的数组
call和apply类似，不过它的参数不是以数组的形式，而是散列传递。bind要和call比较，它可以接受多个参数，第一个也是上下文，第二个开始往后面可以是函数的部分参数，这里它的思路非常类似于函数的柯里化，比如我们可以`add.bind(1)(2) //3`

我们来想一下apply的发生过程

* 接受第一个参数作为上下文，如果没有则默认绑定至全部作用域
* 将第二个参数带入至函数本身
* 返回函数返回的结果

```
Function.prototype.apply = function(context, args) {
	var context = context || typeof window === "undefined" ? global : window
	context.func = this
	let result = context.func(...args)
	delete context.func
	return result
}
Function.prototype.call = function(context, ...args) {
	var context = context || typeof window === "undefined" ? global : window
	context.func = this
	let result = context.func(...args)
	delete context.func
	return result
}

var add = (a, b) => a + b

console.log(add.apply(null, [1, 2]))
console.log(add.call(null, 1, 2))

```

```
Function.prototype.bind = function(context, ...args) {
	let fn = this
	return (...restArgs) => fn.call(context, ...args, ...restArgs)
}
```

大概的call和apply可以写成上述形式，有两个需要注意的点

* 不可以直接this(...args)，因为我们需要隐式绑定this到context上
* 因为上述步骤，我们对context新增了一个属性，获取完数据后需要删除该属性

</details>

### 1.4 分别实现防抖和节流函数

<details>
<summary>查看解析</summary>

防抖和节流函数创建的初衷都是为了限制短时间内某个事件发生的频次，不一样的是处理逻辑。防抖函数要求，在一段时间内，该函数只能触发一次，一旦再次触发，则重新计时。而节流函数则是当再次触发的时候，触发无效

```
const debounce = (fn, delay) => {
	let timer = null
	return (...args) => {
		if (timer) {
			clearTimeout(timer)
			timer = null
		}
		timer = setTimeout(() => {
			fn.apply(null, args)
		}, timeout)
	}
}

const throttle = (fn, delay) => {
	let isRun = false
	return (...args) => {
		if (isRun) {
			console.log("isBusy...")
			return
		} else {
			iRun = true
			setTimeout(() => {
				fn.apply(null, args)
				isRun = false
			}, delay)
		}
	}
}
```

</details>

### 1.5 原型链

### 1.6 继承的方式

<details>
<summary>查看解析</summary>
</details>

### 1.7 柯里化函数的实现

```
add(1, 2, 3) // 6
curry(add)(1)(2)(3) // 6
curry(add)(1, 2)(3) // 6
```

<details>
<summary>查看解析</summary>
我们首先来看函数柯里化的概念，函数柯里化是指将接受多个参数的函数转化成接受单一参数的函数，并且返回接受余下参数并且返回结果的新函数的技术

```
add(1, 2, 3) // 6
curry(add)(1)(2)(3) // 6
curry(add)(1, 2)(3) // 6
```

具体实现如下

```
const curry = (fn, ...args) =>
	args.length < fn.length
		? (...newArgs) => curry(fn, ...args, ...newArgs)
		: fn(...args)
```

</details>

## 2. 对象

### 2.1 遍历

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

### 2.2 实现一个深拷贝

<details>
<summary>查看解析</summary>

> 浅拷贝会将对象的每个属性依次进行复制，当对象的属性值是引用类型时，它拷贝的实际上是属性的引用
 
浅拷贝的实现可以用`slice,Object.assign,for...in,concat`来进行
 
> 深拷贝则会递归引用类型的数据，并且复制基本类型的属性值。深拷贝后的对象和原有对象属性表现完全一致，但是却是两个不同的个体，修改并不会互相影响

深拷贝的实现有几种方式

> 1.JSON.parse(JSON.stringfy(obj))

就是先将对象序列化然后再反序列化，但是这么做有几个限制

* 属性值是函数时，无法处理
* Date和RegExp类型的属性，无法处理
* 原型链上的属性，无法处理
* Symbol类型的属性，无法处理
* undefined无法处理
* 无法序列化自身引用的对象

> 2.实现一个deepClone函数

* 基本类型直接返回
* Date和RegExp类型，则返回对应类型
* 复杂数据类型，递归处理
* 考虑循环引用


```
const deepClone = obj => {
	if (Array.isArray(obj)) return [...obj]
	let newObj = {}
	for (let key in obj) {
		newObj[key] =
			typeof obj[key] === "object" ? deepClone(obj[key]) : obj[key]
	}
	return newObj
}
```

</details>

### 2.3 数据拦截

<details>
<summary>查看解析</summary>
1.Object.defineProperty
2.Proxy
</details>

## 3. new的实现原理是什么

> 归类: this和对象原型

<details>
<summary>查看解析</summary>
以`var a = new Array()`
new的过程包含四个步骤

* 创建一个新的对象
* 该对象被执行原型链接
* 执行构造函数方法，Array中所有this指向新对象（一个显式绑定的过程）
* 如果Array函数中有返回值则返回返回值，如果没有则直接返回该新对象

```
function _new() {
    let target = {}
    let [constructor, ...args] = [...arguments]
    target.__proto__ = constructor.prototype
    let result = constructor.apply(target, args)
    if(result && (typeof result === "object" || typeof result === "function")) {
        return result
    }else {
        return target
    }
}
```

</details>

## 6. 异步加载js的方法有哪些

<details>
<summary>查看解析</summary>
1. script里写入defer或者async

区别

* 加载顺序。defer是等页面的渲染完全结束（DOM结构生成+其他脚本执行）之后，window.onload之前加载。async在加载之后，整个页面就停止渲染，然后渲染async脚本，加载完全之后之后再继续渲染
* defer脚本会按照其代码中的顺序依次执行
* async脚本顺序不保证

2.动态创建脚本

只有在`document.body.append(script)`才会执行该脚本

3.xhr+eval异步加载和执行脚本
</details>

## 7. 说一说你对js执行上下文栈和作用域链的理解

<details>
<summary>查看解析</summary>
作用域，实际上就是js引擎根据变量名获取对应值权限的机制。作用域分两种词法作用域和动态作用域，词法作用域是根据书写代码时变量和函数声明的位置来决定的，而动态作用域则是会在函数运行的过程中动态改变作用域。（with和eval可以修改词法作用域，但是影响性能和增加不确定性，不推荐）
</details>

## 8. 实现Promise.all方法

<details>
<summary>查看解析</summary>

```
if (!Promise.all) {
	Promise.all = promises =>
		new Promise((resolve, reject) => {
			let result = []
			let count = 0
			promises.map((promise, index) =>
				Promise.resolve(promise).then(
					res => {
						result[index] = res
						if (++count === promises.length) {
							resolve(result)
						}
					},
					err => reject(err)
				)
			)
		})
}
```

</details>

## 9. 可迭代对象有哪些特点

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

## 10. 原型链的理解

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

## 11. JS的数据类型分类和判断

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



