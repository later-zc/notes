# Uniapp微信小程序：轻松实现自定义导航栏，提升用户体验

---

## 1、引言

当涉及微信小程序的界面设计时，我们常常会发现自带的导航栏功能相对简单，仅限于显示当前页面的标题。然而，在实际开发过程中，我们往往需要更多的自由度和个性化，以满足用户体验的需求。因此，自定义导航栏成为必然选择。通过自定义导航栏，我们可以赋予小程序更多的设计灵活性和交互性，不再受限于传统的简单导航功能，更能突显个性化的特色，提升用户的整体体验感受。在本文中，我们将深入探讨如何实现自定义导航栏，并解释其对于微信小程序开发的重要性和实际应用的价值。此文章以uniapp+pinia演示。

如下：微信自定义的导航栏比较简单

<img src="assets/image-20240528141451338.png" alt="image-20240528141451338" style="zoom:80%;" />

看看滴滴出行，选择在导航栏部署选择城市、扫一扫等工具。

<img src="assets/image-20240528141659927.png" alt="image-20240528141659927" style="zoom:80%;" />

## 2、实现步骤

#### 1、pinia创建deviceStore作为全局存储空间存储设备信息

state中保存三个数据：statusBarHeight、menuButtonInfo、navBarHeight。

#### 2、定义一个component当作自定义导航栏（我的叫做 HeadNav），在用到自定义导航栏的页面会使用这个组件

在这个组件里此处判断storage中是否有statusBarHeight、navBarHeight两个数据，没有则执行pinia中的方法deviceStore.getInfo()获取设备信息。

#### 3、获取到手机状态栏的高度，胶囊宽高计算出状态栏与胶囊按钮中的空隙，保存至缓存

```js
const statusBarHeight = (uni.getStorageSync('statusBarHeight')|| ref({}))//手机状态栏的高度，这个状态来就是手机顶部的电量啊，信号这些区域的高度，如果是刘海屏，它还会包含刘海屏的高度
const menuButtonInfo = ref({})//胶囊信息,就是微信小程序自带的那个有关闭，分享按钮的胶囊。
const navBarHeight =  (uni.getStorageSync('navBarHeight')|| ref({}))//状态栏与胶囊按钮中的空隙
//缓存中没有的话就执行下面方法：
statusBarHeight.value = uni.getSystemInfoSync().statusBarHeight
menuButtonInfo.value = uni.getMenuButtonBoundingClientRect()
//然后计算出navBarHeight
navBarHeight.value = (menuButtonInfo.value.bottom - statusBarHeight.value) + (menuButtonInfo.value.top - statusBarHeight.value) //状态栏与胶囊按钮中的空隙
```

#### 4、设置允许自定义状态栏，uniapp中在pages.json里面设置，微信小程序原生开发是在app.json（全局设置）或index.json（页面设置）

```json
"path" : "ChatDetail/ChatDetail",
    "style" :                                                                                 
    {
        "navigationBarTitleText": "",
        "enablePullDownRefresh": false,
        "navigationStyle": "custom"
    }        
}
```

#### 5、进入刚刚新建的component里进行状态栏占位，高度是手机状态栏的高度。

#### 6、创建真正的导航栏内容，并给一个初始高度防止黏在状态站位栏，这个高度是状态栏与胶囊按钮中的空隙。

```vue
<!-- 状态栏占位 -->
	<view :style="{height:deviceStore.statusBarHeight+'px'}"></view>
	<!-- 真正的导航栏内容 ，请按照自己的需求自行定义-->
	<view :style="{height:deviceStore.navBarHeight+'px'}" class="nav">
		<uni-icons type="back" size="30" class="nav-back" @click="goBackIndex"></uni-icons>
		<image :src="avatar"  class="nav-avatar" @click="gotoOthersInfo"></image>
		<text class="nav-name" @click="gotoOthersInfo">{{nickname}}</text> 
	 </view>
</view>
```



