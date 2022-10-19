# 一. 属性

---

```html
<video
id="video" 
src="video.mp4" /*视频地址*/
controls = "true" 
poster="images.jpg" /*视频封面*/
preload="auto" /*属性规定在页面加载后载入视频*/
webkit-playsinline="true" /*这个属性是ios 10中设置可以让视频在小窗内播放，也就是不是全屏播放*/  
playsinline="true"  /*IOS微信浏览器支持小窗内播放*/ 
x-webkit-airplay="allow" 
x5-video-player-type="h5"  /*启用H5播放器,是wechat安卓版特性*/
x5-video-player-fullscreen="true" /*全屏设置，设置为 true 是防止横屏*/
x5-video-orientation="portraint" //播放器支付的方向， landscape横屏，portraint竖屏，默认值为竖屏
style="object-fit:fill">/*默认，不保证保持原有的比例，内容拉伸填充整个内容容器*/
</video>

controls: 加上这个属性，Gecko(排版引擎)会提供用户控制，允许用户控制视频的播放，
包括音量, 跨帧, 暂停/恢复播放.

poster: 属性规定视频下载时显示的图像，或者在用户点击播放按钮前显示的图像。
如果未设置该属性，则使用视频的第一帧来代替。

webkit-playsinline和playsinline: 视频播放时局域播放，不脱离文档流 。
但是这个属性比较特别， 需要嵌入网页的APP比如WeChat中UIwebview的
allowsInlineMediaPlayback = YES webview.allowsInlineMediaPlayback = YES，
才能生效。换句话说，如果APP不设置，你页面中加了这标签也无效，
这也就是为什么安卓手机WeChat播放视频总是全屏，因为APP不支持playsinline，
而ISO的WeChat却支持。这里就要补充下，如果是想做全屏直播或者全屏H5体验的用户，
IOS需要设置删除 webkit-playsinline 标签，因为你设置 false 是不支持的，
安卓则不需要，因为默认全屏。但这时候全屏是有播放控件的，无论你有没有设置control, 
做直播的可能用得着播放控件，但是全屏H5是不需要的，那么去除全屏播放时候的控件，
需要以下设置：同层播放 

x5-video-player-type: 启用同层H5播放器，就是在视频全屏的时候，
div可以呈现在视频层上，也是WeChat安卓版特有的属性。同层播放别名也叫做沉浸式播放，
播放的时候看似全屏，但是已经除去了control和微信的导航栏，只留下"X"和"<"两键。
目前的同层播放器只在Android（包括微信）上生效，暂时不支持iOS。
至于为什么同层播放只对安卓开放，是因为安卓不能像ISO一样局域播放，
默认的全屏会使得一些界面操作被阻拦，如果是全屏H5还好，但是做直播的话，
诸如弹幕那样的功能就无法实现了，所以这时候同层播放的概念就解决了这个问题。
不过在测试的过程中发现，不同版本的IOS和安卓效果略有不同

x-webkit-airplay="allow" : 这个属性应该是使此视频支持ios的AirPlay功能。
使用AirPlay可以直接从使用iOS的设备上的不同位置播放视频、音乐还有照片文件，
也就是说通过AirPlay功能可以实现影音文件的无线播放，
当然前提是播放的终端设备也要支持相应的功能

x5-video-orientation: 声明播放器支持的方向，可选值landscape 横屏, 
portraint竖屏。默认值portraint。无论是直播还是全屏H5一般都是竖屏播放，
但是这个属性需要x5-video-player-type开启H5模式

x5­-video­-player­-fullscreen:全屏设置。它又两个属性值，ture和false，
true支持全屏播放，false不支持全屏播放。其实，IOS 微信浏览器是Chrome的内核，
相关的属性都支持，也是为什么X5同层播放不支持的原因。安卓微信浏览器是X5内核，
一些属性标签比如playsinline就不支持，所以始终全屏
```

# 二. 全屏处理

---

```html
<!-- ios -->
ios加playsinline属性，之前只带webkit前缀的在ios10以后，会吊起系统自带播放器，
两个属性都加上基本ios端都可以保证内敛到浏览器webview里面了。
如果仍有个别版本的ios会吊起播放器，还可以引用一个库iphone-inline-video（具体用法
很简单看它github，这里不介绍了，只需加js一句话，css加点），
github地址加上playsinline webkit-playsinline这两个属性和这个库基本可以保证
ios端没有问题了（不过亲测，只加这两个属性不引入库好像也是ok的，至
今没有在ios端微信没有出现问题，如果你要兼容uc或者qq的浏览器建议带上这个库）

<!-- android -->
x5-video-player-type="h5"属性，腾讯x5内核系的android微信和手Q内置浏览器
用的浏览器webview的内核，使用这个属性在微信中视频会有不同的表现，会呈现全屏状态，
貌似播放控件剥去了，至少加了这个属性后视频上层可以有其他dom元素出现了
（非腾讯白名单机制的一种处理措施）。
//code from http://caibaojian.com/h5-video.html
<video id="video" src="xx.mp4" playsinline webkit-playsinline></video>
```

# 三. 自动播放

---

