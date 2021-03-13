1.介绍一下标准的 CSS 的盒子模型？低版本 IE 的盒子模型有什么不同的？
```
盒模型都是由四个部分组成的，分别是margin、border、padding和content。

标准盒模型和IE盒模型的区别在于设置width和height时，所对应的范围不同。
标准盒模型的width和height属性的范围只包含了content，而IE盒模型的width和height属性的范围包含了border、padding和content。

一般来说，我们可以通过修改元素的box-sizing属性来改变元素的盒模型。
```
---
2. ::before 和:after 中双冒号和单冒号有什么区别？解释一下这 2 个伪元素的作用
```
在css3中使用单冒号来表示伪类，用双冒号来表示伪元素。但是为了兼容已有的伪元素的写法，在一些浏览器中也可以使用单冒号来表示伪元素。

伪类一般匹配的是元素的一些特殊状态，如hover、link等，而伪元素一般匹配的特殊的位置，比如after、before等。
```
---
3. 经常遇到的浏览器的兼容性有哪些
```
不同浏览器默认值不同，需要初始化样式
Chrome最小字体12px
ios老版本的浏览器弹出输入框收起后页面不自动回弹
ios老版本不支持position：fixed
```
---
4. BFC
```
BFC指的是块级格式化上下文。
一个元素形成了BFC之后，那么它内部元素产生的布局不会影响到外部元素，外部元素的布局也不会影响到BFC中的内部元素。
一个BFC就像是一个隔离区域，和其他区域互不影响。
overflow，position非static，inline-block，float，flex都能产生BFC
```
---
5. 浏览器是怎样解析 CSS 选择器的
```
从右向左匹配，也是从内向外
所以为了性能，最好不用标签选择器，用class或id
```
---
