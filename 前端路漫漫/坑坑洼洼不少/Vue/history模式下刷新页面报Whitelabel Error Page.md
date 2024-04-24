## 前言

vue cli创建的项目，使用的路由是history模式，刷新页面时，出现空白页并显示: Whitelabel Error Page，有的页面没有这个问题,有的就有，由于这次是新建页面后出现了问题，一般这种会考虑是：

1. 路由配置中该路径没有匹配的路由组件。
2. 检查完发现是没问题的，那是什么问题呢？



## 原因

是因为跳转的url路径前面的部分 与 devServer.proxy中配置的代理转发路径前面的部分 有相同的内容，就会导致直接当成请求发送到代理服务了。

**路径中只要代理地址前面字母开头与路径地址前面字母开头有重复就会有问题，不单单是一定一模一样才会出错**

例如：

- url地址为`localhost:8080/activit/setMgmt` 

- 代理配置如下：

  ```js
  proxy:{
    ...,
      '/act':{
        target:url,
          ws:true,
            pathRewrite:{
              '^/act':'/act'
            }
      },
  }
  ```

- 路由中的开头字母 act 和代理中的act产生冲突了 ,导致页面刷新直接去请求后端地址了，所以配置时不止不要名称一样,尽量不要路由地址开头字母也一样



## 参考文章

https://blog.csdn.net/weixin_44741820/article/details/135925390?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-135925390-blog-126594807.235%5Ev43%5Epc_blog_bottom_relevance_base3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-135925390-blog-126594807.235%5Ev43%5Epc_blog_bottom_relevance_base3&utm_relevant_index=2

https://blog.csdn.net/weixin_41923266/article/details/126594807

刷新 Whitelabel Error Page