# Git同步它人的远程仓库至自己的Git服务器并自动定时更新

昨天晚上将2015年一篇不负责的 Git 服务器搭建的教程更新了一下，详见[在 CentOS 下搭建自己的 Git 服务器及使用 nginx 配置 gitweb 面板](https://www.lvtao.net/config/centos-git-setup-nginx-gitweb.html)，今天折腾了一些本地的项目，倒是可以了。现在的需要是将远程的仓库同步一份到自己的git服务器。

开始想的是，远程 clone 下来本地，然后本地提交一次到自己的git服务器。实在是太二了...

我的需求是：

- Git服务器A: github之类的远程仓库。
- Git服务器B: 我自己搭的内网的。
- 我要在B上面建个仓库，自动同步服务器A的。

so...按上面的想法，还要手工，还不能自动，还不能定时...太SB了。

一般搜寻之后有了，我本地的仓库地址是 `/www/git` 目录下。所以以下的就不多解析了。

现在我假设要同步 `https://github.com/easysoft/zsite` 这个项目，并让它半夜自动更新。

```bash
mkdir /www/git/chanzhi.git
cd /www/git/chanzhi.git
git clone --mirror https://github.com/easysoft/zsite.git ./
```

就OK了。

至于定时更新：

```bash
git --git-dir=/www/git/chanzhi.git remote update
```

来个脚本，方便一些。

```bash
#!/bin/bash
read -p "请输入本地仓库名称:" name
read -p "请输入远程仓库地址:" url
if [ ! -n "$name" ];then
  echo "不能为空"
else
  mkdir -p /www/git/crond_$name.git
  cd /www/git/crond_$name.git
  git clone --mirror $url ./
  echo "1 1 * * * git --git-dir=/www/git/crond_$name.git remote update" >> /var/spool/cron/root
  echo "http://git.lvtao.net/crond_$name.git"
fi
```

---
via: https://www.lvtao.net/config/git-other-auto-time-rync.html
