# git强制push

不知道做了什么非人的改动，今天push Github的时候，出现了一句错误提示：

```bash
error: failed to push some refs to 'https://github.com/......'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
```

想到也没做什么，就直接强制push了。

```bash
$ git push -u origin master -f
```

总感觉哪里不太对，合作项目的时候，还是建议不要用这种方法的好。
顺便搜索了下别的解决方案：

**1\. push前先将远程repository修改pull下来：**

```bash
$ git pull origin master
$ git push -u origin master
```

**2\. 若不想merge远程和本地修改，可以先创建新的分支：**

```bash
$ git branch [name]
$ git push -u origin [name]
```

---
via: https://blog.csdn.net/Jesounao/article/details/51580647
