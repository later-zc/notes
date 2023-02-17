- 从`Git`远程仓库克隆`https`的方式

  - 失败: 无法访问'`https://github.com/coderwhy/hy-react-web-music.git/`': `OpenSSL SSL_connect`:连接被重置到`github.com:443 `

  ```js
      git clone https://github.com/coderwhy/hy-react-web-music.git
  
  	// 报错：fatal: unable to access 'https://github.com/coderwhy/hy-react-web-music.git/': OpenSSL SSL_connect: Connection was reset in connection to github.com:443
  ```

- 解决办法：

  - 打开`vpn`，配置代理及端口号
    - 具体配置`http`还是`https`，个人测试吧，我只配了`http`

  ```js
      git config --global http.proxy 127.0.0.1:4780 // 端口号4780需和vpn的http(s)端口保持一致
      git config --global https.proxy 127.0.0.1:4780
  ```

  - 取消配置

  ```js
      git config --global --unset http.proxy
      git config --global --unset https.proxy
  ```

  - 一般直接在配置文件中修改即可

  ```js
  	// C:\Users\23634\.gitconfig
  	[user]
  		name = later_zc
  		email = 2363442097@qq.com
  	// 一些别的...
  	[http]
  		proxy = http://127.0.0.1:4780 
  ```

- 参考：

  - `https://wxler.github.io/2021/02/28/151203/#1-openssl-ssl_connect-connection-was-reset-in-connection-to-githubcom443`
  - `https://stackoverflow.com/questions/49345357/fatal-unable-to-access-https-github-com-xxx-openssl-ssl-connect-ssl-error`

  