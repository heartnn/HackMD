---
tags: deb
via: http://www.linuxsir.org/bbs/thread3310.html
---

# 自己动手制作.deb文件

## 1. 安装相关软件

    apt-get install debhelper dh-make dpkg-dev

## 2. 重新编译Debian包好的套件

下载 sources

    apt-get source package

进入该 package 目录『 cd package-version 』下底下

命令： `dpkg-buildpackage`

就会有一连串的东东开始configure、compile等等，这是最简单的。但只能说compile成跟maintainer一样，并没有变量。

## 3. 以Debian包好的套件为基础

先切换 package下的 debian 会发现到几个重要的档案：

`rules`

编辑它，有点类似Makefile 。

`Changelog`

指定package的version或修正哪些信息 。

`control`

处理相依性的问题,里面会有多个package，比如libqt,、libqt-dev等等之类的东西，而相依性再Conflict这个字段处理，要输入相依的package name 以及版本（版本也可不输入）。

以 zsnes 软件作说明：

    apt-get source zsnes

抓完之后会发现有几个档案和目录（请先安装好debhelper），进入zsnes-1.337这个目录下的debian编辑rules，将configure里面的参数修改一下，然后退到上一层的目录，也就是zsnes打`dpkg-buildpackage`（要root或是fakeroot）。

成功后,就会这上一层的目录看到.deb

`dpkg -i package`

这样就完成了。

## 4. Debian没有包过的

请先抓该软件的`source tarball untar`之后，将目录改成以下形式：

`package-version`

注意！package name请用小写，后面要接版本数字 。

    cd package-version

打dh_make，会问你这个package要生成single deb or Multi deb 请自己选择。

成功后，就会帮你产生debian这个目录了，接下来就跟最前面一样的意思了。
