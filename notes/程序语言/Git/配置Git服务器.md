# 配置Git服务器

正当我们正在愉快地使用或向别人推荐 [Git](https://git-scm.com/) 时，一些比较注重数据保密性的人多半会向你讯问关于 Git 服务器的一些问题。一般情况下，像开源项目这类不需要数据保密的案例使用现有的 Git 服务提供商（如：[GitHub](https://github.com/)）是非常不错的选择，否则就需要自己准备 Git 服务器，本文以 Ubuntu 为例为大家介绍怎样搭建 Git 服务器，虽然在 Windows 环境下也可以推荐 Git 服务器，但我强烈建议你不要做出这样的选择。

## 安装 Git

```bash
$ sudo apt-get update && sudo apt-get upgrade # 更新系统。
$ sudo apt-get install git-core               # 安装 git 核心文件。
```

## 权限支持

如果项目成员较少使用直接编辑 `authorized_keys` 文件的方法来进行授权也是一种选择，但这种做法还缺少必要的权限管理（每个人都对所有项目拥有完整的读写权限），如果项目的成长历程中不断增加或减少成员，密钥的管理十分不便且容易出错。因此采用像 [Gitosis](https://github.com/res0nat0r/gitosis) 或 [Gitolite](https://github.com/sitaramc/gitolite) 这样进行集中授权的方式成为了多种选择（如 HTTP、GIT-DAEMON、SSH 等）中的最佳选择，不过 Gitosis 从 2009 年开始已经未有更新，因此本文也将介绍使用 Gitolite 对仓库进行权限控制。下面代码即为 Gitolite 的安装和初始化过程：

```bash
$ sudo adduser git && su git                   # 新建用户。
$ mkdir ~/resources && cd ~/resources          # 新建并进入源代码目录。
$ git clone git://github.com/sitaramc/gitolite # 获取源代码。
$ gitolite/src/gl-system-install               # 安装。
# $ PATH=/home/git/bin:$PATH                   # 如果提示设置环境变量。
# $ ssh-keygen -t rsa -b 2048 -C 'YourComment' # 新建密钥对。
$ gl-setup -q YourName.pub                     # 初始化。
```

经过上面的配置我们便可通过下面的代码获得进行集中授权的项目，通过管理该项目便从而方便又灵活地进行用户管理和权限控制。

    $ git clone git@server:gitolite

## 参考资料

- [Pro Git - Pro Git Book](http://progit.org/book/zh/)
- [Gitolite online documentation](http://sitaramc.github.com/gitolite/)

---
via: http://baifa.me/2010/11/ubuntu-git.html
