# msysGit中文环境配置及跨平台开发注意事项

注：因从 Git V1.7.10 开始引入了 Unicode 支持，所以在此版本之前会出现的乱码问题可能在之后的版本则不需要设置，但仍存在[一些乱码问题](https://github.com/kblees/git/wiki#wiki-Known_Issues)，因此我仍然强烈推荐与 [mintty](http://code.google.com/p/mintty/) 配合使用。

Git 是一个非常不错的分布式版本控制系统，虽然它最初设计在 Linux 平台下，但现在已经被很好的移植到其它平台。Windows 平台下对应的 Git 工具为 [msysGit](http://code.google.com/p/msysgit/)，在涉及到跨语言开发时我们需要做一些配置来规避部分问题。但有些问题因为平台差异并不能够很好的解决，但只要在使用时留心这些差异，我们仍然可以让 msysGit 几乎完美的工作。

##Git Bash中不能输入中文

**解决方法**

在 `/etc/inputrc` 中添加或修改以下配置:

```
set output-meta on
set convert-meta off
```

## 中文log乱码

**解决方法**

在 `/etc/gitconfig` 中添加或修改以下配置:

```
[gui]
    encoding = utf-8
[i18n]
    commitEncoding = gbk
    logOutputEncoding = gbk
```

或在 Git Bash 中运行下列代码:

```bash
git config --system gui.encoding utf-8
git config --system i18n.commitEncoding gbk
git config --system i18n.logOutputEncoding gbk
```

并在 `/etc/profile` 中添加:

    export LESSCHARSET=utf-8

### 说明

- gui.encoding = utf-8 解决 Git GUI 和 GITK 里中文乱码；
- i18n.commitEncoding = gbk 提交 log 使用的编码；
- i18n.logOutputEncoding = gbk 使在运行 git log 时将 utf-8 编码转换成 gbk 编码;
- export LESSCHARSET=utf-8 设置 less 分页器的编码，使 git log 在使用分页时正常显示中文（默认分页）。

## Git Bash中ls命令查看中文文件名或目录乱码

**解决方法**

在 `/etc/git-completion.bash` 添加:

    alias ls='ls --show-control-chars'

如果你想 ls 也像其它环境下一样自动着色，也可以用下面替代:

    alias ls='ls --show-control-chars --color=auto'

注意: 这样虽然能解决中文文件名或目录的问题，但是在跨平台开发时，不应使用中文文件名和目录。

## 使用mintty

通过上面的设置我们便可以顺利地进行跨语言及跨平台的开发了，但我觉得仍有必要给大家介绍 [mintty](http://code.google.com/p/mintty/)，有了它，所有的跨语言问题都可以通过配置得到完美的解决。mintty 的部署十分简单，我们只需要下载 mintty 对应的 [MSYS](http://www.mingw.org/wiki/MSYS) 版本，并保存在 Git 安装目录下的 bin 目录下，然后通过 Git Bash 运行下列代码便可以在 mintty 执行相应的命令：

```bash
#!/bin/sh

mintty sh -l
```

需要注意的是，如果你 mintty 设置为 Linux 环境开发，即 `~/.minttyrc` 对应的设置如下:

```
Locale=zh_CN
Charset=UTF-8
```

你需要将 `/etc/gitconfig` 中的 `i18n` 设置节点全部设置为 `utf-8`，以及 `core.quotepath` 设置为 `false`，即:

```
[core]
quotepath = false
[i18n]
commitEncoding = utf-8
logOutputEncoding = utf-8
```

虽然此时关于 LESSCHARSET 的设置可有可无，但为了更好地交替使用 Git Bash 和 mintty，我所做的方法是保留此设置，并准备两个版本的 gitconfig 分别对应 gitconfig.msysgit 和 gitconfig.mintty，并在 `/etc/bin` 目录下新建 usemsysgit 和 usemintty 文件如下:

```bash
#!/bin/sh
# usemsysgit

cp /etc/gitconfig.msysgit /etc/gitconfig
printf 'msysGit is ready.'
```

```bash
#!/bin/sh
# usemintty

cp /etc/gitconfig.mintty /etc/gitconfig
mintty /bin/bash -l
```

以后你需要在使用前执行相应版本的命令，如果你有更好的方法，不妨同大家一起分享 :)
换行问题

之所以把换行问题放在最后，是因为不能用固定的换行配置去适应多种不同的环境，只有何种配置更适合当前环境。Git 通过设定 `core.eol`, `core.safecrlf`, `core.autocrlf` 的值相应地处理换行问题，具体如下：

```
core.eol
    -lf # 使用 LF 为行结束类型。
    -crlf # 使用 CRLF 为行结束类型。
    -native # 默认，使用本地（当前操作系统）的行结束类型。
            # Windows 使用 CRLF，Linux 和 MacOS 使用 LF（MacOS 9 前使用 CR）。

core.safecrlf
    -true # 保证换行类型转换为可逆转换（如同时包含 CRLF 和 LF 换行符时便不是可逆转换）。
    -false # 不检查换行类型转换。
    -warn # 当换行类型转换不可逆时显示警告信息，但不影响操作执行。

core.autocrlf
    -true # 提交时自动将 CRLF 转换为 LF，获取自动将 LF 转换为 CRLF。
    -false # 不转换换行类型（本地和源的代码完全一致）。
    -input # 提交时转换将 CRLF 转换为 LF，获取时不转换。
```

不同的人对于换行的配置有不同的理解，你可以选择自己认为满意的方式，比如我比较推荐的方案如下：

```
# 换行类型为 LF 的环境中配置。
core.autocrlf = input

# 换行类型为 CRLF 的环境中配置。
core.autocrlf = true
```

上面这种配置方式也是比较主流的配置方式，但当你跨平台拷贝移动源仓库时请记得先转换换行类型（可通过修改配置后再获取的方式）。还有一个问题就是如果二进制文件如果在传输中转换结束类型时往往会使文件不能正常使用，因此需通过设置 [gitattributes](http://schacon.github.com/git/gitattributes.html) 将这些文件排出，例如当前项目中包含 HelloWorld.exe 的二进制文件，则在同级或上级目录的 .gitattributes 文件中做如下设置（一般为项目根目录）。

    *.exe binary

参考资料：
- [git-config(1) Manual Page](http://schacon.github.com/git/git-config.html)
- [gitattributes(5) Manual Page](http://schacon.github.com/git/gitattributes.html)
- [Pro Git - Pro Git 7.0 自定义 Git](http://progit.org/book/zh/ch7-0.html)

---
via: http://baifa.me/2010/10/msysgit.html
