[TOC]

# 创建 Lit 组件涉及多个概念：

## 定义组件

Lit 组件实现为自定义元素，在浏览器中注册。

```js
// 通过创建一个继承自 LitElement 的类并在浏览器中注册自定义的类来定义 Lit 组件

export class SimpleGreeting extends LitElement {
  /* ... */
}
customElements.define("simple-greeting", SimpleGreeting);
```

## 渲染

组件有一个渲染方法，用于渲染组件的内容。在渲染方法中，你为组件定义一个模板。

```js
// 为自定义组件添加渲染模板
// 模板可以包含动态内容的占位符的表达式
export class SimpleGreeting extends LitElement {
  render() {
    return html`<p>Hello from my template.</p>`;
  }
}
```

### 组合模版

```js
import { LitElement, html } from "lit";

class MyPage extends LitElement {
  static properties = {
    article: { attribute: false },
  };

  constructor() {
    super();
    this.article = {
      title: "My Nifty Article",
      text: "Some witty text.",
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
      ${this.headerTemplate()} ${this.articleTemplate()} ${this.footerTemplate()}
    `;
  }
}
```

导入使用其他元素组合模板

```js
import { LitElement, html } from "lit";

import "./my-header.js";
import "./my-article.js";
import "./my-footer.js";

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

## 响应式属性

属性保存组件的状态。更改组件的一个或多个*响应式属性*会触发更新周期，重新渲染组件。

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

- properties 里的属性和元素的属性映射
- HTML 的属性改变,lit 会自动更新 this.[属性],相反的 this.[属性]改变不会写回 HTML,需要在添加`reflect: true`才会双向更新
- lit 不支持直接在对象里处理计算属性值,正确的处理方法是在 setter/getter 中处理

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

### 属性选项

#### 1. `attribute`

**是否与 HTML attribute 关联**

```js
name: {
  attribute: true;
} // 默认，<my-el name="">
data: {
  attribute: false;
} // 仅 JS 内部使用
```

- true：property ↔ attribute
- 对象 / 数组通常设为 false

#### 2. `type`

attribute 字符串 ↔ JS 值 的转换方式

```js
count: {
  type: Number;
}
enabled: {
  type: Boolean;
}
```

常见规则：

- String：原样

- Number：Number(value)

- Boolean：attribute 是否存在

#### 3. `reflect`

属性变化是否写回 attribute

```js
open: { type: Boolean, reflect: true }
this.open = true;
// <my-el open>
```

常用于：

- 调试
- CSS attribute 选择器

#### 4. `hasChanged`

自定义“是否真的变化”

```js
items: {
  hasChanged(newVal, oldVal) {
    return newVal !== oldVal;
  }
}

```

- 对象 / 数组必须注意 this.items.push() 不会触发更新

#### 5. `state`

内部响应式状态（组件私有）

```js
loading: { state: true }
// 等价于：
@state() loading;
```

特点：

- 响应式 ✔

- 不生成 attribute ✔

- 外部不可访问 ✔

## 样式

组件可以定义*封装样式*来控制自身外观。
Lit 里的 static styles 是为“组件封装”服务的，不是为了取代传统 CSS 文件。

因此正常开发还是使用外部 css

### 通过 Shadow DOM + 外部 CSS 实现组件级样式隔离

CSS 被注入到组件的 ShadowRoot,样式不会影响外部元素。

```js
// my-element.ts
import { LitElement, html, css } from "lit";
import styles from "./my-element.css" assert { type: "css" };

export class MyElement extends LitElement {
  static styles = styles;

  render() {
    return html`<p>Hello</p>`;
  }
}
```

## 生命周期

Lit 定义了一组回调，你可以重写这些回调以挂钩到组件的生命周期——例如，在元素添加到页面时或组件每次更新时运行代码。

## 事件

在模板中使用 @ 表达式向组件模板中的元素添加事件监听器

```js
// my-element.ts
import { LitElement, html, css } from "lit";
import styles from "./my-element.css" assert { type: "css" };

export class MyElement extends LitElement {
  static styles = styles;

  render() {
    return html` <button @click=${this._handleClick}>Click me</button> `;
  }

  _handleClick() {
    console.log("Clicked!");
  }
}
```

## shadow dom

### 访问 shadow dom 中的节点

#### renderRoot

Lit 将组件渲染到其 renderRoot 中，可以使用 this.shadowRoot.querySelector()来访问 shadow dom 中的节点。

#### @query,@queryAll 和 queryAsync 装饰器

```js
@query('#first')
  _first;
// 等价于
get _first() {
  return this.renderRoot?.querySelector('#first') ?? null;
}

@queryAll('button')
  _buttons;
// 等价于
get _buttons() {
  return this.renderRoot?.querySelectorAll('button') ?? null;
}
```

@queryAsync 类似于@query,但是返回的是一个 Promise,等待节点出现后才 resolve。

#### createRenderRoot
默认实现创建一个开放的 shadow root，并向其添加在 static styles 类字段中设置的任何样式

当返回 this 时，Lit 就不会创建 Shadow Root,会直接把模板内容渲染为元素的直接子节点。这被称为 Light DOM（也就是我们平时最普通的 DOM 结构）。
```js
  createRenderRoot() {
    return this; // 返回组件实例本身，而不是 shadow root
  }
```
## 装饰器
| 装饰器 | 说明 | 相关概念 |
|--------|------|----------|
| @customElement | 定义自定义元素 | [定义](https://lit.tips/docs/components/defining/) |
| @eventOptions | 添加事件监听器选项 | [事件](https://lit.tips/docs/components/events/#event-options-decorator) |
| @property | 定义公共属性 | [属性](https://lit.tips/docs/components/properties/#declare-with-decorators) |
| @state | 定义私有状态属性 | [属性](https://lit.tips/docs/components/properties/#declare-with-decorators) |
| @query | 定义返回组件模板中元素的属性 | [Shadow DOM](https://lit.tips/docs/components/shadow-dom/#query) |
| @queryAll | 定义返回组件模板中元素列表的属性 | [Shadow DOM](https://lit.tips/docs/components/shadow-dom/#query-all) |
| @queryAsync | 定义返回解析为组件模板中元素的 promise 的属性 | [Shadow DOM](https://lit.tips/docs/components/shadow-dom/#query-async) |
| @queryAssignedElements | 定义返回分配给特定插槽的子元素的属性 | [Shadow DOM](https://lit.tips/docs/components/shadow-dom/#query-assigned-nodes) |
| @queryAssignedNodes | 定义返回分配给特定插槽的子节点的属性 | [Shadow DOM](https://lit.tips/docs/components/shadow-dom/#query-assigned-nodes) |

