[TOC]

# Git和Github
代码库中安装 AEM GitHub 机器人后，系统会自动为每个分支创建网站

## 访问
1. 内容预览: `https://<branch>--<repo>--<owner>.aem.page/` 
2. 发布内容: `https://<branch>--<repo>--<owner>.aem.live/`

### my github
#### Create your content:
https://da.live/start?org=ythuang-leadstec&site=my-aem-eds
#### Check your site:
Preview: https://main--my-aem-eds--ythuang-leadstec.aem.page/

Live: https://main--my-aem-eds--ythuang-leadstec.aem.live/

Add users:
Use the admin tool: https://labs.aem.live/tools/user-admin/index.html


# 代码开发

## 模块
1. 编辑component-definition.json文件，添加以下新引用块的定义
```js
{
  "title": "Quote",
  "id": "quote",
  "plugins": {
    "xwalk": {
      "page": {
        "resourceType": "core/franklin/components/block/v1/block",
        "template": {
          "name": "Quote",
          "model": "quote",
          "quote": "<p>Think, McFly! Think!</p>",
          "author": "Biff Tannen"
        }
      }
    }
  }
}

```

2. 编辑component-models.json,添加model定义
```js
{
  "id": "quote",
  "fields": [
     {
       "component": "richtext",
       "name": "quote",
       "value": "",
       "label": "Quote",
       "valueType": "string"
     },
     {
       "component": "text",
       "valueType": "string",
       "name": "author",
       "label": "Author",
       "value": ""
     }
   ]
}

```

3. 编辑component-filters.json,将引用块添加到过滤器

```js
{
  "id": "section",
  "components": [
    "text",
    "image",
    "button",
    "title",
    "hero",
    "cards",
    "columns",
    "quote"
   ]
}

```

4. 往blocks添加组件

