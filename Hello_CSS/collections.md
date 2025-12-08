[TOC]

# 关于遇到的或知识模糊的样式属性

## 媒体资源
### object-fit
指定元素的内容适应指定容器的高度与宽度,一般用于img 和 video 标签

+ **fill** 默认，不保证保持原有比例，内容拉伸填充整个容器
+ **contain** 保持原有比例，内容缩放
+ **cover** 保持原有尺寸比例。但部分可能被剪切
+ **none** 保留原有元素内容的长和宽，即内容不会被重置
+ **scale-down** 保持原有尺寸比例。内容的尺寸与none或contain中的一个相同，取决于它们两个间谁得到的对象尺寸会更小一点


### 一些需求实现
#### 添加背景阴影使其凸出显示
**需求**: 一个定位的弹窗关闭按钮(白色X号)显示不清晰
**想法**: 给关闭按钮盒子添加一层灰色的底色来凸出显示X号
1. 想到背景灰色渐变`background: linear-gradient`,但是只是内部盒子有颜色,显得有点突兀
2. 又想到`box-shadow`来添加外阴影.

**解决**: 直接使用`filter: drop-shadow(3px 3px 3px rgba(0, 0, 0, 0.5));`为X号添加阴影

#### 图片随屏幕缩放固定比例进行缩放
##### 1. 定位 + img + padding
```html
<style>
  .img_wrap {
    padding-bottom: 100%;
    width: 100%;
    position: relative;
  }
  .img_wrap img {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
  }
</style>
<div class="img_wrap">
  <img src="../../assets/images/hktb/esolution/demo.jpg" alt="">
</div>
```
##### 2. background + cover + padding
``` html
<style>
  .img_wrap {
    padding-bottom: 50%;
    background: url(url);
    background-repeat: no-repeat;
    background-size: cover;
  }
</style>
<div class="img_wrap"></div>
```
2. 同一行子元素高度以最高的子元素高度为基准进行同高处理
父元素添加样式
```css
.fa {
  display: flex;
  flex-wrap: wrap;
}
```

#### input样式客制化
##### 打勾自定义
```css
.op {
  width: 20px;
  height: 20px;
  cursor: pointer;
  border-radius: 50%;
  position: relative;
  background-color: #E9EAEB;
}
.op input[type=radio] {
  opacity: 0;
}
.op input[type=radio]:checked + i {
  width: 20px;
  height: 20px;
  position: absolute;
  left: 0;
  top: 0;
  background-color: #ED1C24;
  border-radius: 50%;
}
.op input[type=radio]:checked + i::after {
  content: "";
  display: inline-block;
  width: 4px;
  height: 8px;
  border-top: 2px solid #fff;
  border-left: 2px solid #fff;
  background-color: #ED1C24;
  transform: rotate(230deg);
  position: absolute;
  left: 7px;
  top: 4px;
}
```
```html
<label class="op">
  <input type="radio" name="statement5" value="">
  <i></i>
</label>
```
## 定位
### sticky
基于用户的滚动位置来定位。

粘性定位的元素是**依赖于用户的滚动**，在 position:relative 与 position:fixed 定位之间切换。

它的行为就像 position:relative; 而当页面滚动超出目标区域时，它的表现就像 position:fixed;，它会固定在目标位置。

元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。

这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。

> 遇到的问题是有一个sticky的元素在遮罩弹窗弹出时sticky失效。

> 分析原因：页面滚动超出了sticky元素的目标区域，元素黏在页面顶部，但是点击弹出dialog遮罩时，滚动条转为dialog的元素，远比原先滚动的少，因此此时sticky元素的行为表现为position:relative

> 解决的方法：理论要回归sticky属性未出现前的使用js进行控制，判断元素的滚动距离进行定位

## 字体属性
### opacity

### 文字

#### 字体与图表垂直对齐
需要垂直居中对齐的元素加上`vertical-align: middle;`
#### li列表list-style-position为inside并且换行文字对齐实现
```css
  margin-left: 1.5em;
  text-indent: -1.5em;
```

