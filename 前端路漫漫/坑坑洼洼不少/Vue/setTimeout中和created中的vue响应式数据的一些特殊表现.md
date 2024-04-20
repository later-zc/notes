对于data中初始化赋值的空对象和空数组，但未显式定义其中的属性或子项时，如下

```vue
<template>
<div>{{ obj.name }}</div>
<div>{{ arr[0] && arr[0].num }}</div>
<button @click="changeVal">click</button>
</template>

<script>
  data() {
    return {
      obj: {}.
      arr: [],
  	}
  }
</script>
```

在实际使用中，对像obj和arr这样的响应式对象或数组进行操作的时候，经常会通过地址引用直接对其增加或修改属性或子项，如下：

情况一：普通方法中直接修改或赋值

```js
changeVal() {
  // 对象
  this.obj.name = 'aaa' // 内存中该变量已经被作用了，但是视图层绑定的值没有变化
  this.$set(this.obj, 'name', 'aaa') // 视图会更新

  // 数组
  this.arr[0] = { num: 111 } // 视图不会更新
  this.$set(this.arr, 0, { num: 111 }) // 视图会更新
}
```

情况二：created生命周期函数中进行属性新增或赋值，会具有等同于在data中显式定义属性的效果，如果使用setTimeout，created生命周期完成后，执行setTimeout的回调时，此时其中的操作就已经不具备在created中同步代码等同的效果。

```js
created() {
  // 对象
  // 1.直接赋值 
  this.obj.name = 111 // 视图更新，`created()` 钩子在调用之前 `render()` 被调用，因此即使该属性 `obj.name` 不是响应式的，它在组件呈现时就存在，除非通过 `setTimeout()` 延迟，https://github.com/vuejs/vue/issues/9597
  // 2.setTimeout中
  setTimeout(() => {
    this.obj.name = '123' // 视图不会更新
    this.$set(this.obj, 'name', '123') // 视图不会更新
  }, 500)

  // 数组
  this.arr.push({ num: 111 }) // 视图更新
  this.arr[0] = { num: 111 } // 视图更新
  setTimeout(() => {
    this.arr[0] = { num: 111 } // 视图不会更新
    this.arr.push({ num: 111 }) // 视图更新
    this.$set(this.arr, 0, { num: 111 }) // 视图更新
  }, 500)
}
```

> 总结：
>
> - 根据测试结果可以看出，对于这类情况，最好是使用this.$set去修改响应式数据源，避免踩坑。
>
> - 还有一些特殊情况，在created生命周期函数中，同样的代码在setTimeout中使用和不在setTimeout中使用时，具有不同的效果，使用this.$set是避免踩坑的方式
>
>   - 平板项目中，固件上传代码中，遇到的情况，使用时：
>
>     ```js
>     xxx(row) {
>       row.num = 10// 视图更新
>       // 1.
>       setTimeout(() => {
>         row.num = 20 // 视图不更新
>       })
>     
>       // 2.
>       this.$nextTick(() => {
>         setTimeout(() => {
>           row.num = 10 // 窗口发生变化时触发更新，猜测是跟vue响应式和异步更新队列机制有关
>         })
>       })
>     
>       // 3.
>       setTimeout(() => {
>         this.$nextTick(() => {
>           row.num = 10 // 窗口发生变化时触发更新
>         })
>       })
>     }
>     ```
>
>     - 对于上面示例中，解决方式是使用 `Vue.set` 去修改row的数据源