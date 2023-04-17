# 前言

- 在用 `node` 做某个服务端项目开发时，因为 `require` 语句写错位置了，出现循环 `require()` 调用，导致在获取某个模块导出的对象时，获取到的是一个空对象 `{}`

- 大致情况如下：

  ```js
  // a.js
  console.log('---- a start ----')
  const b = require('./b.js')
  exports.done = true
  console.log('---- a end ----')
  ```

  ```js
  // b.js
  console.log('---- b start ----')
  const a = require('./a.js')
  console.log('in b, a = %j, a.done = %j', a, a.done)
  console.log('---- b end ----')
  ```

  ```js
  // main.js
  console.log('---- main start ----')
  const a = require('./a')
  console.log('in main, a.done = %j', a.done)
  console.log('---- main end ----')
  ```

  ```js
  // output:
  ---- main start ----
  ---- a start ----
  ---- b start ----
  in b, a = {}, a.done = undefined
  ---- b end ----
  ---- a end ----
  in main, a.done = true
  ---- main end ----
  ```



# 原因

- `Node` 官方文档中有提到此现象：https://nodejs.org/dist/latest-v9.x/docs/api/modules.html#modules_cycles

- **当存在循环 `require()` 调用时，模块在返回时可能尚未完成执行**

- 如下：

  ```js
  // a.js
  console.log('---- a start ----')
  exports.done = false
  const b = require('./b.js')
  console.log('in a, b.done = %j', b.done)
  exports.done = true
  console.log('---- a end ----')
  ```

  ```js
  // b.js
  console.log('---- b start ----')
  exports.done = false
  const a = require('./a.js')
  console.log('in b, a.done = %j', a.done)
  exports.done = true
  console.log('---- b end ----')
  ```

  ```js
  // main.js
  console.log('---- main start ----')
  const a = require('./a')
  const b = require('./b')
  console.log('in main, a.done = %j, b.done = %j', a.done, b.done)
  console.log('---- main end ----')
  ```

- 当 `main.js` 加载 `a.js` 时， `a.js` 依次加载 `b.js` 。此时， `b.js` 尝试加载 `a.js` 。为了防止无限循环，将 `a.js` 导出对象的未完成副本返回给 `b.js` 模块。然后 `b.js` 完成加载，其 `exports` 对象被提供给 `a.js` 模块

- 当 `main.js` 加载两个模块时，它们都已完成。因此，该程序的输出将是：

  ```js
  // output:
  ---- main start ----
  ---- a start ----
  ---- b start ----
  in b, a.done = false
  ---- b end ----
  in a, b.done = true
  ---- a end ----
  in main, a.done = true, b.done = true
  ---- main end ----
  ```



# 解决

- 对于此类情况：最好是在模块设计上优化，以允许循环模块依赖项在应用程序中正常工作
- 相关文章：https://cnodejs.org/topic/5a3e14179807389a1809f656



# 相关

- `Nodejs v8.5.0` 开始支持 `ES` （`ECMAScript`）模块规范的

- `Node.js` 有两个模块系统：`CommonJS` 模块和 `ECMAScript` 模块

- 可以通过 `.mjs` 文件扩展名、`package.json` [`"type"`](http://url.nodejs.cn/KQBZE5) 字段、或 [`--input-type`](http://url.nodejs.cn/oLHTvL) 标志告诉 `Node.js` 使用 `ES` 模块加载器

- 在这些情况之外，`Node.js` 将使用 `CJS` 模块加载器。 参阅[确定模块系统](http://url.nodejs.cn/9hCU3w)了解更多详细信息

- 官方文档：https://nodejs.cn/api-v16/esm.html#enabling

- 既然可以在 `Node` 中使用 `ES` 模块规范，那我们测试一下上面的循环引用在 `ES` 中的表现怎样：

  ```js
  // a.js
  console.log('---- a start ----')
  import {} from './b.js'
  var a = 'aaa'
  function foo() {}
  const bar = 'bar'
  export { a, foo, bar }
  console.log('---- a end ----')
  ```
  
  ```js
  // b.js
  console.log('---- b start ----')
  import * as a from './a.js'
  console.log('a: ', a)
  console.log('---- b end ----')
  ```
  
  ```js
  // main.js
  console.log('---- main start ----')
  import {} from './a.js'
  console.log('---- main end ----')
  ```

- 执行 `main.js`，打印结果如下：

  ```js
  // output:
  ---- b start ----
  a:  [Module: null prototype] {
    a: undefined,
    bar: <uninitialized>,
    foo: [Function: foo]
  }
  ---- b end ----
  ---- a start ----
  ---- a end ----
  ---- main start ----
  ---- main end ----
  ```

- 得出结论：

  - 与 `CJS` 不同，`ESM` 因为有编译器静态分析，所以在模块代码未执行时，可以提前获取到将要导出的对象的部分属性
  - 这里讨论的情况不包括 `export default` 导出的情况
    - `ES` 模块中，当前模块中导出的函数声明，在编译阶段就被提前处理，允许在本模块代码执行之前调用
    - `ES` 模块中，当前模块中导出的 `var` 定义的变量，因为作用域提升，允许在本模块代码执行之前访问（`export default` 导出除外），此时变量还未初始化，值为 `undefined`
    - `ES` 模块中，当前模块中导出的 `let`、`const` 定义的变量，没有作用域提升，不允许在本模块代码执行之前访问
  - 具体情况可以查看 前端工程化基础 > `ESM` 运行原理部分

  

















