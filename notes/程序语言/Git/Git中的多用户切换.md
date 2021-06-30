# Git中的多用户切换

在使用 [Git](https://git-scm.com/) 管理多个项目时，可能存在用户名称、用户邮箱、密钥等不同的情况，如果在切换项目时再通过命令或配置文件更改 Git 配置，另外可能需要使用当前密钥覆盖旧的密钥，这样做虽然可行，但作为这么优秀的一个版本控制软件，这种做法显然太过原始了，Git 提供了更方便的多用户切换方案。

## Git 配置

如果当前 Git 配置（全局配置或用户配置，可通过形如 `git config user.name` 的命令查看指定配置，如果需要查看的是 `user.email`，则使用 `git config user.email`）与项目所需的配置不同时，可以通过更改项目中的 `.git/config` 文件或在项目中使用命令更改项目配置：

```
# your name 需要替换为你的名字。
# 如果需要更改其它配置如用户邮箱，则使用 git config user.email 'your email'。
git config user.name 'your name'
```

如果需要在克隆仓库时或其它情况下临时改变 Git 配置，可在 SHELL 环境中设置 `GIT_COMMITTER_NAME`, `GIT_COMMITTER_EMAIL`, `GIT_AUTHOR_NAME`, `GIT_AUTHOR_EMAIL` 这些环境变量的值。

如果需要在克隆仓库时或其它情况下临时改变 Git 配置，可在 SHELL 环境中设置 `GIT_COMMITTER_NAME`, `GIT_COMMITTER_EMAIL`, `GIT_AUTHOR_NAME`, `GIT_AUTHOR_EMAIL` 这些环境变量的值。

## SSH 密钥

```
Host github               # 友好名称。
  HostName github.com     # 真实的地址。
  User git                # 为 Git 服务器提供 Git 服务的用户名称，一般为 git。
  IdentityFile github     # 密钥文件的地址（相对路径或绝对路径）。
```

完成配置 config 文件后，可能更改项目中的 `.git/config` 文件或 `git config` 命令将仓库源地址中的主机名（github.com）替换为配置的友好名称（github），假设之前的仓库源地址为：`git://github.com:username/projectname.git`，通过替换则为 `git://github:username/projectname.git`，这样我们在使用之前的非 GitHub 项目时仍然使用 `~/.ssh/id_rsa` 做为密钥，而在使用 GitHub 项目时则使用指定的密钥 `~/.ssh/github`，当然你也可以将密钥放到其它路径或使用不同的名称。

## 参考资料

- [初次运行 Git 前的配置](http://progit.org/book/zh/ch1-5.html)
- [Set your user name, email and GitHub token](http://help.github.com/set-your-user-name-email-and-github-token/)
- [SSH issues](http://help.github.com/ssh-issues/)

---
via: http://baifa.me/2011/02/git.html