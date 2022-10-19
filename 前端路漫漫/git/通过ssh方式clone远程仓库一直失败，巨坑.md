```js
    $ git clone git@gitee.com:later-sensen/git-test-repository.git
    
	Cloning into 'git-test-repository'...
    The authenticity of host 'gitee.com (212.64.63.190)' can't be established.
    ED25519 key fingerprint is SHA256:+ULzij2u99B9eWYFTw1Q4ErYG/aepHLbu96PAUCoV88.
    This key is not known by any other names
    Are you sure you want to continue connecting (yes/no/[fingerprint])? // 这里一定要输入yes！！！否则就会出现如下情况
    Host key verification failed.
    fatal: Could not read from remote repository.

    Please make sure you have the correct access rights
    and the repository exists.
```

- `git ssh`方式` clone `远程云服务器的仓库，提示如下

```js
    主机“gitee.com(212.64.63.190)”的真实性无法确定。  

    ED25519密钥指纹为SHA256:+ULzij2u99B9eWYFTw1Q4ErYG/aepHLbu96PAUCoV88。  

    此键不为任何其他名称所知  

    您确定要继续连接(yes/no/[fingerprint])? // 这里一定要输入yes！直接打的回车所以一直提示权限被拒绝！弄了好多遍ssh！太坑了！
```

- 参考
  - `https://blog.csdn.net/yunweifun/article/details/119189790`
  - `https://blog.csdn.net/weixin_38318244/article/details/115353920`