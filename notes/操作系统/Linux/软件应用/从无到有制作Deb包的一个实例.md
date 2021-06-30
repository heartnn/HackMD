---
tags: deb
via: http://www.linuxsir.org/bbs/thread322285.html
---

# 从无到有制作Deb包的一个实例

我希望从零开始制作出一个自己的Deb包，意思是连软件的源码都是自己写的，类似于deb from scratch吧，那么这样的一个制作过程大致由三部分构成：

1. 源码的编写和测试
2. 使用autotools工具生成符合Gnu编程标准的相关文件，如configure，makefile等。
3. 按照Debian的方式制作Deb包文件

现在我编写了一个小软件hb-0.01，意思是hyperbolic，是自己写的一个双曲函数和反双曲函数的小软件，总共只有三个文件，在`/home/wen1/hb-0.01/`目录下，整个制作hb软件的deb包的过程为：

## 第一步：源码编写

这个步骤由用户自己编写，我的小软件总共只有三个文件，一个是头文件，一个是函数实现的C文件，还有一个主程序C文件，系统显示为：

```bash
wen1@lenny:~/hb-0.01$ ls -l
total 12
-rw-r--r-- 1 wen1 wen1 140 2008-01-26 15:17 bolic.h
-rw-r--r-- 1 wen1 wen1 369 2008-01-26 15:17 boliclib.c
-rw-r--r-- 1 wen1 wen1 300 2008-01-26 15:17 hb.c
```

## 第二步：使用Autotools工具生成所有符合Gnu编程标准的配置文件等

这个过程的步骤比较多，具体来说，包括有：

1\. 使用autoscan命令来生成一个configure的模板文件。系统操作为：

```bash
wen1@lenny:~/hb-0.01$ autoscan
wen1@lenny:~/hb-0.01$ ls -l
total 16
-rw-r--r-- 1 wen1 wen1 0 2008-01-26 15:25 autoscan.log
-rw-r--r-- 1 wen1 wen1 140 2008-01-26 15:17 bolic.h
-rw-r--r-- 1 wen1 wen1 369 2008-01-26 15:17 boliclib.c
-rw-r--r-- 1 wen1 wen1 484 2008-01-26 15:25 configure.scan
-rw-r--r-- 1 wen1 wen1 300 2008-01-26 15:17 hb.c
```

可以看到现在生成了一个configure.scan文件。

2\. 将configure.scan文件改名为configure.in，并进行相应的修改，我的是：

```makefile
AC_PREREQ(2.61)
AC_INIT(hb, 0.01, wenheping@tom.com)
AM_INIT_AUTOMAKE(hb,0.01)
# Checks for programs.
AC_PROG_CC
# Checks for libraries.
# Checks for header files.
# Checks for typedefs, structures, and compiler characteristics.
# Checks for library functions.
AC_CHECK_FUNCS([sqrt])
AC_OUTPUT(Makefile)
```

一般来说，这里只有AC_INIT、AC_OUTPUT、AM_INIT_AUTOMAKE三个参数需要根据自己的情况修改一下，其他自动生成的东西不动。

3\. 执行命令aclocal和autoconf，生成configure文件:

```bash
wen1@lenny:~/hb-0.01$ aclocal
wen1@lenny:~/hb-0.01$ autoconf
wen1@lenny:~/hb-0.01$ ls -l
total 184
-rw-r--r-- 1 wen1 wen1 31848 2008-01-26 15:36 aclocal.m4
drwxr-xr-x 2 wen1 wen1 4096 2008-01-26 15:36 autom4te.cache
-rw-r--r-- 1 wen1 wen1 0 2008-01-26 15:25 autoscan.log
-rw-r--r-- 1 wen1 wen1 140 2008-01-26 15:17 bolic.h
-rw-r--r-- 1 wen1 wen1 369 2008-01-26 15:17 boliclib.c
-rwxr-xr-x 1 wen1 wen1 130126 2008-01-26 15:36 configure
-rw-r--r-- 1 wen1 wen1 434 2008-01-26 15:35 configure.in
-rw-r--r-- 1 wen1 wen1 300 2008-01-26 15:17 hb.c
```

