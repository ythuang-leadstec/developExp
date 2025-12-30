[TOC]

# Git和Github
代码库中安装 AEM GitHub 机器人后，系统会自动为每个分支创建网站

## 访问
1. 内容预览: `https://<branch>--<repo>--<owner>.aem.page/` 
2. 发布内容: `https://<branch>--<repo>--<owner>.aem.live/`


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