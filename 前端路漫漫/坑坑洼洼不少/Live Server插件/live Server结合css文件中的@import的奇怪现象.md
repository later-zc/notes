# 前言

---

- **下面所述现象只出现在 `chrome` 开发调试工具移动端模式下，在 `pc` 端和安卓真机测试中均未测出**

- 在做某个项目时，在浏览器移动端模式下访问某个页面，通过 `live server` 启动本地服务器来实时更新查看页面，设置某个页面元素 `transition` 动画时，如下：

  ```less
  .btn {
    position: absolute;
    width: 159rem;
    height: 54rem;
    top: 1178rem;
    opacity: 0;
    transfrom: translateX(50px)
    -webkit-transition: all 1s ease 1.2s;
  }
  ```

- 按理说：只有当这些属性发生改变时，才会触发 `transition` 过渡动画

- 但是在没有触发这些属性修改时，仅仅是给元素设置了上面的 `css` 规则，在 `vscode` 中每次保存，或浏览器中清空缓存并硬性加载时，会执行 `transition` 过渡动画（上面 `css` 中的 `opacity` 和 `translateX`），这就很奇怪





# 原因

---

- 查阅了 `live server` 官方文档中有这样一条说明：

  - `liveServer.settings.fullReload`：**`Live Server` 默认会在不完全重新加载浏览器的情况下注入 `CSS` 更改**
  - 您可以通过将此设置设为 `true` 来更改此行为，默认：`false`
  - https://github.com/ritwickdey/vscode-live-server/blob/HEAD/docs/settings.md

- 个人理解：`live server` 默认保存文件时，`css` 相关的修改会在不完全重新加载浏览器的情况下注入

- 后面经过测试发现，不用 `live server`，使用别的一些方式，是不会出现前言中所遇到的情况

- 到这里可以推断出，应该是 `live server` 引起的问题，我们只要设置该配置为 `true` 即可（注意 `vscode` 配置修改之后，需要重启才能生效）

- 但是在后续测试的情况下，发现还有一个因素在影响，如下：

- 此时的文档结构

  ```
  | - a.css
  | - b.css
  | - index.html
  ```

- `index.html`

  ```html
  <!DOCTYPE html>
  <html lang="zh-CN">
  <head>
    // ...
    <link rel="stylesheet" href="./a.css">
  </head>
  <body>  
    <div class="rule"></div>
  </body>
  </html>
  ```

- `css` 文件

  ```css
  /* a.css文件中的内容 */
  @import './b.css';
  
  /* b.css文件中的内容 */
  .rule {
    width: 159px;
    height: 54px;
    transform: translateX(50px);
    background-color: skyblue;
    -webkit-transition: all 4s ease .2s;
  }
  ```

- 测试结果：通过 `live server` 启动本地服务器，打开 `index.html` 在浏览器上（移动端模式下），在 `vscode` 中保存文件修改 或 在直接刷新浏览器的情况下，页面会执行过渡动画（`translateX`，`opacity`），很奇怪，不应该有 `translateX` 的动画才对，而且 `opacity` 动画从 `0=>1`，这又是怎么来的？通过其他方式运行 `index.html` 并没有这个现象

- 但是如果将 `css` 文件结构改为如下，就不会发生上面的现象：

  ```css
  /* a.css */
  .rule {
    width: 159px;
    height: 54px;
    transform: translateX(50px);
    background-color: skyblue;
    -webkit-transition: all 4s ease .2s;
  }
  ```

- 当不再在 `a.css` 中通过 `@import` 发生引入 `b.css` 时，而是直接将 `b.css` 中的样式规则放入到 `a.css` 中时，就不会产生上面的现象

- 到这里，可以推断出上面奇怪的现象跟 `live Server` 本地服务器 和 `@import` 引入 `css` 文件有关





# 解决

---

- 在 `live Server` 插件配置项 `liveServer.settings.fullReload` 设置为 `true` 的前提下（注意 `vscode` 配置修改之后，需要重启才能生效）

- 再结合下面的几种方式可解决

- 方式一：

  - 避免在 `css` 文件中使用 `@import` 直接引入 `css`，而是将多个 `css` 打包到一个 `css` 文件中

  - 我们可以使用 `CSS` 预处理器，如 [Less](https://link.juejin.cn/?target=http%3A%2F%2Flesscss.org%2F)、[SASS](https://link.juejin.cn/?target=https%3A%2F%2Fsass-lang.com%2F)。它们不仅提供了将 `@import` 作为普通 `CSS` 使用的功能，而且还可以将样式合并到单个最终 `CSS` 文件中

  - `Less` 中将 `CSS` 强制打包到单个文件中的技巧

  - `less` 在 `import` 其它 `less` 文件的时候会将其合并到单个文件中。 但是当引入 `css` 文件时，默认不会将 `css` 合并进来 。 使用 `inline` 关键字 将 `*.css` 强制打包到最终的单个文件中

    ```less
    @import './reset.less';
    @import './common.less';
    @import './page/load.less';
    @import './page/hp.less';
    
    // 等同于上面写法
    @import (inline) './reset.css';
    @import (inline) './common.css';
    @import (inline) './page/load.css';
    @import (inline) './page/hp.css';
    ```

  - https://lesscss.org/features/#import-atrules-feature-inline

  - 缺点：单独修改某个引入的 `css` 文件中的 `css` 规则时，并没有在总的入口样式文件中进行更新，必须要更新入口样式文件才行（推荐生产环境的时候使用，开发阶段推荐第三种方式，能自动更新）

- 方式二：将多个 `css` 文件中的规则写入到 一个 `css` 文件中，在 `html` 文件中单独引入该 `css` 即可

  - 缺点：如果页面多样式很多，会很长，阅读起来不方便

- 方式三：使用多个 `link` 标签

  - 每个 `CSS` 都可以通过单独的 `link` 标签下载，如下所示：

    ```html
    <head>
      <link rel="stylesheet" href="a.css" />
      <link rel="stylesheet" href="b.css" />
      ...
    </head>
    ```

  - 缺点：每个样式文件都需要单独 `link` 引入（推荐开发阶段使用，生产环境中使用第二种）