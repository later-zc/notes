# 前言

---

- 在做某个项目时，在浏览器中访问某个页面，通过 `live server` 启动本地服务来实时更新查看页面，但是有时候想在手机上查看效果





# 解决

---

- 以下内容来自 `live server` 官方文档中

- https://github.com/ritwickdey/vscode-live-server/blob/HEAD/docs/settings.md

- 首先，确保您的 `PC` 和手机通过同一网络连接

  - **Windows**：打开 `CMD` 并进入 `ipconfig`
  - **Linux/macOS**：打开 `terminal` 并输入 `ifconfig`

- 并记下 `IPv4 Address`（可能看起来像 `192.168.xx.xx`）。这是您 `PC` 的 `IP` 地址

- 使用端口号 `xxxx` 输入浏览器 `URL` 栏的地址

  ```
  http://<IP Address> : <Port>
  ```

- 例如，如果您的服务器在 `PC` 上运行在 **`http:// 127.0.0.1:3500` 那么端口号是 `3500`**