## 总结：

页面上方区域：状态栏 + 导航栏

- 状态栏高度：导航栏顶部到屏幕顶部的高度
- 导航栏高度：胶囊按钮顶部到状态栏底部的空隙 + 胶囊自身高度 + 胶囊按钮底部到页面顶部的空隙

封装成工具函数去获取：

```js
/**
 * 异步获取系统信息
 * @description 基于{@link https://uniapp.dcloud.net.cn/api/router.html#animation uni.getSystemInfo}的二次封装，支持同步获取异步获取
 * @param {object} [opts]
 * @param {Function} [opts.success] 接口调用成功的回调
 * @param {Function} [opts.fail] 接口调用失败的回调函数
 * @param {Function} [opts.complete] 接口调用结束的回调函数（调用成功、失败都会执行）
 * @returns {Promise<unknown>}
 * @example
 * // 异步方式：
 * // callback
 * getSystemInfo({ success() {} })
 * // promise
 * getSystemInfo().then(res => {})
 * // 同步方式: async await
 * const { statusBarHeight } = await getSystemInfo()
 */
export function getSystemInfo(opts) {
  return new Promise((resolve, _) => {
    return uni.getSystemInfo(
      Object.assign(
        {
          success: (res) => resolve(res),
          fail: (err) => resolve(err),
        },
        opts ?? {}
      )
    )
  })
}

/**
 * 获取导航栏高度（页面上方区域：状态栏 + 导航栏）
 * @returns {Promise<number>}
 * @example
 * // 异步方式：Promise
 * getNavBarHeight().then(res => {})
 * // 同步方式: async await
 * const { statusBarHeight } = await getSystemInfo()
 */
export async function getNavBarHeight() {
  // 获取状态栏高度
  const { statusBarHeight } = await getSystemInfo()
  // 获取菜单按钮（右上角胶囊按钮）的布局位置信息。坐标信息以屏幕左上角为原点。单位px
  const menuBtnInfo = uni.getMenuButtonBoundingClientRect()
  // 自定义导航栏高度 = 胶囊按钮自身的高度 + 胶囊上边界到状态栏底部的距离 + 胶囊下边界到页面顶部的距离
  // 由于拿不到页面顶部在屏幕中的布局信息，我们就不能通过页面顶部的top - menuBtnInfo.bottom的方式计算出距离
  // 但是，根据社区及网上资料可以看出，胶囊下边界到页面顶部的距离与胶囊上边界到状态栏底部的距离应该是一致的
  // 所以得出：胶囊下边界到页面顶部的距离 = 胶囊上边界到状态栏底部的距离
  return menuBtnInfo.height + (menuBtnInfo.top - statusBarHeight) * 2
  // 还有一种计算方式也是可以的：
  // 胶囊按钮下边界到状态栏底部的距离 + 胶囊按钮上边界到状态栏底部的距离
  // 这两种计算方式都是基于：胶囊下边界到页面顶部的距离 = 胶囊上边界到状态栏底部的距离
  // return (menuBtnInfo.bottom - statusBarHeight) + (menuBtnInfo.top - statusBarHeight)
}
```

> 注意：
>
> - iOS中防止橡皮筋滑动效果拖动导航栏等区域被拖拽，可以才用fixed定位固定在屏幕顶部。
> - 为了适配各类屏宽的设备，导航栏中的元素大小的css单位不能使用类似rpx等相对页面宽度的单位，需要根据设计稿中的尺寸比对实际屏幕的尺寸，根据比例去换算应该设置的css尺寸，单位推荐px等绝对单位。



## 参考文章

- [Uniapp微信小程序：轻松实现自定义导航栏，提升用户体验](https://developers.weixin.qq.com/community/develop/article/doc/0006ca48ce4230bbaff03a38566813?highline=%E8%87%AA%E5%AE%9A%E4%B9%89%E5%AF%BC%E8%88%AA%E6%A0%8F)





