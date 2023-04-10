> 众所周知, 函数中的 this 对象默认指向调用函数的对象。
>
> 在浏览器执行环境中, requestAnimationFrame 是 window 对象的方法, 所以该函数中的 this 对象指向的是调用函数的对象。
>
> 所以它的第一个参数中的 this 指向的就是 window 对象，所以类组件中的场景使用它时，切记需要注意。
>
> setTimeout, setInterval 中的this也是指向 window 对象的。 
> 
>
> [引用]: https://blog.csdn.net/qingyafan/article/details/52335753