```js
android始终不能自动播放，不多说。值得一提的是经测现在ios10后版本的safari和微信
都不让视频自动播放了（顺带音频也不能自动播放了），但微信提供了一个事件
WeixinJSBridgeReady，在微信嵌入webview全局的这个事件触发后，视频仍可以自动播放，
这个应该是现在在ios端微信的视频自动播放的比较靠谱的方式，其他如手q或者其他浏览器，
建议就引导用户出发触屏的行为操作出发比较好。
// code from http://caibaojian.com/h5-video.html
document.addEventListener("WeixinJSBridgeReady", function (){ 
 video.play()
 video.pause()
}, false)
```

# 四. 播放控制

---

```js
对于video或者audio等媒体元素，有一些方法，常用的有play(),pause();也有一些事件，
如'loadstart','canplay','canplaythrough','ended','timeupdate'.....等等。
在移动端有一些坑需要注意，不要轻易使用媒体元素的除'ended','timeupdate'以外
event事件，在不同的机子上可能有不同的情况产生，例如：ios下监听'canplay'和
'canplaythrough'（是否已缓冲了足够的数据可以流畅播放）,当加载时是不会触发的，
即使preload="auto"也没用，但在pc的chrome调试器下和android下，是会在加载阶段
就触发。ios需要播放后才会触发。总之就是现在的视频标准还不尽完善，有很多坑要注意，
要使用前最好自己亲测一遍
就是当第一次播放视频的时候ios端，如果网络慢，视频从开始播到能展现画面会有短暂的黑屏
（处理视频源数据的时间），为了避免这个黑屏，可以在视频上加个div浮层（可以一个假的
视频第一帧），然后用timeupdate方法监听，视屏播放及有画面的时候再移除浮层·
// code from http://caibaojian.com/h5-video.html
video.addEventListener('timeupdate', function () {
    // 当视频的currentTime大于0.1时表示黑屏时间已过，已有视频画面，可以移除浮层（.pagestart的div元素）
    if ( !video.isPlayed && this.currentTime>0.1 ) {
        $('.pagestart').fadeOut(500)
        video.isPlayed = !0
    }
})
```

# 五. 隐藏播放控件

---

```html
据说腾讯的android团队的x5内核团队放开了视频播放的限制，视频不一定调用它们那个备受
诟病的视频播放器了，x5-video-player-type="h5"属性这个属性好像就有点那个意思，
虽然体验还是有点...（导航栏也会清理）但至少播放器控件没有了，上层可以浮div或者
其他元素了，这个还是值得一提。还有一点值得说的是，带播放器控件的隐藏
// code from http://caibaojian.com/h5-video.html
<div class="videobox" ontouchmove="return false;">
	<video id="mainvideo" src="test.mp4" x5-video-player-type="h5" playsinline webkit-playsinline></video>
</div>
```

# 六. 去掉 video 的 controls 中的下载按钮

---

```css
video::-internal-media-controls-download-button {
	display:none;
}

video::-webkit-media-controls-enclosure {
	overflow:hidden;
}

video::-webkit-media-controls-panel {
	width: calc(100% + 30px); 
}
```

# 七. 参考文章

---

> [html5--移动端视频video的android兼容，去除播放控件、全屏等](https://segmentfault.com/a/1190000006857675)
>
> [MDN-Video](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/video)
>
> [视频H5のVideo标签在微信里的坑和技巧](https://aotu.io/notes/2017/01/11/mobile-video/)
>
> [移动端HTML5视频播放优化实践](http://www.xuanfengge.com/html5-video-play.html)
>
> [微信端视频播放问题](http://www.jianshu.com/p/9dec20414bb8)[HTML5](http://caibaojian.com/t/html5),[JavaScript](http://caibaojian.com/t/javascript),[video](http://caibaojian.com/t/video) 推荐文章
>
> [使用HTML5 Canvas arc()绘制圆形/圆环](http://caibaojian.com/html5-canvas-arc.html)
>
> canvas怎么画圆？前段时间的一个项目做到这个玩意，研究了一下绘制圆环的方法。 最终使用HTML5的画布标签来做。arc()是HTML5 canvas的一个API函数， 作用是“创建弧/曲线（用于创建圆形或部分圆）”。 本文先讲解如何绘制圆形以及一些应用。
>
> [html5 shiv让IE6-IE8支持HTML5标签](http://caibaojian.com/html5shiv.html)
>
> 越来越多的站点开始使用 HTML5 标签。但是目前的情况是还有很多人在使用IE6，IE7，IE8。 为了让所有网站浏览者都能正常的访问网站，解决方案就有下面两个：  为网站创建多套模板， 通过程序对User-Agent的判断给不同的浏览器用户显示 ...
>
> 必看！移动前端开发优化指南
>
> 移动前端开发由于是在手机上使用，需要考虑到网络信号较差和为用户的流量着想， 性能和加载速度就是前端开发人员需要认真考虑的，PC的优化手段同样使用在移动端上， 下面一起看看腾讯ISUX这篇文章是如何从CSS、JavaScript、http请求和硬件渲染方面 做出优化的。
>
> canvas绘制旋转的圆环百分比进度条
>
> 如何制作圆环百分比进度条，本文通过使用canvas方法制作一个运动的圆环和圆饼来实现 常用的进度条。
>
> [video播放器全屏兼容方案](http://caibaojian.com/video-screenfull.html)
>
> 在Github上有两个video的插件维护的比较积极，在Github里搜索video， 排序选择最高star的，关于video
>
> 来源：[前端开发博客](http://caibaojian.com/h5-video.html)
>
> 原文链接：http://caibaojian.com/h5-video.html

