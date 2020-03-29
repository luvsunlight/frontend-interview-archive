![](https://static001.geekbang.org/resource/image/41/62/4153891927afac7f4c21ccf6a141f062.png)

## 如果一个网页的文档中含有阿拉伯文，你应该用什么编码

> 归类: 

<details>
<summary>查看解析</summary>
这个题考察的是对编码的理解，虽然跟前端没什么关系，但是前端经常要用到，所以也算一个实用的小知识了

utf-8，unicode都是字符代码的三种编码形式，一个字符可以被编码成这几个格式

unicode字符集编码是国际组织制定的可以容纳世界上所有文字和符号的编码方案。unicode编码使用两个字节来表示一个字符。但是unicode只是一个符号及，它只规定了符号的二进制代码。却没有规定这个二进制代码应该如何存储

utf-8是unicode的一种实现方式，它是一种编码规则。unicode是字符集，它的作用是为每一个字符分配唯一的码位，编码规则的作用是将码位转换为字节序列的规则

* ascii码可以很好地表示英文，但是它最多只能表示2^8也就是256个数字，这对于汉字来说还远远不够
* 中国人民通过对 ASCII 编码的中文扩充改造，产生了 GB2312 编码，可以表示6000多个常用汉字。
* 汉字实在是太多了，包括繁体和各种字符，于是产生了 GBK 编码，它包括了 GB2312 中的编码，同时扩充了很多。
* 中国是个多民族国家，各个民族几乎都有自己独立的语言系统，为了表示那些字符，继续把 GBK 编码扩充为 GB18030 编码。
* 每个国家都像中国一样，把自己的语言编码，于是出现了各种各样的编码，如果你不安装相应的编码，就无法解释相应编码想表达的内容。
* 终于，有个叫 ISO 的组织看不下去了。他们一起创造了一种编码 UNICODE ，这种编码非常大，大到可以容纳世界上任何一个文字和标志。所以只要电脑上有 UNICODE 这种编码系统，无论是全球哪种文字，只需要保存文件的时候，保存成 UNICODE 编码就可以被其他电脑正常解释。unicode用两个字节来表示一个字符
* UNICODE 在网络传输中，出现了两个标准 UTF-8 和 UTF-16，分别每次传输 8个位和 16个位。
* 于是就会有人产生疑问，UTF-8 既然能保存那么多文字、符号，为什么国内还有这么多使用 GBK 等编码的人？因为 UTF-8 等编码体积比较大，占电脑空间比较多，如果面向的使用人群绝大部分都是中国人，用 GBK 等编码也可以。
</details>

## css百分比

> 归类: css/语言/单位

请写出inner的实际高度

```
<style>
  .outer {
    width: 200px;
    height: 100px;
  }
  .inner {
    width: 60px;
    height: 60px;
    padding-top: 20%;
  }
</style>
<div class="outer"><div class="inner"></div></div>
```

<details>
<summary>查看解析</summary>
看起来好像是60+100*20%=80px，但实际上，除了height以外（比如margin和padding）的垂直方向上的百分比取值都是相对于父元素内容的宽度（不包含padding），所以在这里，是60+200*20%=100px
</details>

## meta标签 

> 归类: html/元素/文档元信息

<details>
<summary>查看解析</summary>

* seo优化
* charset
* viewport移动端适配
</details>

## H5和CSS3的新特性

<details>
<summary>查看解析</summary>

HTML5的新特性有

* 语义化元素
* canvas
* 内联SVG
* drag拖拽（对被拖拽的元素设置draggable属性，然后通过event.dataTransfer来缓存数据，注意要preventDefault，然后在被拖拽的容器里设置ondragover时preventDefault，接着ondrag时将dataTransfer里的数据提取出来并且存入）
* navigator.geolocation
* WebSocket
* video & audio

CSS3的特性有

* 动画（transform，transition，animation）
* flex
* border-radius
* box-shadow
* 媒体查询（@media <mediaType> <queryRule>）
    * mediaType包含all，print(打印机),screen(屏幕),speech(屏幕阅读器)
    * queryRule包含max-width


</details>

## 隐藏页面中某个元素的方法有哪些

> 归类: css/布局

<details>
<summary>查看解析</summary>

屏幕并不是唯一的输出机制，比如一些看不见的元素可以被读屏软件阅读出来，为了消除它们之间的歧义，我们将其归为三大类

* 完全隐藏：元素从渲染树种消失，不占据空间
* 视觉上的隐藏：屏幕中不可见，占据空间
* 语义上的隐藏：读屏软件不可读，但正常占据位置

1.完全隐藏

* display:none
* html属性 hidden

2.视觉隐藏

* 利用盒模型
    * position => fixed,absolute,relative移出屏幕外
    * 负margin值
* transform值
    * transform: scale(0)
    * height: 0
    * transform: translateX(-99999px)
* 自身的大小
    * 宽高设为0
    * visibility: hidden
    * opacity: 0
* 层级覆盖
    * position:relative; z-index:-999
* clip-path建材
    * clip-path: polygon(0 0, 0 0, 0 0, 0 0)

3.语义上的隐藏
    * dom属性aria-hidden="true"

* opacity: 0
* height: 0
</details>

## 说几个实用的BOM

<details>
<summary>查看解析</summary>

BOM即浏览器对象模型

* location对象
    * url
* history对象
    * go
    * back
* navigator对象
    * UA
    * 浏览器等硬件信息

</details>

## iframe是什么，有什么缺点

<details>
<summary>查看解析</summary>

</details>

## Doctype作用?严格模式与混杂/怪异模式如何区分？它们有何意义?

<details>
<summary>查看解析</summary>
我们看一段html代码

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <script src="https://unpkg.com/vue@2.6.11/dist/vue.js"></script>
        <title>Document</title>
    </head>
    <body>
        <div id="app">
            <div>{{text}}</div>
            <input v-model="text" type="text" />
        </div>
        <script>
            let app = new Vue({
                el: "#app",
                data() {
                    return { text: 1 }
                }
            })
        </script>
    </body>
</html>

```

在很久以前，浏览器解析网页有两个标准，一个是网景公司的浏览器版本，一个是IE版本，后来W3C创立了网络标准后，虽然有统一的标准（如上所示Doctype html），但是为了保证旧网页的正确加载，还是保留了两种规范，即`标准模式`下以html和css的规范去解析网页，在`怪异模式`下以网景浏览器和IE的非标准行为去解析网页

两种模式的区别如下

* 标准模式使用的是W3C标准盒模型，怪异模式使用的是IE盒模型（width即clientWidth）
* 字体继承问题。标准模式下字体是可以继承的，但是怪异模式下，table内部的元素可能不会继承这些字体
* 内联元素尺寸。标准模式下内联元素不可以设置宽高，但是怪异模式可以
* 元素百分比高度计算，标准模式下要求父元素必须要有指定的高度，不能为auto，怪异模式下父元素可以为auto，子元素使用百分比会正确计算
* 对于溢出内容，标准模式默认为visible，怪异模式下会让父元素的大小变化以填充所有的内容
* table-cell布局里的base-line不一样

</details>

## clientHeight,scrollHeight,offsetHeight ,以及scrollTop, offsetTop,clientTop的区别？

<details>
<summary>查看解析</summary>

offsetHeight和offsetHeight指DOM元素的像素高度/宽度，包含自身`content`+`内边距`和`边框`还有`滚动条(注意这里单指滚动条本身的宽度)`，注意不包含margin`外边距`

![](https://developer.mozilla.org/@api/deki/files/186/=Dimensions-offset.png)

clientWidth即元素内部的元素，和offsetWidth相比，少了`边框`和`竖直滚筒条`,即它只包含`width`和`padding`

![](https://developer.mozilla.org/@api/deki/files/185/=Dimensions-client.png)

scrollWidth和clientWidth相比，则多了一个水平方向可以滚动的内容，除此之外它还可以包含伪元素（前两者均不行）,如果说没有伪元素且没有水平方向没有溢出的话，scrollWidth和clientWidth是相同的

![](https://developer.mozilla.org/@api/deki/files/185/=Dimensions-client.png)

scrollTop，scrollBottom，这些属性是用来获取一个元素其xx方向距离其目前视口的距离，如果没有相应方向的滚动则为0

</details>


## mouseover & mouseenter的区别

<details>
<summary>查看解析</summary>
简单来说就是mouseover属性有冒泡效果，鼠标进入其子元素或者元素自己都会触发mouseover事件，mouseenter则不会，只有鼠标进入元素自己才会触发
</details>

## 设置字体大小为6px

<details>
<summary>查看解析</summary>
chrome里面最小只能设置字体为12px（其他浏览器各不一样），如果想要实际字体为6px，可以尝试transform:scale
</details>

## 实现自适应的正方形

<details>
<summary>查看解析</summary>
乍一看很难，其实很简单，关键就在于正方形的边长设置为自适应单位，比如vw，vh，vmin，vmax，em，rem等。比如宽度设置20%。高度设置20vw即可

还有一种思路是，元素设置width 20%, height: 0, padding-bottom 为20%，这里的20%就是相对父元素的宽度，就能达到高宽一致了，如果需要在该元素内设置子元素的高度，需要通过设置子元素为absolute，脱离文档流，这样它参考的就是父元素的高度，也是实际它应该参考的高度（本元素为0）
</details>

## css实现菊花图

<details>
<summary>查看解析</summary>

</details>

## CSS实现环形进度条

<details>
<summary>查看解析</summary>

</details> 

## @import 和 link

<details>
<summary>查看解析</summary>

* @import必须写在样式表的最顶层
* @import的兼容性差
* @import的发生阶段在

</details>

## 图片懒加载

<details>
<summary>查看解析</summary>

首先要搞清楚这样几个核心概念

* document.documentElement.clientHeight。视口宽度
* document.documentElement.scrollTop。当前页面顶部距离视口的距离
* element.offsetTop。当前元素距离其offsetParent的距离，一般来说这个距离是固定的

那么只要clientHeight+scrollTop>offsetTop时，即表示该元素进入视口区域

对于图片的懒加载，步骤如下

* 首先定义元素，不设置src，可以设置其他的属性比如data-src表示其应该加载的元素
* 用一个数组存储全部需要懒加载的元素
* 监听页面的onload和onscroll，回调函数为懒加载函数
* 在懒加载函数中，每个图片判断是否为可视状态，如果可以则将其src设置为data-src，然后将其pop出数组

</details>

