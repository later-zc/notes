

# 一. 前言

在本地分支和远程分支不同名时，进行 `push` 操作，报错如下：

```bash
23634@laterzcwindow MINGW64 ~/Desktop/learn/learn_taro/hyJPApp (master)
$ git push -u origin main
error: src refspec main does not match any
error: failed to push some refs to 'github.com:later-zc/hyJPApp.git'
```





# 二. 解决方案

统一远程和本地的仓库名称：

 把本地的 `master` 仓库名称修改为远端的 `main`

```bash
# 重命名命令： 
git branch -m 旧名字 新名字

# 示例：
git branch -m master main
```