#### 使用vw线性改变字体大小
```css
@media screen and (min-width: 1024px) and (max-width: 1440px) {
    /* 1440-1024 = 416px,1vw对应变化4.16px  (60-40)/4.16= 4.807692307692308 */
    .vw {
        font-size: calc(4.80769vw - 9.2307px)
    }
}
```
### li文字换行与第一行左对齐
```css
li {
  margin-left: 1.5em;
  text-indent: -1.5em;
}
```

## layout
#### 父元素宽度自适应子元素宽度之和
> 需求：swiper3里面wrapper的宽度等于实际slide宽度之和，让条件“wrapper宽度大于container宽度时初始化swiper”成立。

> 想法：让wrapper脱离文本流（脱离文本流的三种方式：float; absolute定位; fixed定位）

> 解决：width: max-content(ie不兼容);

### 弹窗layout移动端出现的问题

##### 需求代码
fixed定位，考虑蒙层要占满网页使用了`width: 100vw;height: 100vh;`

##### 出现的问题
在移动端出现菜单栏/地址栏的时候，弹窗下半部被切掉了一部分，通过滑动屏幕显示/隐藏地址栏还是能正常显示（地址栏隐藏时则显示出原切掉的部分）。

#### 解决1
> 在移动端避免使用100vh
移动端浏览器（Chrome/Safari）有菜单栏/地址栏，地址栏有时可见有时隐藏，改变了视窗的大小。这些浏览器没有将100vh的高度调整为视口高度变化时屏幕的可见部分，而是将100vh设置为隐藏地址栏的浏览器高度。结果是当地址栏可见时，屏幕底部的部分被切断，从而破坏了100vh的初衷

##### 使用window.innerHeight
如果地址栏是可见的，那么`window.innerHeight`是全屏的高度。如果地址栏是隐藏的，那么`window.innerHeight`将是屏幕可见部分的高度

##### 出现的问题
移动端进来页面正常显示，隐藏地址栏时，蒙层高度不足遮住底部，以及横屏响应失败，高度始终固定为初始高度

#### 解决2
> fixed定位基点是浏览器窗口。因此直接使用`width: 100%; height: 100%;`即可实现蒙层占满网页，同时随可视窗口大小进行响应


### 弹窗内容自动撑开高度,并且设置超过最高高度时滚动
**难点:** 要支持超出滚动就要在内容层使用overflow: scroll,但是会使右上角close button超出被隐藏
**解决:** 支持超出最高高度滚动且close button超出容器部分不会被切割
**技术:** 
  1. height和max-height使用百分比需要父元素是确定高度才会生效
  2. height: auto时高度就由内容进行撑开,会超出祖先元素的max-height

- dialog层 fix定位,可以确定高度
  > 
  - wrapper层 height可设置百分比
    - container层
    - close button

## Hack
1. 自定义箭头
```css
&::before {
  border-left: 2px solid #fff;
  border-top: 2px solid #fff;
  content: '';
  display: block;
  opacity: 0.5;
  transition: 0.25s;
  width: 11px;
  height: 11px;
  position: absolute;
}

.swiper-button-prev {
  left: 40px;

  &::before {
    left: 17px;
    top: 15px;
    transform: rotate(-45deg);
  }
}

.swiper-button-next {
  right: 40px;

  &::before {
    left: 13px;
    top: 14px;
    transform: rotate(135deg);
  }
}
```

## 布局
### 盒模型(box model)
可以将所有HTML元素看做一个盒子。
+ Content（内容） - 由内容边界限制，容纳着元素的“真实”内容，例如文本、图像，或是一个视频播放器。
+ Padding（内边距） - 由内边距边界限制，扩展自内容区域，负责延伸内容区域的背景，填充元素中内容与边框的间距。当其取值为百分比时，该百分比都是相对于包含该元素的块的宽度（相对于该块的百分比）。
+ Border（边框） - 由边框边界限制，扩展自内边距区域，是容纳边框的区域。即围绕在内边距和内容外的边框。
+ Margin（外边距） - 由外边距边界限制，用空白区域扩展边框区域，以分开相邻的元素。当其取值为百分比时，该百分比都是相对于包含该元素的块的宽度（相对于该块的百分比）。