5. [页面验证](https://www.aem.live/developer/universal-editor-blocks)
  验证内容是否已发布。链接将类似于：`https://<branch>--<repo>--<owner>.aem.page`

6. 使用其他分支工作
  可以`?ref=<branch>`在通用编辑器地址栏中添加 `<branch_name>` 以从您的分支加载页面

### Block模板
```js
// 1. 引入依赖 (通常是 aem.js 或第三方库)
import { createOptimizedPicture } from '../../scripts/aem.js';

// 2. 默认导出 decorate 函数 (这是入口)
export default function decorate(block) {
  
  // 3. 解析数据 (从 DOM 中读取)
  // EDS 会把 Word/Excel 内容转成 div > div 结构传给你
  const [imageWrapper, textWrapper] = block.children;

  // 4. 转换 DOM (构建你的组件结构)
  const h3 = textWrapper.querySelector('h3');
  const p = textWrapper.querySelector('p');
  
  // 5. 优化图片 (使用核心 API)
  const img = imageWrapper.querySelector('img');
  if (img) {
    imageWrapper.replaceChildren(createOptimizedPicture(img.src, img.alt, false, [{ width: '750' }]));
  }

  // 6. 最终组装
  block.textContent = ''; // 清空原始 DOM
  const container = document.createElement('div');
  container.className = 'my-block-container';
  container.append(imageWrapper, textWrapper);
  block.append(container);
}
```

### 最佳实践
AEM Boilerplate (基础模板)

这是所有 EDS 项目的起点。
看 blocks/columns/columns.js：学习如何处理多列布局。
看 blocks/hero/hero.js：学习如何处理图片优化 (createOptimizedPicture)。
GitHub: adobe/aem-boilerplate
EDS Block Collection (官方组件库)

这里有几十个高质量的 Block 实现。
比如你想写一个轮播图，就去搜 carousel，看看它的 decorate 是怎么写的。
网址: https://www.aem.live/developer/block-collection


# 开发步骤

1. 从[adobe github模板](https://github.com/adobe-rnd/aem-boilerplate-xwalk)创建repository

2. 更改fstab.yaml文件为aem实例url

3. 更改paths.json文件

4. 添加block

5. 将新block添加今_section.json筛选规则

6. 运行`npm run build:json`

7. 提交至github

8. 线上aem实例编辑内容并发布到preview

9. 本地运行`aem up`跑本地环境(本地环境是镜像的preview环境)


## 模型定义
可以在component属性中使用的值


**文档链接**:
[Model Definitions, Fields, and Component Types - Adobe Experience League](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/developing/universal-editor/field-types)

### 核心字段类型速查表

根据文档，以下是你可以在 `component` 属性中使用的所有值：

| 组件类型 (Component Type) | JSON 值 (`"component": "..."`) | 用途 |
| :--- | :--- | :--- |
| **Text** | `text` | 单行文本输入 (最常用) |
| **Rich Text** | `richtext` | 富文本编辑器 (多行、格式化) |
| **Reference** | `reference` | 引用 AEM Assets (图片) |
| **AEM Content** | `aem-content` | 引用 AEM 页面或其他资源 |
| **Select** | `select` | 下拉单选框 |
| **Multiselect** | `multiselect` | 下拉多选框 |
| **Checkbox Group** | `checkbox-group` | 多选复选框组 |
| **Radio Group** | `radio-group` | 单选按钮组 |
| **Boolean** | `boolean` | 开关 (True/False) |
| **Number** | `number` | 数字输入 |
| **Date Time** | `date-time` | 日期/时间选择器 |
| **Container** | `container` | 容器 (用于分组字段) |
| **Tab** | `tab` | 选项卡 (用于在编辑器中分页显示字段) |
| **AEM Tag** | `aem-tag` | AEM 标签选择器 |
| **Content Fragment** | `aem-content-fragment` | 内容片段选择器 |
| **Experience Fragment** | `aem-experience-fragment` | 体验片段选择器 |

### 常用字段示例

**1. 图片引用 (Reference)**
```json
{
  "component": "reference",
  "label": "Image",
  "name": "image",
  "valueType": "string"
}
```

**2. 链接选择 (AEM Content)**
```json
{
  "component": "aem-content",
  "label": "Link Target",
  "name": "link",
  "valueType": "string"
}
```

**3. 下拉菜单 (Select)**
```json
{
  "component": "select",
  "label": "Alignment",
  "name": "align",
  "valueType": "string",
  "options": [
    { "name": "Left", "value": "left" },
    { "name": "Center", "value": "center" }
  ]
}
```

**4. 选项卡分组 (Tab)**
如果你字段太多，可以用 Tab 分开：
```json
[
  { "component": "tab", "label": "Content", "name": "tab1" },
  { "component": "text", "label": "Title", "name": "title" },
  
  { "component": "tab", "label": "Settings", "name": "tab2" },
  { "component": "boolean", "label": "Show Border", "name": "border" }
]
```

## 常用脚本函数

`scripts/` 文件夹是 AEM Edge Delivery Services 项目的核心引擎。这里面包含了所有用于页面加载、组件装饰（Decoration）、资源加载和性能优化的工具函数。

以下是按功能分类的函数统计和说明：

### 1. 组件装饰 (Decoration)
这是开发中最常用的部分。当你创建新 Block 或需要处理 DOM 时，会用到这些。

| 函数名 | 来源 | 描述 |
| :--- | :--- | :--- |
| `decorateMain(main)` | scripts.js | 装饰整个 `<main>` 元素，按顺序调用其他装饰函数。 |
| `decorateBlock(block)` | aem.js | **常用**: 初始化单个 Block，添加基础类名，处理状态。 |
| `decorateBlocks(main)` | aem.js | 自动查找并装饰容器内所有的 Block。 |
| `decorateSections(main)` | aem.js | 将内容按 `---` 分隔符包裹成 `.section` div。 |
| `decorateButtons(element)` | aem.js | 自动将单独的链接 `<a>` 转换为按钮样式 (`.button`)。 |
| `decorateIcons(element)` | aem.js | 查找 `<span class="icon-name">` 并替换为 SVG 图片。 |
| `decorateTemplateAndTheme()` | aem.js | 根据 Metadata 给 `<body>` 添加模板和主题类名。 |
| `buildBlock(name, content)` | aem.js | **常用**: 手动创建一个 Block 的 DOM 结构（常用于 Auto Blocking）。 |
| `wrapTextNodes(block)` | aem.js | 将 Block 里的纯文本节点包裹在 `<p>` 标签中，方便样式控制。 |

### 2. 资源加载 (Resource Loading)
用于动态加载 CSS、JS 或其他资源。

| 函数名 | 来源 | 描述 |
| :--- | :--- | :--- |
| `loadCSS(href)` | aem.js | 异步加载 CSS 文件。 |
| `loadScript(src, attrs)` | aem.js | 异步加载 JS 文件。 |
| `loadBlock(block)` | aem.js | 加载指定 Block 的 JS 和 CSS 文件。 |
| `loadHeader(header)` | aem.js | 加载并装饰 Header。 |
| `loadFooter(footer)` | aem.js | 加载并装饰 Footer。 |
| `waitForFirstImage(section)` | aem.js | 等待第一张图片加载完成（用于优化 LCP）。 |

### 3. 工具函数 (Utilities)
开发 Block 时经常会用到的小工具。

| 函数名 | 来源 | 描述 |
| :--- | :--- | :--- |
| `readBlockConfig(block)` | aem.js | **常用**: 读取 Block 顶部的 Key-Value 配置表（通常用于 Section Metadata）。 |
| `getMetadata(name)` | aem.js | 获取 `<head>` 中 `<meta>` 标签的值。 |
| `toClassName(name)` | aem.js | 将字符串转换为 CSS 类名格式（小写、连字符）。 |
| `toCamelCase(name)` | aem.js | 将字符串转换为驼峰命名（用于 Dataset 属性）。 |
| `createOptimizedPicture(...)` | aem.js | **常用**: 生成带有 WebP 支持和响应式断点的 `<picture>` 标签。 |
| `moveAttributes(from, to)` | scripts.js | 将属性从一个元素移动到另一个元素。 |
| `moveInstrumentation(...)` | scripts.js | **重要**: 在修改 DOM 结构时，保留 Universal Editor 的编辑属性 (`data-aue-*`)。 |

### 4. 编辑器支持 (Editor Support)
| 函数名 | 来源 | 描述 |
| :--- | :--- | :--- |
| `applyChanges(event)` | editor-support.js | 处理 Universal Editor 的实时更新事件，重新渲染组件。 |

### 开发者建议
*   **最常用**: `readBlockConfig`, `createOptimizedPicture`, `toClassName`。
*   **Auto Blocking**: 如果你要根据页面元数据自动生成 Block（比如在文章页自动加一个 Hero），你会用到 `buildBlock` 和 `decorateBlock`。
*   **DOM 操作**: 如果你在重构 DOM（比如把 `<div>` 换成 `<ul>`），一定要记得用 `moveInstrumentation`，否则在 Universal Editor 里可能会无法点击编辑。


### 使用实例
Added 1 todo

以下是几个最常用的 AEM EDS 工具函数的使用场景和代码示例：

#### 1. `readBlockConfig` - 读取配置型 Block
**场景**: 你有一个 Block，作者在里面填的是 Key-Value 对（例如配置倒计时的时间、背景色等），而不是直接显示的内容。

**DOM 结构 (传入的 block)**:
```html
<div class="countdown">
  <div>
    <div>Target Date</div>
    <div>2025-12-31</div>
  </div>
  <div>
    <div>Theme</div>
    <div>Dark</div>
  </div>
</div>
```

**代码示例**:
```javascript
import { readBlockConfig } from '../../scripts/aem.js';

export default function decorate(block) {
  // 1. 读取配置
  const config = readBlockConfig(block);
  
  // config 现在是: { "target-date": "2025-12-31", "theme": "Dark" }
  
  // 2. 清空原有内容 (因为我们已经读完了，不需要显示原始表格)
  block.textContent = '';
  
  // 3. 根据配置渲染新内容
  const timer = document.createElement('div');
  timer.classList.add('timer-display', config.theme.toLowerCase());
  timer.innerText = `Counting down to ${config['target-date']}`;
  block.append(timer);
}
```

---

#### 2. `createOptimizedPicture` - 生成响应式图片
**场景**: 你在 JS 里动态创建了一个图片（例如轮播图的图片），需要它自动支持 WebP 和不同屏幕尺寸的优化。

**代码示例**:
```javascript
import { createOptimizedPicture } from '../../scripts/aem.js';

export default function decorate(block) {
  const imageSrc = '/assets/my-banner.jpg';
  const imageAlt = 'A beautiful banner';
  
  // 生成 <picture> 标签
  // 第三个参数 true 表示 eager loading (如果是首屏图片)
  // 第四个参数定义断点
  const picture = createOptimizedPicture(imageSrc, imageAlt, true, [
    { media: '(min-width: 1000px)', width: '2000' },
    { media: '(min-width: 600px)', width: '1200' },
    { width: '600' }
  ]);
  
  block.append(picture);
}
```

---

#### 3. `toClassName` - 安全的类名转换
**场景**: 你想把用户输入的文本（可能包含空格、大写、特殊符号）用作 CSS 类名。

**代码示例**:
```javascript
import { toClassName } from '../../scripts/aem.js';

const userInput = "Hello World! @2025";
const className = toClassName(userInput);

console.log(className); // 输出: "hello-world-2025"

// 应用场景：根据 Tag 给文章加类名
const tag = "News & Events";
document.body.classList.add(toClassName(tag)); // body.classList.add('news-events')
```

---

#### 4. `moveInstrumentation` - 保留编辑器功能
作用是**搬运编辑器的‘锚点’（Anchor）**

> 想象你在装修房子：

> 原始 DOM (Source)：这是开发商交房时的毛坯房。墙上贴着一张便利贴（Instrumentation），写着：“这面墙对应图纸上的‘主卧墙’”。
你的代码 (Decorate)：你进场装修，把这面墙敲了，重新砌了一面更漂亮的装饰墙（New DOM）。
问题：如果你把旧墙敲碎运走时，连同那张便利贴也扔了，那么当设计师（Universal Editor）来验收时，他看着新墙，不知道这面墙对应图纸上的哪里，也就无法对其进行修改。
moveInstrumentation 的作用就是：在你敲掉旧墙（原始 DOM）之前，把那张便利贴（data-aue-* 属性）撕下来，贴到你新砌的墙（新 DOM）上。
**场景**: 你在重构 DOM 结构（例如把默认的 `<div>` 结构改成语义化的 `<article>` 或 `<ul>`），但不想破坏 Universal Editor 的点击编辑功能。

**代码示例**:
```javascript
import { moveInstrumentation } from '../../scripts/scripts.js';

export default function decorate(block) {
  // 假设 block.children[0] 是我们要改造的原始行
  const originalRow = block.children[0];
  
  // 创建一个新的语义化标签
  const newArticle = document.createElement('article');
  newArticle.innerHTML = originalRow.innerHTML;
  
  // 关键步骤：把编辑器的钩子属性从旧元素移到新元素
  moveInstrumentation(originalRow, newArticle);
  
  // 替换 DOM
  originalRow.replaceWith(newArticle);
}
```

---

#### 5. `buildBlock` - 自动生成 Block (Auto Blocking)
**场景**: 你想在所有页面的顶部自动插入一个 "Breadcrumb" (面包屑) Block，而不需要作者在每个页面手动添加。

**代码示例 (在 scripts.js 的 `buildAutoBlocks` 函数中)**:
```javascript
import { buildBlock } from './aem.js';

function buildAutoBlocks(main) {
  try {
    // 1. 创建 Block 的 DOM 结构
    // buildBlock('block-name', content)
    // content 可以是字符串、元素或数组
    const breadcrumbBlock = buildBlock('breadcrumb', { elems: [] });
    
    // 2. 找到插入位置 (例如 main 的最前面)
    const section = main.querySelector('div');
    section.prepend(breadcrumbBlock);
    
  } catch (error) {
    console.error('Auto Blocking failed', error);
  }
}
