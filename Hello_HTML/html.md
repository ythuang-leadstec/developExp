[TOC]

# 元素属性

## contenteditable
属性指定元素内容是否可编辑
可用于自定义input,例如单横线不使用textarea的情况下进行内容换行


## 媒体标签

### video
#### 设置
```httml
<video src="../img/banner.mp4" controls loop controlslist="nodownload noplaybackrate nofullscreen" disablePictureInPicture></video>
```
+ controls 支持播放控制
+ loop 循环播放
+ controlslist="nodownload noplaybackrate" 菜单(三个点)
  - nodownload 不要下载按钮
  - noplaybackrate 不要播放速度按钮
  - nofullscreen 不要全屏按钮
+ disablePictureInPicture 不要画中画按钮

#### 兼容性
没有自动播放的视频在ios不显示略缩图,只有一个播放按钮

**两个解决方案**
1. 在资源url后面添加`#t=xxx`(xxx为时间(秒)),跳转到设置的时间
2. 添加poster属性,自定义略缩图

#### 播放问题
##### 关闭了(隐藏)视频仍旧有声音
**原因**: 元素只是进行了隐藏,没有销毁,视频仍旧在播放中,所以有声音
**解决方案**: 销毁video
```js
_video.pause()
_video.removeAttribute('src') //清空 VIDEO 元素的 src 属性值，停止加载视频
_video.load() //确保所有相关数据都被卸载
_video = null //将 VIDEO 元素的事件监听器设为 null（如果有的话），以避免内存泄漏。
              //最后将 VIDEO 元素赋值为 null，以释放其占用的内存。
```
