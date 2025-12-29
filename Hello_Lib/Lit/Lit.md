[TOC]

## 创建 Lit 组件涉及多个概念：

### 定义组件
Lit 组件实现为自定义元素，在浏览器中注册。


```js
// 通过创建一个继承自 LitElement 的类并在浏览器中注册自定义的类来定义 Lit 组件

export class SimpleGreeting extends LitElement { /* ... */  }
customElements.define('simple-greeting', SimpleGreeting);  

```
### 渲染 
组件有一个渲染方法，用于渲染组件的内容。在渲染方法中，你为组件定义一个模板。

```js
// 为自定义组件添加渲染模板
// 模板可以包含动态内容的占位符的表达式
export class SimpleGreeting extends LitElement { 

    render(){
    return html`<p>Hello from my template.</p>`;
  }
}

```

#### 组合模版
```js
import {LitElement, html} from 'lit';

class MyPage extends LitElement {
  static properties = {
    article: {attribute: false},
  };

  constructor() {
    super();
    this.article = {
      title: 'My Nifty Article',
      text: 'Some witty text.',
    };
  }

  headerTemplate() {
    return html`<header>${this.article.title}</header>`;
  }

  articleTemplate() {
    return html`<article>${this.article.text}</article>`;
  }

  footerTemplate() {
    return html`<footer>Your footer here.</footer>`;
  }

  render() {
    return html`
      ${this.headerTemplate()}
      ${this.articleTemplate()}
      ${this.footerTemplate()}
    `;
  }
}
```

导入使用其他元素组合模板
```js
import {LitElement, html} from 'lit';

import './my-header.js';
import './my-article.js';
import './my-footer.js';

class MyPage extends LitElement {
  render() {
    return html`
      <my-header></my-header>
      <my-article></my-article>
      <my-footer></my-footer>
    `;
  }
}
```
### 响应式属性
属性保存组件的状态。更改组件的一个或多个_响应式属性_会触发更新周期，重新渲染组件。
```js
static properties = {
  name: {},
};

// 等价于
static properties = {
  name: {
    attribute: 'name',
    type: String,
    reflect: false,
    hasChanged: (newVal, oldVal) => newVal !== oldVal,
  },
};
```

简单理解为: **一个被 Lit 接管的 this.xxx，它的读写被包装成 getter / setter，任何一次写入都会触发更新调度。**
```
this.name = 'Tom'
↓
不是普通赋值
↓
Lit 的 setter 被触发
↓
安排一次 render()

```

+ properties里的属性和元素的属性映射
+ HTML的属性改变,lit会自动更新this.[属性],相反的this.[属性]改变不会写回HTML,需要在添加`reflect: true`才会双向更新
+ lit不支持直接在对象里处理计算属性值,正确的处理方法是在setter/getter中处理
  ```js
  // 在 setter 中处理（推荐）
  static properties = {
    name: {},
  };

  set name(value) {
    const old = this._name;
    this._name = value?.trim();
    this.requestUpdate('name', old);
  }

  get name() {
    return this._name;
  }

  //用 getter 派生一个“计算属性”（最常见）
  static properties = {
    name: {},
  };

  get upperName() {
    return this.name?.toUpperCase();
  }

  render() {
    return html`${this.upperName}`;
  }

  // 直接render里处理
  render() {
    return html`${this.name?.toUpperCase()}`;
  }

  ```

#### 属性选项
##### 1. `attribute`
**是否与 HTML attribute 关联**

```js
name: { attribute: true }   // 默认，<my-el name="">
data: { attribute: false } // 仅 JS 内部使用
```
+ true：property ↔ attribute
+ 对象 / 数组通常设为 false

##### 2. `type`
attribute 字符串 ↔ JS 值 的转换方式

```js
count:   { type: Number }
enabled: { type: Boolean }
```
常见规则：

+ String：原样

+ Number：Number(value)

+ Boolean：attribute 是否存在

##### 3. `reflect`
属性变化是否写回 attribute

```js
open: { type: Boolean, reflect: true }
this.open = true;
// <my-el open>
```
常用于：

+ 调试
+ CSS attribute 选择器

##### 4. `hasChanged`
自定义“是否真的变化”

```js
items: {
  hasChanged(newVal, oldVal) {
    return newVal !== oldVal;
  }
}

```
+ 对象 / 数组必须注意this.items.push() 不会触发更新
##### 5. `state`
内部响应式状态（组件私有）

```js
loading: { state: true }
// 等价于：
@state() loading;
```
特点：

+ 响应式 ✔

+ 不生成 attribute ✔

+ 外部不可访问 ✔


### 样式
组件可以定义_封装样式_来控制自身外观。

### 生命周期
Lit 定义了一组回调，你可以重写这些回调以挂钩到组件的生命周期——例如，在元素添加到页面时或组件每次更新时运行代码。