## 一. vue-cli 创建的项目

- 基于 vue cli 的没找到符合要求的全自动按需导入组件的插件，有以下几种方案（下面示例为Vue2，Vue3同理只不过语法不同而已）：

  1. 手动按需导入，优点：通用，按需导入。缺点：每次使用都要手写导入。

     ```js
     // xxx.vue
     
     // 1.导入组件
     import ComponentA from 'components/xxx'
     // 2.注册组件...
     ```

  2. 全局导入，优点：通用，不需要手写导入。缺点：初始加载资源包体积增大。

     ```js
     // main.js
     
     // 1.在 main.js 中直接使用 Vue.component 注册全局组件（不推荐，当需要注册的全局组件很多时，要写多行导入与注册，不够优雅）
     
     // 2.使用基于 webpack 的 Vue-cli3+ 创建的项目
     // 那么就可以使用 require.context 只全局注册这些非常通用的基础组件。这里有一份可以让你在应用入口文件 (比如 src/main.js) 中全局导入基础组件的示例代码：
     
     import Vue from 'vue'
     import upperFirst from 'lodash/upperFirst'
     import camelCase from 'lodash/camelCase'
     
     const requireComponent = require.context(
       // 其组件目录的相对路径
       './components',
       // 是否查询其子目录
       false,
       // 匹配基础组件文件名的正则表达式
       /Base[A-Z]\w+\.(vue|js)$/
     )
     
     requireComponent.keys().forEach(fileName => {
       // 获取组件配置
       const componentConfig = requireComponent(fileName)
     
       // 获取组件的 PascalCase 命名
       const componentName = upperFirst(
         camelCase(
           // 获取和目录深度无关的文件名
           fileName
             .split('/')
             .pop()
             .replace(/\.\w+$/, '')
         )
       )
     
       // 全局注册组件
       Vue.component(
         componentName,
         // 如果这个组件选项是通过 `export default` 导出的，
         // 那么就会优先使用 `.default`，
         // 否则回退到使用模块的根。
         componentConfig.default || componentConfig
       )
     })
     ```

  3. **`babel-plugin-import`** 或 `babel-plugin-component` 插件，具体使用姿势，用到了再查文档，这里也不推荐使用这两种插件，后者component是import插件的扩展，专门为ant-design vue这样的UI库设计的

  >推荐使用 require.context 只全局注册非常通用的基础组件，其余没有太通用的组件建议手动导入，全局基础组件建议全放在src/components中，或者以Basic 或 App 开头命名，其余业务组件建议放到视图组件下的component中。

## 二. vite 创建的项目

无脑上 unplugin-vue-components，下面内容来自G老师：

`unplugin-vue-components` 是一个专为 Vite、Vue3 项目设计的插件，用于实现 Vue 组件的按需导入。它的设计目标是提供一种简单而灵活的方式来处理组件的自动导入，类似于 Vue CLI 中的 `babel-plugin-component`。

以下是使用 `unplugin-vue-components` 实现全自动按需导入自定义组件和第三方 UI 组件的一般步骤：

1. **安装插件：**

   ```bash
   npm i unplugin-vue-components
   ```

2. **配置 Vite 插件：**

   ```js
   // vite.config.js
   import { defineConfig } from 'vite';
   import Components from 'unplugin-vue-components/vite';
   
   export default defineConfig({
     plugins: [
       Components({
         // 指定扫描组件的目录，可以是绝对路径或相对路径
         dirs: ['src/components'],
         // 自定义组件库的别名和路径
         dts: 'src/components.d.ts',
         include: [/\.vue$/, /\.vue\?vue/, /\.md$/], // 包含的文件类型
         exclude: [/node_modules/], // 排除的目录
         extensions: ['vue', 'md'], // 支持的文件扩展名
         dtsOnly: false, // 仅生成 .d.ts 文件
       }),
     ],
   });
   ```

3. **在组件中使用：**

   在你的 Vue 组件中，你无需手动导入组件，`unplugin-vue-components` 会自动帮你处理。

   ```vue
   <!-- YourComponent.vue -->
   
   <template>
     <div>
       <MyCustomButton />
       <AnotherCustomComponent />
       <VantButton /> <!-- 也可以自动导入第三方组件 -->
     </div>
   </template>
   
   <script>
   // 无需手动导入组件，unplugin-vue-components 会处理
   export default {
     // ...
   };
   </script>
   ```

4. **启动 Vite：**

   - 启动 Vite 项目，`unplugin-vue-components` 会自动扫描你指定的目录，生成按需导入的代码。
   - 这个插件的配置非常简单，大多数情况下你只需要指定扫描组件的目录即可。同时，它支持一些其他配置项，以适应不同的项目需求。详细的配置信息可以参考 unplugin-vue-components 的文档。


