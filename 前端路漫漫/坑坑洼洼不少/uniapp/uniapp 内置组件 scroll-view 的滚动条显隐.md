# 前言

- `uniapp` 中 `scroll-view` 内置组件的滚动条不显示，查看官网文档，有个组件属性 `show-scrollbar`：控制是否出现滚动条，默认 `false`，但是却只支持 `App-nvue 2.1.5+` 的环境

- 对此感到很奇怪，为什么会不支持微信小程序？

- 于是，查看微信小程序 `scroll-view` 组件，发现也有 `show-scrollbar` 属性，默认 `false`，滚动条显隐控制 (同时开启 `enhanced` 属性后生效)

- 由此可见，微信小程序是支持滚动条的，但是为什么 `uniapp` 中的 `scroll-view` 不行呢？

- 带着疑惑，去网上查找相关文章，发现一篇：[uniapp scroll-view滚动条不显示](https://blog.csdn.net/LJJONESEED/article/details/123986312)，虽然不是解释上面问题，但对于解决当下的需求有了帮助

  ```css
  // 以下内容来自文章
  
  // 我是用了scroll-view可以滚动，但是不出现滚动条，vue页面。
  // 在调试中发现是被这个样式影响了
  
  ::-webkit-scrollbar {
    display: none;
    width: 0 !important;
    height: 0 !important;
    -webkit-appearance: none;
    background: transparent;
  }
  ```

- 于是，我在微信小程序中查找，微信小程序的控制台中并无显示有该样式

- 猜测是微信开发者工具的调试器问题，于是将项目跑在 `chrome` 浏览器中，果不其然，能找到该样式，从而就定位到问题所在



# 解决

- 添加对应样式覆盖即可

  ```css
  .scroll-bar {
    /*滚动条整体样式*/
    ::-webkit-scrollbar {
      display: block;
      width: 4px !important;
      height: 4px !important;
      -webkit-appearance: auto !important;
      background-color: #ccc !important;
    }
    /*滚动条上的滚动滑块*/
    /deep/ ::-webkit-scrollbar-thumb {
      border-radius: 10px !important;
      /* box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2) !important; */
      background: #ccc !important;
    }
  
    /*滚动条轨道*/
    /deep/ ::-webkit-scrollbar-track {
      /* box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2) !important; */
      /* border-radius: 10px !important; */
      background: #ffffff !important;
    }
  }
  ```



# 相关资料

- `uniapp` 官网文档 `scroll-view`：https://uniapp.dcloud.net.cn/component/scroll-view.html
- 微信小程序官网文档 `scroll-view`：https://developers.weixin.qq.com/miniprogram/dev/component/scroll-view.html
- 相关文章：https://blog.csdn.net/LJJONESEED/article/details/123986312
- `MDN`：https://developer.mozilla.org/zh-CN/docs/Web/CSS/::-webkit-scrollbar

