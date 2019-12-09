![](https://static001.geekbang.org/resource/image/41/62/4153891927afac7f4c21ccf6a141f062.png)

## 1. 如果一个网页的文档中含有阿拉伯文，你应该用什么编码

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

## 2. css百分比

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

## 3. meta标签 

> 归类: html/元素/文档元信息

<details>
<summary>查看解析</summary>

* seo优化
* charset
* viewport移动端适配
</details>

## 4. BFC是什么？BFC的布局规则是什么？如何创建BFC

## 5. 隐藏页面中某个元素的方法有哪些

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


## Refer

* [这里有20道大厂面试题等你查收](https://juejin.im/post/5d124a12f265da1b9163a28d)