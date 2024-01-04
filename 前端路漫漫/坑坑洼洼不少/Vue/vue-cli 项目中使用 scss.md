# 前言

- 项目环境：`Vue-cli` 创建的 `vue2` 项目


- 直接上文档：https://cli.vuejs.org/zh/guide/css.html#%E8%87%AA%E5%8A%A8%E5%8C%96%E5%AF%BC%E5%85%A5


- 这里把文档中的内容搬过来：

  1. 安装 `sass-loader`

     - 注意：根据 `node` 版本来安装对应的 `sass-loader` 的版本，不然有可能遇见不兼容的情况

  2. 自动化导入

     - 如果你想自动化导入文件 (用于颜色、变量、mixin……)，你可以使用 [style-resources-loader](https://github.com/yenshih/style-resources-loader)。这里有一个关于 Stylus 的在每个单文件组件和 Stylus 文件中导入 `./src/styles/imports.styl` 的例子：

       ```js
       // vue.config.js
       const path = require('path')
       
       module.exports = {
         chainWebpack: config => {
           const types = ['vue-modules', 'vue', 'normal-modules', 'normal']
           types.forEach(type => addStyleResource(config.module.rule('stylus').oneOf(type)))
         },
       }
       
       function addStyleResource (rule) {
         rule.use('style-resource')
           .loader('style-resources-loader')
           .options({
             patterns: [
               path.resolve(__dirname, './src/styles/imports.styl'),
             ],
           })
       }
       ```

     - 你也可以选择使用 [vue-cli-plugin-style-resources-loader](https://www.npmjs.com/package/vue-cli-plugin-style-resources-loader)。



# 实践

- `sass-loader` 根据对应的 `node` 版本安装

- 安装 `style-resources-loader`：

  ```bash
  npm i style-resources-loader -D
  ```

- 配置：

  ```js
  // vue.config.js
  
  function addStyleResource(rule) {
    rule
      .use('style-resource')
      .loader('style-resources-loader')
      .options({
        patterns: [path.resolve(__dirname, './src/styles/index.scss')],
      })
  }
  
  module.exports = {
    chainWebpack(config) {
      // const types = ['vue-modules', 'vue', 'normal-modules', 'normal']
      const types = ['vue']
      types.forEach(type => addStyleResource(config.module.rule('scss').oneOf(type)))
    },
  }
  ```

  

# 参考

- https://cli.vuejs.org/zh/guide/css.html#%E8%87%AA%E5%8A%A8%E5%8C%96%E5%AF%BC%E5%85%A5
- https://github.com/yenshih/style-resources-loader