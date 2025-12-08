# 关于拖拽表头实现移动表格栏目

1. 对拖拽表头的hover事件先定义
2. 定义拖拽事件
    - 获取拖拽的类名,找到对应的th元素
    - 克隆th元素绝对定位到body,坐标为拖拽时的target,具体自定义
3. 监听mousemove.customDrag,根据target移动clone元素的坐标,为target对应的th添加class

4. 处理mouseup.customDrag事件,交换列
  - draggedIndex = drag target所在th的index
  - targetIndex = 鼠标松开时对应的th所在的index
  ```js
    if (fromIndex < toIndex) {
        toHeader.after(fromHeader);
    } else {
        toHeader.before(fromHeader);
    }
    
    // Swap corresponding cells in all rows
    $table.find('tbody tr').each(function() {
        var row = $(this);
        var fromCell = row.find('td, th').eq(fromIndex);
        var toCell = row.find('td, th').eq(toIndex);
        
        if (fromIndex < toIndex) {
            toCell.after(fromCell);
        } else {
            toCell.before(fromCell);
        }
    });
  ```

5. 清理拖拽痕迹
  - 清除clone元素
  - 清除拖拽加的类名
  - off掉drag事件

6. 绑定全局调用