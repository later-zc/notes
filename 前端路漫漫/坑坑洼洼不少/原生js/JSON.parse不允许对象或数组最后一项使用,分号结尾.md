```js
// both will throw a SyntaxError
JSON.parse("[1, 2, 3, 4, ]")
JSON.parse('{"foo" : 1, }')


// 去掉对象数组中最后一项的分号：
JSON.parse("[1, 2, 3, 4]")
JSON.parse('{"foo" : 1}')
```

