1. 居中问题
```css
/*方法一*/
height：100%；
line-height: 100%;

/*方法二*/
position: absoute;
top: 50%;
left: 50%;
transform: translate3d(-50%, -50%, 0);
```

2. BFC
```js
// 定义：块级格式化上下文
// 特点：独立渲染区域，这个区域内的布局与外部毫不相干
// 典型触发方式：父元素没有设置高度，子元素设置margin-top的时候，父元素会塌陷
// 解决方式：给父元素设置border-top：solid 1px transparent，或者overflow：hidden；
```

3. 清除浮动
```css
/*方法一*/
overflow：hidden;

/*方法二*/
.clearfix::after{
    content: '';
    display: block;
    clear: both;
    height: 0;
    line-height: 0;
    visibility: hidden;
}
.clearfix{
    zoom: 1;
}
```

4. 选择器的优先级
```js
// important > id > class > 标签选择器 > 伪类选择器
```

5. px, em, rem
```js
// rem可以自适应
// 还有绝对单位，相对屏幕的宽度的
```

6. flex布局
```css
// 注意：一般使用了flex的元素，需要设置其高度1px，不然渲染会有问题；
/*父元素*/
.father{
    display: flex;
    flex-direction: row / column;   // 主轴对齐方式
    flex-wrap: nowrap | wrap | wrap-reverse;    // 是否换行
    justify-content: flex-start | flex-end | center | space-between | space-around;     // 在主轴上的对其方式
    align-items: flex-start | flex-end | center | baseline | stretch;   // 在侧轴上的对其方式
}
/*子元素*/
.son{
    flex: 1;    // 占比
    order: 1;   // 排列顺序，越小越靠前
    flex-grow: <number>; /* default 0 */    // 有剩余空间的时候放大倍数，默认不放大
}
```

7. animation和transition的属性
```css
/*animation*/
.box{
    animation: 动画名 时间 速度曲线 延迟 播放次数 是否轮流反向播放；
}
/*定义动画*/
@keyframes myanimation{
    from {
        background: red;
    }
    to {
        background: yellow;
    }
}

/*transition*/
.box{
    transition: 属性名 时间 速度曲线 延迟；
}

```

8. AMD和CMD的区别
    - AMD：依赖前置，文件一次性加载
    - CMD：就近依赖，文件需要的时候才加载