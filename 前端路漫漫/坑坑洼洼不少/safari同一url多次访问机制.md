# **一.  缘由**

---

kakaotalk大转盘项目中，有一项需求是需要做到防止用户复制项目URL直接进入首页，必须通过指定banner入口才能进入。

# **二.  结论**

---

safari浏览器中同一url访问时，直接显示上次访问停留的页面。

当时测试发现项目在safari浏览器中是能直接进入项目的，而不是通过指定banner入口进入，

safari浏览器机制中，对某一个URL地址 a 访问时，

中间也许跳转到其他页面（踩坑这次是跳的同一域名下的其他页面），

最终停留在某个页面，且手机后台浏览器未关闭的情况下，

下次再访问 a 链接时，safari会直接显示上次最终停留在的页面，

如果将浏览器退出（未打开的状态）， 则不会出现这种情况（直接显示上次停留的页面）。





