## CSS动画基础知识测验
1.解释 `animation`子属性的含有<br>
* animation-delay 
* animation-direction
* animation-duration
* animation-iteration-count
* animation-name
* animation-play-state
* animation-timing-function
* animation-fill-mode

2.使用什么_________ 定义动画序列？

3.animation-iteration-count 属性的 `infinite`值含义是什么？

4.animation-direction 属性的 `alternate` 值含义是什么？

5.css动画从开始播放到完成会触发那些动画事件?

6.解释 `transitions`子属性的含义
* transition-property
* transition-delay
* transition-timing-function
* transition-duration


7.补充完毕`transitions` 的简写格式。将第六题对应的子属性填入到下方代码的空格中
```css
div {
    transition: ____ ____ ____ ____;
}
```
8.补充完下列代码
```css
.box {
    border-style: solid;
    border-width: 1px;
    display: block;
    width: 100px;
    height: 100px;
    background-color: #0000FF;
    transition: ____________________________________________;
}

.box:hover {
    background-color: #FFCCCC;
    width: 200px;
    height: 200px;
    transform: rotate(180deg);
}
```
要求高度变化时间为1s，宽度变化时间为1.5秒，背景颜色过渡为2秒，旋转过渡时间为1s。

9.请问top 的过渡时间是多久？
```css
div {
  transition-property: opacity, left, top, height;
  transition-duration: 3s, 5s;
}
```
10.写出你知道的常用css 动画支持的属性。如width,height（15个试卷中提及的不算）





11.对于css `animation`属性可以实现，同指定不同的动画不？比如放大的同时改变背景颜色，写出关键代码
。如何做到在不改变`<div class="dv1">`class赋值的情况下先放大2倍再改变背景颜色，请写出核心代码。如果有多种方式，使用
方法一，方法二隔开