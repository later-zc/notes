# 前言

- 在看 `vue2` 源码时，安装依赖时，因为当时公司电脑 `node14+` 的版本不支持 `pageage.json` 中依赖所使用的 `workspace` 路径时（官网说最低要 `node16+`），从而导致安装失败，网上也有说用 `yarn` 安装的，或者 `pnpm`，但是当时本来选择用 `yarn` 去解决的，在安装依赖时，提示让我选择 `@vue/compiler-sfc` 依赖下载的版本，然后我去查阅 `github` 上，发现 `vue2.7` 以上都是 `workspace`，`vue2.6` 就没有这两项开发依赖，猜测可能 `2.7` 之后，将这两个依赖提取出来了，所以最终采用 `nvm-window` 去管理 `node` 版本

  ```json
  // pageage.json
  
  "dependencies": {
      "@vue/compiler-sfc": "workspace:*", // 问题出处
      "csstype": "^3.1.0"
  },
  ```

- 当时电脑上已有 `node14` 的版本，一开始安装 `nvm`，然后提示我是否接管已有的 `node` 版本，我选择接管，然后就导致，`node -v` 出问题（提示找不到该命令）。然后只能通过 `nvm` 下载` node`，安装 `npm` 时，安装一直失败



# 问题分析

- 在已经存在 `Node.js` 版本的情况下安装 `nvm`，并选择让 `nvm` 接管已有的 `Node.js` 版本时，可能会出现一些配置冲突，导致 `npm` 安装不正确
- 如果你在安装 `nvm` 时选择让其接管已有的 `Node.js` 版本，那么 `nvm` 将会尝试管理这个已有版本的 `Node.js`，包括管理其路径和相关的环境
- 然而，由于 `nvm` 的版本和已有的版本可能有一些不兼容的部分，可能会导致 `npm` 安装失败或无法正常使用



# 解决

```bash
# 1. 先卸载已安装的nodejs

# 2. nvm卸载重新安装

# 3. nvm install 14.18.1（node版本号）

# 4. 若遇到npm安装不上，更改下载源（之后，再卸载对应nvm中的node并重新安装，npm uninstall xxx）
registry=https://registry.npmjs.org/

# 5. 测试node跟npm
node -v
npm -v
```



# 相关资料

- ChatGPT（解决bug神器）：https://chat.openai.com/
- 查看node所有发布版本：https://nodejs.org/dist/index.json
- nvm-windows下载：https://github.com/coreybutler/nvm-windows/releases
- nvm-windows文档：https://github.com/coreybutler/nvm-windows#star-star-uninstall-any-pre-existing-node-installations-star-star



