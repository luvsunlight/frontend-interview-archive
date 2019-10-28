# ![](https://static001.geekbang.org/resource/image/6a/9b/6aec0a09381a2f74014ec604ef99c19b.png)

## 1. 实现一个深拷贝

> 归类: js/运行时/数据结构/基本类型

<details>
<summary>查看解析</summary>
这个实际上是考察基本型中Object类型的细节。在js中，对象是引用类型，比如我们先定义了一个`obj1 = {name:"test"}`,然后我们让`obj2=obj`,那么实际上obj1和obj2在指向的是同一片内存地址。修改obj1，obj2也会改变，反之亦然。深拷贝相当于是一份数据，两个别名。

深拷贝则不然，就像克隆人，拷贝完了之后就是完全的另外一个个体，两个对象之间互不打扰。深拷贝主要要解决的就是数组和对象两个格式，因为可能会有无线嵌套，所以我们一定要用递归来做

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

## 2. const, let, var 的区别

> 归类: 文法/词法/关键字

<details>
<summary>查看解析</summary>
这三个都是js中用于定义元素的关键字，前两者是es6新引入的特性.js中一共有`6种声明对象的方法`，分别是var,function(es5中)，const,let,import,class.

变量提升即，在变量定义前也可以引用，只不过返回 undefined。暂时性死区即在作用于中，一旦声明了该变量，则在声明之前，该变量均不可使用

| 声明方式 | 变量提升 | 块级作用域 | 暂时性死区 | 重复声明 | 特点                                               |
| -------- | -------- | ---------- | ---------- | -------- | -------------------------------------------------- |
| var      | ✅       | ⛔️        | ⛔️        | ✅       | -                                                  |
| let      | ⛔️      | ✅         | ✅         | ⛔️      | -                                                  |
| const    | ⛔️      | ✅         | ✅         | ⛔️      | 定义时需要赋值，之后值不可以改变（对象的属性可以） |

</details>


