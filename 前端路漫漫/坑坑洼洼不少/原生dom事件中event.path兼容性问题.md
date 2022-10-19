# **一.  缘由**

---

爱音乐翻牌项目中，视频播放采用 video 标签播放，用的是监听 dom 原生 click 事件触发视频暂停，

ios 端中 event 没有 path 属性，安卓没问题，

所以导致 ios 设备无法暂停视频。

# **二.  解决**

---

```js
兼容方案：
element.onClick(event) {
	const ev = window.event || event;
	const path = event.path || (event.composedPath && event.composedPath())
	console.log(path)
}
```



[引用]: https://blog.csdn.net/qq_39264561/article/details/110084170

