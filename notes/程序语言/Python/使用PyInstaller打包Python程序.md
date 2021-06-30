# 使用PyInstaller打包Python程序

有不少订阅本公众号的朋友都不是玩 Python，甚至都不是计算机相关专业的，当我给他们一个 Python 程序时，他们是完全不知道该怎么运行的。

于是我想是不是可以将我的程序打包成可执行文件，直接运行？

就像这样：

![hello](vx_images/20200208103148377_17039.gif)

Python 程序都是脚本的方式，一般是在解析器里运行，如果要发布出去，需要提前安装解析器才可以运行，为了在 Windows 里方便发布，只要点击一个 EXE 文件运行，并且打包所需要库文件，这样发布给用户使用就会更方便。

## PyInstaller

PyInstaller 是一个十分有用的第三方库，可以用来打包 python 应用程序，打包完的程序就可以在没有安装 Python 解释器的机器上运行了。

它能够在 Windows、Linux、 Mac OS X 等操作系统下将 Python 源文件打包，通过对源文件打包， Python 程序可以在没有安装 Python 的环境中运行，也可以作为一个 独立文件方便传递和管理。

PyInstaller 支持 Python 2.7 / 3.4-3.7。可以在 Windows、Mac OS X 和 Linux 上使用，但是并不是跨平台的，而是说你要是希望打包成 .exe 文件，需要在 Windows 系统上运行 PyInstaller 进行打包工作。

下面我们以 Windows 为例来进行程序的打包工作。

## 安装

```bash
pip install pyinstaller
# 或者
python -m pip install pyinstaller
```

安装成功：

![安装成功](vx_images/20200208103218819_27955.png)

## 使用

    pyinstaller -F helloworld.py

其中，`-F` 表示打包成单独的 .exe 文件，这时生成的 .exe 文件会比较大，而且运行速度回较慢。仅仅一个 helloworld 程序，生成的文件就 5MB 大。

另外，使用 `-i` 还可以指定可执行文件的图标；
`-w` 表示去掉控制台窗口，这在 GUI 界面时非常有用。不过如果是命令行程序的话那就把这个选项删除吧！

PyInstaller 会对脚本进行解析，并做出如下动作：

1. 在脚本目录生成 helloworld.spec 文件；
2. 创建一个 build 目录；
3. 写入一些日志文件和中间流程文件到 build 目录；
4. 创建 dist 目录；
5. 生成可执行文件到 dist 目录。

执行流程：

```bash
$ pyinstaller -F helloworld.py
838 INFO: PyInstaller: 3.4
839 INFO: Python: 3.4.3
841 INFO: Platform: Windows-8-6.2.9200
842 INFO: wrote d:\code\Python\pyinstaller\helloworld.spec
858 INFO: UPX is not available.
885 INFO: Extending PYTHONPATH with paths
['d:\\code\\Python\\pyinstaller', 'd:\\code\\Python\\pyinstaller']
886 INFO: checking Analysis
887 INFO: Building Analysis because Analysis-00.toc is non existent
888 INFO: Initializing module dependency graph...
890 INFO: Initializing module graph hooks...
899 INFO: Analyzing base_library.zip ...
6225 INFO: Processing pre-find module path hook   distutils
11387 INFO: running Analysis Analysis-00.toc
12012 INFO: Caching module hooks...
12022 INFO: Analyzing d:\code\Python\pyinstaller\helloworld.py
12027 INFO: Loading module hooks...
12028 INFO: Loading module hook "hook-encodings.py"...
12395 INFO: Loading module hook "hook-xml.py"...
13507 INFO: Loading module hook "hook-pydoc.py"...
13508 INFO: Loading module hook "hook-distutils.py"...
13606 INFO: Looking for ctypes DLLs
13662 INFO: Analyzing run-time hooks ...
13677 INFO: Looking for dynamic libraries
13894 INFO: Looking for eggs
13895 INFO: Using Python library C:\WINDOWS\system32\python34.dll
13895 INFO: Found binding redirects:
[]
13915 INFO: Warnings written to d:\code\Python\pyinstaller\build\helloworld\warn-helloworld.txt
14035 INFO: Graph cross-reference written to d:\code\Python\pyinstaller\build\helloworld\xref-helloworld.html
14287 INFO: checking PYZ
14287 INFO: Building PYZ because PYZ-00.toc is non existent
14288 INFO: Building PYZ (ZlibArchive) d:\code\Python\pyinstaller\build\helloworld\PYZ-00.pyz
15836 INFO: Building PYZ (ZlibArchive) d:\code\Python\pyinstaller\build\helloworld\PYZ-00.pyz completed successfully.
15883 INFO: checking PKG
15884 INFO: Building PKG because PKG-00.toc is non existent
15884 INFO: Building PKG (CArchive) PKG-00.pkg
18528 INFO: Building PKG (CArchive) PKG-00.pkg completed successfully.
18536 INFO: Bootloader D:\program\Python34\lib\site-packages\PyInstaller\bootloader\Windows-64bit\run.exe
18537 INFO: checking EXE
18537 INFO: Building EXE because EXE-00.toc is non existent
18538 INFO: Building EXE from EXE-00.toc
18538 INFO: Appending archive to EXE d:\code\Python\pyinstaller\dist\helloworld.exe
18548 INFO: Building EXE from EXE-00.toc completed successfully.
```

生成文件：

![生成两个目录](vx_images/20200208103235608_1071.png)

![生成的可执行文件](vx_images/20200208103242735_14133.png)

## 注意事项

1、直接运行最终的 .exe 程序，可能会出现**一闪而过**的情况，这种情况下要么是程序运行结束（比如直接打印的 helloWorld），要么程序出现错误退出了。

这种情况下，建议在命令行 cmd 下运行 .exe 文件，这时就会有文本输出到窗口；

2、`-i` 是改变图标的，但是我发现是有些 bug 的，客官请看：

![图标变化](vx_images/20200208103311064_16421.gif)

放大过程中，图标才变成了我们设置的图标。

3、写代码的时候应当有个良好的习惯，用什么函数导什么函数，不要上来 import 整个库，最后你会发现你一个 100KB 的代码打包出来有 500MB；

4、当你的代码需要调用一些图片和资源文件的，这是不会自动导入的，需要你自己手动复制进去才行。不然 exe 文件运行时命令窗口会报错找不到这个文件。

导入方法：

假设程序中需要引入一个 `test.txt` 文件，首先我们运行：

    pyi-makespec -F helloworld.py

此时会生成一个 `.spec` 文件，这个文件会告诉 pyinstaller 如何处理你的脚本，pyinstaller 创建一个 exe 的文件就是依靠它里面的内容进行执行的。

正常情况下你不需要去修改这个 spec 文件，除非你需要打包一个 dll 或者 so 文件或者其他数据文件。

那么我们就需要修改这个 spec 文件：

```python
a = Analysis(['helloworld.py'],
 pathex=['/home/test'],
 binaries=[],
 datas=[],  ### <------- 改
```

修改为：

```python
a = Analysis(['helloworld.py'],
 pathex=['/home/test'],
 binaries=[],
 datas=[('test.txt','.')],  ## <---- 修改此处添加外部文件
```

然后在生成 exe 文件：

    pyinstaller helloworld.spec

然后生成的文件就可以正常引入外部文件了。

## 总结

本文只是使用 PyInstaller 打包流程进行简单的介绍，更多内容可以参见官方文档：<https://pyinstaller.readthedocs.io>。

---
via: https://hoxis.github.io/python-pyinstaller.html
