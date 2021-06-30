# Git设置代理访问GitHub和Bitbucket

## 介绍

目前，网络上可选择的Git远程仓库比较多，其中用的较多的可能就是**github**和**bitbucket**（当然，你也可以使用自己搭建的远程仓库）。github和bitbucket的主要区别在于：bitbucket创建私人库是免费的。如果你不介意自己的代码公开，那你就可以使用github。如果，你有些私人的代码的话，又需要版本控制，这时候bitbucket就满足需要了。

但是，在国内的主要问题是：**网络不稳定**。这样，就会经常发生git不能push的情况。所以，这时候如果你有个代理服务器，就可以通过设置使git通过代理访问远程仓库，达到家和公司代码同步的目的。

Git允许使用[三种协议](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols)来连接远程仓库：`ssh`、`http`、`git`。所以，如果你要设置代理，必须首先明确本地git使用何种协议连接远程仓库，然后根据不同协议设置代理。

**前提**：socks5代理服务器，默认端口1080

## 设置SSH协议的代理

如果你的远程仓库拥有如下的格式：

```
git@github.com:archerie/learngit.git
ssh://git@github.com:archerie/learngit.git
```

那么，你使用的是[`SSH协议`](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols#The-SSH-Protocol)连接的远程仓库。因为git依赖ssh去连接，所以，我们需要配置ssh的socks5代理实现git的代理。在ssh的配置文件`~/.ssh/config`（没有则新建）使用`ProxyCommand`配置：

```
#Linux
Host bitbucket.org
  User git
  Port 22
  Hostname bitbucket.org
  ProxyCommand nc -x 127.0.0.1:1080 %h %p
```

```
#windows
Host bitbucket.org
  User git
  Port 22
  Hostname bitbucket.org
  ProxyCommand connect -S 127.0.0.1:1080 %h %p
```

如果你使用`github`，那么你只需要把`bitbucket.org`换成`github.com`就行了。具体配置的含义请参考[ssh_config(5)](http://www.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man5/ssh_config.5?query=ssh_config&sec=5&arch=amd64)。

## 设置http/https协议代理

如果你的远程仓库链接拥有如下格式：

```
http://github.com/archerie/learngit.git
https://github.com/archerie/learngit.git
```

说明你使用的是`http/https`协议，所以可以使用git配套的CMSSW支持的代理协议：SOCKS4、SOCKS5和HTTPS/HTTPS。可通过配置`http.proxy`配置：

```bash
# 全局设置
git config --global http.proxy socks5://localhost:1080

# 本次设置
git clone https://github.com/example/example.git --config "http.proxy=127.0.0.1:1080"
```

这里演示的是socks5的配置，需要其他配置的可参考[git-config](https://www.kernel.org/pub/software/scm/git/docs/git-config.html)配置中的`http.proxy`。

## 设置Git协议的代理

**Git协议**是Git提供的一个守护进程，它监听专门的端口(9418)，然后提供类似于ssh协议一样的服务，只是它不需要验证。所以，然后用户通过网络都可以使用git协议连接提供git连接的仓库。如果远程仓库的链接是如下形式：

    git://github.com/archerie/learngit.git

那么，该仓库使用[git协议](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols#The-Git-Protocol)连接。所以，需要使用CMSSW提供的简单脚本去通过socks5代理访问：`git-proxy`。配置如下：

```bash
git config --global core.gitproxy "git-proxy"
git config --global socks.proxy "localhost:1080"
```

了解更多，使用`git-proxy --help`。

## 参考链接

- http://cms-sw.github.io/tutorial-proxy.html
- http://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols
- http://stackoverflow.com/questions/783811/getting-git-to-work-with-a-proxy-server

---
via: https://blog.csdn.net/w1196726224/article/details/50252929