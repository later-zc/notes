## 前言

直接看demo吧：

```vue
<template>
  <el-input v-if="visible" ref="inputRef">111</el-input>
  <span v-else>内容xxx</span>
</template>

<script>
  export default {
    data() {
      return {
        visible: false,
      }
    },
    methods: {
      switchInputVisible() {
        this.visible = !this.visible
        if (this.visible) {
          this.$refs.inputRef.focus() 
          // 这个时候inputRefs是不存在的，虽然visible为true，但vue中只是推入到异步更新队列中，此时立即访问该dom，实际还未更新，所以就访问不到该input
        } else {
          this.$refs.inputRef.blur() 
        }
      },
    }
  }
</script>
```



## 解决

将访问dom的操作放在Vue.nextTick方法传入的回调函数中:

```js
this.$nextTick(() => {
  this.$refs.inputRef.focus() // 这个时候dom已经更新了 可以访问到了
})
```



## 原因

将代码包裹在`this.$nextTick`中，是为了确保在执行后续操作（如访问或操作DOM元素）时，Vue.js已经完成了相关的DOM更新。具体原因如下：

1. **异步更新机制**：Vue.js采用了异步更新队列来优化性能。当数据发生变化时（如上述代码中的 `this.visible = true`），Vue不会立即更新DOM，而是将这些变化推入一个队列。等到当前事件循环结束，Vue会批量处理这些更新，一次性更新DOM，以减少不必要的DOM操作次数，提高渲染效率。
2. **确保DOM已更新**：由于DOM更新是在异步队列中进行的，因此，若直接在修改数据后立即尝试访问或操作相关DOM元素（如上述代码中获取`this.$refs.inputRef`对应的`<input>`元素），可能会因为DOM尚未更新而获取不到正确的元素或得到不准确的结果。
3. **$nextTick的作用**：`this.$nextTick`接受一个回调函数作为参数，该回调将在下一个DOM更新循环结束后立即执行。这意味着在回调函数内部，所有由先前数据变动触发的DOM更新都已经完成。此时，安全地访问和操作DOM元素，可以确保它们处于最新状态。

综上所述，将获取并操作`<input>`元素的代码包裹在`this.$nextTick`中，目的是等待Vue.js完成异步DOM更新，确保能够正确、准确地访问到已更新的DOM元素。



