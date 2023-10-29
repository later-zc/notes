# 前言

---

- 背景：基于 `uniapp` 跨平台框架编译到小程序
- 需求：基于业务封装业务公共组件，该组件接受外部传入的配置表，根据配置表中的字段及工具函数进行数据处理及绑定
- 问题：`uniapp` 组件的 `props` 不支持传入函数类型的值，也不能通过单例模式走一个全局使用，如果同时存在多个相关组件需要传入函数类型的 `props` 的值，就容易会出现管理混乱问题



# 原因

- 原因是受限于 `uniapp` 框架：`uniapp` 中父组件向子组件传递 `props` 时，如果 `props` 带有函数类型的值，将会被 `uniapp` 内部给过滤掉了，貌似是深度克隆的时候不支持拷贝函数



# 解决

- 既然不能做到传入组件内部处理，就先处理完数据再进行传入

- 封装一个工具函数及数据字典提前处理的工具函数（不修过原字段的值，只是新增相关的字段）

  ```js
  // utils/handler/js
  
  export default {
    // 为什么不是直接将配置中的格式化函数通过props的形式传入到自定义组件呢？
    // 原因是受限于uniapp框架：uniapp中父组件向子组件传递prop时，如果prop是函数类型，将无法传递
    // 被uniapp内部给过滤掉了，深度克隆的时候无法拷贝函数
    // https://ask.dcloud.net.cn/question/70659
    // https://devpress.csdn.net/viewdesign/643769bc986c660f3cf937e0.html?spm=1001.2101.3001.6650.17&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Eactivity-17-107089150-blog-118539610.235%5Ev38%5Epc_relevant_anti_vip&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Eactivity-17-107089150-blog-118539610.235%5Ev38%5Epc_relevant_anti_vip&utm_relevant_index=18
    
    // 鉴于这个原因，也不想事件通信传来传去，所以采用将格式化函数提前处理完，再传入子组件的方案，所以格式化函数也就被抽取出来作为一个公共的工具函数
    // 扩展：小程序中也有类似问题，但小程序自定义组件中，是可以在组件实例中获取到传入的函数，只不过不能在wxml中直接使用
    // https://developers.weixin.qq.com/community/develop/doc/000a629b75c918f9e537c988b57000
    
    /**
     * @description (非纯函数)根据config配置项中所提供的formatter方法，在数据源中生成一个相应的字段格式化属性(field+'OfFormatted')，若配置项中的子项有dicData字段传入，将在数据源中生成一个相应字段的字典化属性(field+'OfDicVal')
     * @param {object} origin 数据源
     * @param {Array} config 配置项
     * @example
     * let origin = {a: 1, b: 'A'}
     * let config = [
     *  { field:'a', formatter:(origin, val) => val==1?true:false },
     *  { field: 'b', dicData: {A: 'sss'}}
     * ]
     * formatter(origin, config) // {a:1, b:'A',aOfFormatted: true, bOfDicVal: 'sss'}
     * origin // {a:1, b:'A',aOfFormatted: true, bOfDicVal: 'sss'}
     */
    formatter: (origin, config) => {
      config.forEach((i) => {
        if (i.formatter) {
          origin[i.field + 'OfFormatted'] = i.formatter(origin, origin[i.field])
        }
        if (i.dicData) {
          origin[i.field + 'OfDicVal'] = i.dicData[origin[i.field]]
        }
      })
      return origin
    },
  }
  ```

- 采用全局挂载

  ```js
  // main.js
  import handler from './utils/handler'
  Vue.prototype.$handler = handler
  ```

- 使用案例

  ```vue
  <record-item
    :dataOfItem="$handler.formatter(item, configListOfSubDesc)"
    :configListOfSubDesc="configListOfSubDesc" />
  ```

  ```js
  // configListSubDesc 配置列表
  import { formatDate } from '@/utils/helper'
  import { EnumRechargeType } from '@/utils/enum'
  
  export const configListOfSubDesc = [
    { field: 'chargeType', label: '充值方式', dicData: EnumRechargeType },
    { field: 'time', label: '充值时间', formatter: (_, val) => formatDate(val) },
    { field: 'money', label: '充值金额', formatter: (_, val) => val + '元' },
  ]