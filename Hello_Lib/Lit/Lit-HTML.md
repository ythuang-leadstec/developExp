[TOC]

## 模板
```js
html`<h1>Hello ${name}</h1>`
```

### 渲染
```js
import {html, render} from 'lit-html';

const name = 'world';
const sayHi = html`<h1>Hello ${name}</h1>`;
render(sayHi, document.body);
```

#### 渲染选项
render方法还接受一个options参数
+ host
+ renderBefore
+ creationScope


## 指令
| 分类 | 指示 (Directive) | 概括 (Description) |
| :--- | :--- | :--- |
| **造型** | `classMap` | 根据对象为元素分配一个类列表。 |
| | `styleMap` | 根据对象为元素设置样式属性列表。 |
| **循环和条件** | `when` | 根据条件渲染两个模板之一。 |
| | `choose` | 根据键值渲染多个模板之一。 |
| | `map` | 使用函数转换可迭代对象。 |
| | `repeat` | 将可迭代对象中的值渲染到 DOM 中，并可选择使用键来启用数据差异和 DOM 稳定性。 |
| | `join` | 将可迭代对象中的值与连接符值交错排列。 |
| | `range` | 创建一个包含序列数字的可迭代对象，可用于迭代特定次数。 |
| | `ifDefined` | 如果属性值已定义，则设置该属性；如果属性值未定义，则删除该属性。 |
| **缓存和变更检测** | `cache` | 更改模板时，缓存已渲染的 DOM，而不是丢弃 DOM。 |
| | `keyed` | 将可渲染值与唯一键关联起来，如果键发生变化，则强制 DOM 重新渲染。 |
| | `guard` | 仅当模板的某个依赖项发生更改时才重新评估模板。 |
| | `live` | 如果属性或属性值与实时 DOM 值不同，而不是与上次渲染的值不同，则设置该属性或属性值。 |
| **引用已渲染的 DOM** | `ref` | 获取模板中已渲染元素的引用。 |
| **渲染特殊值** | `templateContent` | 渲染 `<template>` 元素的内容。 |
| | `unsafeHTML` | 将字符串渲染为 HTML 而不是文本。 |
| | `unsafeSVG` | 将字符串渲染为 SVG 而不是文本。 |
| **异步渲染** | `until` | 渲染占位符内容，直到一个或多个 Promise 解析完成。 |
| | `asyncAppend` | 将值从 `AsyncIterable` 递归地添加到 DOM 中。 |
| | `asyncReplace` | 将 `AsyncIterable` 中的最新值渲染到 DOM 中。 |