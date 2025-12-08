[TOC]

# 使用px时遇到的坑
需求是自定义input checkbox的样式

**遇到的坑是:**设置了垂直水平居中的元素盒子在不同的分辨率下有所偏移

```html
  <style>
    .clause-content {
      display: flex;
      flex-direction: row;
      align-items: start;
    }

    .clause-input {
      display: inline-block;
      vertical-align: middle;
      width: 15px;
      height: 15px;
      cursor: pointer;
      position: relative;
      background-color: #fff;
      margin-right: 12px;
      border: 1px solid rgba(237, 30, 14, 0.15);
    }

    .clause-input input {
      opacity: 0;
    }

    .clause-input input:checked+i {
      width: 10px;
      height: 10px;
      position: absolute;
      left: 50%;
      top: 50%;
      margin-left: -5px;
      margin-top: -5px;
      /* transform: translate(-50%, -50%); */
      background-color: #ED1C24;
    }
  </style>
  <div class="clause-content">
    <label class="clause-input checkbox-hack">
      <input type="checkbox" name="clauseEnquiry" class="js_clauseEnquiry" id="clauseEnquiry">
      <i></i>
    </label>
    <label class="clause-text" for="clauseEnquiry">clause.</label>
  </div>
```

## px
> 是图像显示的基本单元，既不是一个确定的物理量，也不是一个点或者小方块，而是一个抽象概念

+ px是一个绝对单位,也可以被视为相对单位,像素单位相对的是设备像素.在同一样的设备上,每一个css像素所代表的物理像素是可以变化的,在不同的设备之间,也是变化的
+ 在不同的分辨率下，像素点的大小是不同的。css的px不是物理像素,而是逻辑像素,为了适配不同的分辨率,经过像素引擎处理过的逻辑像素.
+ 只要使用px,像素点在不同分辨率下就有可能会出现移位的问题(个人理解是逻辑像素的处理计算存在小数,当在很细的效果下就会出现偏差问题)

### 解决方案是换成相对单位来进行计算,尽量避开小数点三位

```html
.clause-content {
  display: flex;
  flex-direction: row;
  align-items: start;
}

.clause-input {
  display: inline-block;
  vertical-align: middle;
  width: 1rem;
  height: 1rem;
  cursor: pointer;
  position: relative;
  background-color: #fff;
  margin-right: 0.8rem;
  border: 0.1rem solid rgba(237, 30, 14, 0.15);
}

.clause-input input {
  opacity: 0;
}

.clause-input input:checked + i {
  width: 0.6rem;
  height: 0.6rem;
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -0.3rem;
  margin-top: -0.3rem;
  /* transform: translate(-50%, -50%); */
  background-color: #ed1c24;
}
```

### 其他实现方法
使用`background-image`实现
`appearance`用于控制 UI 控件的基于操作系统主题的原生外观
```html
.custom-checkbox {
  appearance: none;
  width: 15px;
  height: 15px;
  border: 1px solid rgba(237, 30, 14, 0.15);
  background-image: var(--form-check-bg-img);
  background-size: 10px 10px;
  background-repeat: no-repeat;
  background-position: center;
  &:checked {
    --form-check-bg-img: linear-gradient(to right, blue, blue);
  }
}
```

