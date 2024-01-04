# 前言

- 项目环境：`Vue-cli` 创建的 `Vue2` 项目

- 如果你使用的是 `Vue CLI` 创建的项目，那么 `Vue CLI` 默认已经配置好了 `Vue Loader` 和 `Babel`。但是，如果你需要自定义 `Babel` 的配置以支持更高级的 `ECMAScript` 语法，你可以按照以下步骤进行操作：

  1. **安装依赖**： 确保项目中已经安装了 `@vue/cli-plugin-babel`，通常在创建 `Vue` 项目时会默认安装。如果没有安装，可以通过以下命令进行安装：

     ```bash
     vue add @vue/cli-plugin-babel
     ```

  2. **创建 Babel 配置文件**： 在项目根目录下创建一个名为 `babel.config.js` 的文件（如果之前没有创建过的话）。

  3. **配置 Babel**： 在 `babel.config.js` 中配置 `@babel/preset-env` 来启用特定的 `ECMAScript` 特性。例如，要启用 `Nullish` 合并操作符，可以像这样配置：

     1. 如果在项目中没有安装 `@babel/preset-env`。使用 `npm` 或 `yarn` 安装该模块：

        ```bash
        npm install @babel/preset-env --save-dev
        # 或
        yarn add @babel/preset-env --dev
        ```

        - 确保模块安装完整，没有报错。

     2. 检查项目根目录下的 `babel.config.js` 文件，并确保配置如下：

        ```js
        // babel.config.js
        
        module.exports = {
          presets: [
            ['@babel/preset-env', { targets: 'defaults' }]
            // 可以根据需要添加其他选项
          ]
        };
        ```

        - 你可以根据自己的需求修改 `targets` 选项，以确保你所需的浏览器或运行环境支持的特性。

  4. **重启服务**： 重新启动 `Vue CLI` 的开发服务器，让配置生效：

     ```bash
     npm run serve
     ```

- 这样做应该会使 `Vue CLI` 使用你指定的 `Babel` 配置来转译 `js`代码，允许你在 `Vue` 单文件组件和其他 `js` 文件中使用更高级的 `ECMAScript` 语法特性。记得在修改配置后重新启动开发服务器。



# 实践

- 根据提示，我们修改 `targets` 选项。根据所需的浏览器或运行环境决定

  ```js
  targets: '> 1%, last 2 versions'
  ```



# 参考

- https://babeljs.io/docs/options#targets