# 前言

- `uni-app` 生态专用的 `UI` 框架 `uView UI` 组件库的 `u-button` 组件不支持 `.stop` 事件修饰符

  ```vue
  <view @click="onClick">
  	<u-button
      loadingText="结束中"
    	hover-stop-propagation
    	:loading="isLoading"
    	@click.stop="handleEndOrder"
  	>结束订单</u-button>
  </view>
  ```

- 上述代码中，`u-button` 组件的点击事件，加了 `.stop` 事件修饰符来阻止事件冒泡，但实际测试中，并没有生效，推测 `uView` 没有支持到位

# 解决

- 采用 `uniapp` 的 `button` 组件即可，这里选择放弃使用 `u-button` 组件

  ```vue
  <view @click="onClick">
  	<button
      loadingText="结束中"
      hover-stop-propagation
      :loading="isLoading"
      @click.stop="handleEndOrder"
    >订单</button>
  </view>
  ```



