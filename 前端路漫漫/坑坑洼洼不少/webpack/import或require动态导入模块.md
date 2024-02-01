## webpack中使用import或require动态导入模块

直接上代码，下面是一个动态菜单映射组件的代码	：

```js
const endFill = /\/index$/.test(i.path) ? '' : '/index'
const fullPath = `@/views${parentMenu + i.path}${endFill}.vue`

// 方式一：require
// i.component = (resolve) => require([fullPath], resolve) // 不行
i.component = (resolve) => require([`@/views${parentMenu + i.path}${endFill}.vue`], resolve) // 可行

// 方式二：import
// i.component = () => import(fullPath) // 不行
i.component = () => import(`@/views${parentMenu + i.path}${endFill}.vue`) // 可行
```

为什么直接使用fullPath的那种就不行呢？

- 在Webpack中使用动态导入（Dynamic Import）语法时，你需要确保动态导入的路径是静态字符串（static string）。在案例提供的代码中，`fullPath` 是一个变量，而不是静态字符串。
- Webpack在静态分析阶段会检查动态导入的路径，以确定它是否是一个静态字符串。**如果路径是一个变量，Webpack无法在构建时正确地分析和处理这个导入，导致无法正确地构建出相应的代码块**。
- 确保**使用动态导入时路径是静态字符串**，就像示例中的可行代码一样，使用模板字符串拼接的方式。这样Webpack能够正确地分析模块依赖关系，使得按需加载和代码分割能够正常工作。

接下来再看看webpack文档中提到的：

- 完全动态的语句（如 `import(foo)`），因为 webpack 至少需要一些文件的路径信息，而 `foo` 可能是系统或项目中任何文件的任何路径，因此`foo` *将会解析失败。*`import()` **必须至少包含模块位于何处的路径信息**，所以打包应当限制在一个指定目录或一组文件中。
- *调用* `import()` *时，包含在其中的动态表达式 request，会潜在的请求的每个模块。例如，*`import(`./locale/${language}.json`)` *会导致* `./locale` *目录下的每个* `.json` *文件，都被打包到新的 chunk 中。在运行时，当计算出变量* `language` *时，任何文件（如* `english.json` *或* `german.json`*）都可能会被用到。 Using the* `webpackInclude` *and* `webpackExclude` *options allows us to add regex patterns that reduce the files that webpack will bundle for this import.*
- https://v4.webpack.docschina.org/api/module-methods/#import-