#### 标准盒模型
标准盒模型中 width 指的是内容（Content）区域的宽度；height 指的是内容（Content）区域的高度。标准盒模型下盒子的大小 = width + Border + Padding + Margin。
#### 怪异盒模型
怪异盒模型中的 width 指的是内容（Content）、边框（Border）、内边距（Padding）总和的宽度，即 width = Content + Border + Padding；height 指的是内容（Content）、边框（Border）、内边距（Padding）总和的高度。怪异盒模型下盒子的大小 =width + Margin。
#### 设置盒模型解析模式
box-sizing
  > + content-box： 默认值，border 和padding 不算到 width 范围内，可以理解为是 W3C 的标准模型
  > + border-box：border 和 padding 划归到 width 范围内，可以理解为是 IE 的怪异盒模型
  > + padding-box：将 padding 算入 width 范围
### CSS样式的百分比
#### 宽/高度百分比
子元素宽/高度百分比是基于父级元素的width/height，不包含padding和border
> 高度百分比一定要求父元素设置有height属性，只设置min-height虽然父元素会有高度，但子元素使用百分比无法继承
```css
.box {
	width: 400px;
	height: 200px;
	padding: 20px;
	background-color: red;
}

.item {
	width: 100%;
	height: 100%;
	background-color: blue;
}
```
```html
<div class="box">
    <div class="item">item元素的width为400px 高度为200px</div>
</div>
```
如果父级元素box-sizing: border-box，子级元素大小的百分比基于父级元素真正的大小，即除去padding，border之后的大小

```html
<div class="box">
    <div class="item">父级box的宽度被padding占用40px，item元素的宽度只剩360px</div>
</div>
```
#### 定位元素的宽高百分比
子级定位元素的宽/高100%=父级定位元素width/height+padding的大小
  > 这里继承的并非是直接的父元素，而是父级定位元素，如果直接父元素没有定位，则继续查找更上一级的父元素，直到找到有定位的父元素或者body为止

``` css
.box {
	width: 400px;
	height: 200px;
	padding: 20px;
	background-color: red;
	position: relative;
	border: 10px solid #ccc;
	margin: 50px;
}

.item {
	width: 100%;
	height: 100%;
	background-color: blue;
        position: absolute;
        top:0;
        left:0;
}
```
```html
<div class="box">
    <div class="item">item元素的width=440，item元素的height=240</div>
</div>
```
如果父元素是怪异盒模型，则父元素的width按实际的来计算
####  padding和margin百分比
子元素的padding和margin都是基于父元素的宽度
```css
#box{
    width: 500px;
}
#box > div{
    width: 300px;
    padding-left: 10%;
    margin-left: 10%;
}
<div id="box">
    <div></div>
</div>
```
```js
$(function() {
    console.log($('#box>div').css('paddingLeft'));//50px
    console.log($('#box>div').css('marginLeft'));//50px
})
```
#### line-height百分比
line-height设置百分比是基于当前字体大小，默认值为100%
```css
#box{
    font-size: 20px;
    line-height: 100%;
}

<div  id="box"></div>
```
```js
$(function(){      
    console.log($('#box').css('lineHeight'));//20px
})
```
### 解决flex布局space-between尾部元素左对齐
span方案

## 选择器
### nth-of-type与nth-child的区别
1. nth-of-type是当前元素的兄弟元素的第n个
  > `p:nth-of-type(4)`是第四个p元素，只能是**p元素的第四个**。无论p元素之间加入什么元素都是p元素中的第四个
2. nth-child是当前元素的兄弟节点的第n个当前元素
  > `p:nth-child(4)是取**第四个兄弟节点**的p元素，中间存在其他元素的话会占位
## 偏
### clip-path
### background
```css
background: -webkit-gradient(linear, left top, left bottom, color-stop(0%, rgba(0,0,0,0)), color-stop(100%, rgba(0,0,0,0.8)));

background: -webkit-gradient(linear, left top, left bottom, from(rgba(1,1,1,0)), to(rgba(0,0,0,0.8)))

background: linear-gradient(to bottom, rgba(1,1,1,0), rgba(0,0,0,0.8));
```
### 列表样式
+ list-style-position-相对于对象的内容绘制列表项标记
  - inside
  + outside



改svg颜色

```css
.cls-100 {
  fill: none;
  stroke: #000;
  stroke-width: 0.5px;
  fill-rule: evenodd;
}						
```