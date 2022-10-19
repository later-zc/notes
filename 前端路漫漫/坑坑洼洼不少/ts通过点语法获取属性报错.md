# 一.  缘由

---

在`TypeScript`中如果按`JS`的` . `方式去获取对象属性, 有些情况下会提示`  Property 'xxx' does not exist on type 'Object' `的错误

# 二.  解决

---

## 1. 将类型注解设置为`any`

```typescript
const scr2_dataArr: any = this.listDataArr.splice(0, this.onceNum)

for (let i=1; i<=4; i++) {
    this[`s2_son${i}_name`].text = scr2_dataArr[i-1].nick_name
}

...
```



## 2. 通过方括号方式获取对象属性

```typescript
const scr1_dataArr = this.listDataArr.splice(0, this.onceNum)

for (let i=1; i<=4; i++) {
	this[`s1_son${i}_name`].text = scr1_dataArr[i-1]['nick_name']
}

...
```



相关引用：`https://blog.csdn.net/weixin_44068394/article/details/108779941?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0.no_search_`



​	