可以看到现在生成了configure文件。

4\. 新建Makefile.am文件，再由automake工具根据所写的Makefile.am文件来自动生成Makefile.in文件。

Makefile.am文件一般定义自己的软件最后生成的可执行程序名字、需要连接的库等，我的该文件内容为：

```makefile
AUTOMAKE_OPTIONS=foreign
bin_PROGRAMS=hb # 最后生成的可执行文件的名字
hb_SOURCES=hb.c bolic.h boliclib.c # 所有的源码文件
LIBS = -lm # 需要连接math库
```

然后用automake生成Makefile.in文件，为了符合规范，先：

```bash
wen1@lenny:~/hb-0.01$ touch NEWS README AUTHORS ChangeLog
```

然后的系统显示为：

```bash
wen1@lenny:~/hb-0.01$ automake --add-missing
wen1@lenny:~/hb-0.01$ ls
aclocal.m4 bolic.h configure.in INSTALL missing
AUTHORS boliclib.c COPYING install-sh NEWS
autom4te.cache ChangeLog depcomp Makefile.am README
autoscan.log configure hb.c Makefile.in
```

5\. 执行configure生成Makefile。

这一步很简单，生成Makefile之后，还可以使用一些其他的make命令，如make clean，make install，make dist，看看它们会给你什么样的效果。

以上的例子很简单，更为复杂的Autotools的运用和Makefile的编写等内容请参照：

- [http://www.gnu.org/software/autoconf/](http://www.gnu.org/software/autoconf/) (最权威的了)
- [http://sourceware.org/autobook/](http://sourceware.org/autobook/) (很详细的一本书)
- [http://www.lrde.epita.fr/~adl/autotools.html](http://www.lrde.epita.fr/~adl/autotools.html) (这个演示文稿做得相当不错)

## 第三步：生成Deb包

生成deb包，有两种方法，一种很简单，直接在软件源码目录内运行checkinstall再回答几个简单的问题就可以了，但一般不推荐使用该办法；二是按照Debian的New Maintainer Guide一步一步制作deb包，详细的过程请参照： [http://www.debian.org/doc/maint-guide/](http://www.debian.org/doc/maint-guide/)

我的制作过程大致为：

1\. 下载安装必须的软件：

```bash
apt-get install build-essential dpkg-dev dh-make debhelper fakeroot gnupg lintian Linda pbuilder
```

2\. 生成tar.gz文件并把该文件移动到源码的父目录：

```bash
wen1@lenny:~/hb-0.01$ make dist
wen1@lenny:~/hb-0.01$ mv hb*.gz ../
```

3\. 运行`dh_make`命令。

```bash
wen1@lenny:~/hb-0.01$ dh_make -e wenheping@tom.com -f ../hb-0.01.tar.gz
```

运行该命令之后，原来的软件包将会被打包为hb-0.01.orig.tar.gz并放在父目录中，注意文件名中包名称和版本是以_分割的而且tar.gz之前有orig.。

4\. 修改Makefile、control等文件。

Debian要求可执行文件不能安装在`/usr/local`目录下，所以然后要检查Makefile文件的相应的安装位置，我的这个小软件本来就是安装在`/usr/bin`下，所以不用修改Makefile文件。

至于control copyright等文件，如果只是自己做一个自己用的deb包文件，不改也可以，但是如果是为Debian做的准备上传到Debian apt源中的话，就要按照规范认真地填写。

5\. 生成deb包文件，我是这样的：

```bash
wen1@lenny:~/hb-0.01$ ./configure
wen1@lenny:~/hb-0.01$ make
wen1@lenny:~/hb-0.01$ dpkg-buildpackage –rfakeroot
```

这样，我的hb软件的deb包文件及其他相关文件就生成了，但是生成于源码目录的父目录中。
