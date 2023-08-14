​	

# 前言

- `js` 原始数值使用 `toFixed()` 报错

  ```js
  1.toFixed(2) // Uncaught SyntaxError: Invalid or unexpected token
  12.toFixed(2) // Uncaught SyntaxError: Invalid or unexpected token
  ```



# 原因

- `xxx.toFixed(2)` 会报错，原因是**语法错误**，`js` 引擎在解析的时候，默认将 `xxx` 后面的那个点 `.` 认为是小数点，所以 `toFixed(2)` 被 `js` 引擎当为小数点后面的数值， 所以报错：无效或意外的标记

- 用 `Number` 构造函数 上别的原型方法来验证这一观点：

  ```js
  1.toString() // Uncaught SyntaxError: Invalid or unexpected token
  Number(1).toString() // '1'
  ```




# 解决

```js
// 1. 多加一个点.
1..toFixed(2) // '1.00'

// 2. 用变量保存
const n = 1
n.toFixed(2) // '1.00'

// 3. 用括号包裹起来（不推荐，一些格式化插件在保存时，会自动去掉多余的括号）
(1).toFixed(2) // '1.00'

// 3. 通过原始类型的包装类将其包装成对象的形式调用，因为js原始值本身是不具备属性和方法的，理论上是不能访问到属性和方法的，但是这一个操作js引擎内部本身会帮我们用包装类转成对象去处理完成，这里之所以可以解决问题，本质上应该还是因为转成了对象，从而让后面那个点.变成对象的属性访问，从而能被js正确解析
Number(1).toFixed(2) // '1.00'

// 4. 乘以 1.0（推荐）
(1.*1.0).toFixed(2) //  1.00'
```

