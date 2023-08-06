# 前言

- 在 `onReachBottomDistance` 设置为 `0` 时，测试发现安卓机在滑动到底部时，无法触发 `onReachBottom` 钩子函数



# 解决

- 不设置为 `0` 即可