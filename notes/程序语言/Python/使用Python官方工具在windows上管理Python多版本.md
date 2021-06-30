# 使用Python官方工具在windows上管理Python多版本

## 0. 使用官方工具

网上很多教程还在用修改`Python.exe`文件名的方式来进行多版本的管理，其实Python官方在3.3已经有了一个官方的工具，使用起来也很方便。

## 1. 下载 2.x 和 3.x（3.3以上）的安装包

下载地址：<https://www.python.org/downloads/>

选择自己的版本下载

## 2. 安装Python 2.x 和 3.x

首先安装Python 2.x，使用默认配置即可，再安装Python 3.x，注意安装的时候选中`Python launcher`，这是Python官方用来管理多版本的工具。

## 3. 使用Python launcher

安装好之后在命令行输入`py`，可以打开默认的Python版本，其它参数如下（通过`py -h`查看）：

```bash
Launcher arguments:
-2     : Launch the latest Python 2.x version
-3     : Launch the latest Python 3.x version
-X.Y   : Launch the specified Python version
    The above all default to 64 bit if a matching 64 bit python is present.
-X.Y-32: Launch the specified 32bit Python version
-X-32  : Launch the latest 32bit Python X version
-X.Y-64: Launch the specified 64bit Python version
-X-64  : Launch the latest 64bit Python X version
-0  --list       : List the available pythons
-0p --list-paths : List with paths
```

可以看到能够通过参数来指定使用哪个版本的Python

```bash
py -2 # 启动Python2.x
py -2.14 # 启动Python2.14
py -0 # 查看当前安装的Python版本，默认版本后面会有一个星号
```

如果要使用指定版本的`pip`安装包

    py -2 -m pip install package_name

## 4. Python launcher使用的具体例子

### 1. 在命令行中使用

```bash
py -2 test.py # 使用python2.x来执行test.py
py -3.5 test.py # 使用python3.5来执行test.py
```

### 2. 在代码中使用

新建一个文件`get_version.py`，代码内容如下（注意电脑上要装上对应的版本）

```python
#! python2.7
# -*- coding:utf-8 -*-
import sys
import time

print(sys.version)
time.sleep(3600)
```

**双击**文件执行，可以看到打印的是Python2.7的版本号，修改文件内容如下

```python
#! python3.7
# -*- coding:utf-8 -*-
import sys
import time

print(sys.version)
time.sleep(3600)
```

这时候再次**双击**执行，打印的是3.7的版本号，可以看出使用哪个版本来执行是根据第一行的声明来选择的。还可以在命令行来执行

    py get_version.py

同样会按照声明来选择对应的Python版本。

---

上面就是工具的使用办法，很简单的，有时候为了避免库冲突还需要进行虚拟环境管理，继续往下看。

## 5. 安装virtualenv

随便选一个版本的`pip`安装，比如

    py -2 -m pip install virtualenv

`virtualenv`的使用方法简单说一下

```bash
virtualenv -h
Usage: virtualenv [OPTIONS] DEST_DIR

Options:
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  -v, --verbose         Increase verbosity.
  -q, --quiet           Decrease verbosity.
  -p PYTHON_EXE, --python=PYTHON_EXE
                        The Python interpreter to use, e.g.,
                        --python=python3.5 will use the python3.5 interpreter
                        to create the new environment.  The default is the
                        interpreter that virtualenv was installed with
                        (c:\python27\python.exe)
  --clear               Clear out the non-root install and start from scratch.
  --no-site-packages    DEPRECATED. Retained only for backward compatibility.
                        Not having access to global site-packages is now the
                        default behavior.
  --system-site-packages
                        Give the virtual environment access to the global
                        site-packages.
  --always-copy         Always copy files rather than symlinking.
  --relocatable         Make an EXISTING virtualenv environment relocatable.
                        This fixes up scripts and makes all .pth files
                        relative.
  --no-setuptools       Do not install setuptools in the new virtualenv.
  --no-pip              Do not install pip in the new virtualenv.
  --no-wheel            Do not install wheel in the new virtualenv.
  --extra-search-dir=DIR
                        Directory to look for setuptools/pip distributions in.
                        This option can be used multiple times.
  --download            Download preinstalled packages from PyPI.
  --no-download, --never-download
                        Do not download preinstalled packages from PyPI.
  --prompt=PROMPT       Provides an alternative prompt prefix for this
                        environment.
  --setuptools          DEPRECATED. Retained only for backward compatibility.
                        This option has no effect.
  --distribute          DEPRECATED. Retained only for backward compatibility.
                        This option has no effect.
  --unzip-setuptools    DEPRECATED.  Retained only for backward compatibility.
                        This option has no effect.
```

其它参数不介绍了，主要说一下`-p`，后面接上想要创建Python环境的版本（默认使用的是virtualenv所在的版本），这里要跟上Python.exe的路径。

    virtualenv -p path-to-python.exe venv

virtualenv的使用方法

```bash
# 创建虚拟环境名叫venv
virtualenv venv
New python executable in e:\test\venv\Scripts\python.exe
Installing setuptools, pip, wheel...done.

# 激活虚拟环境
.\venv\Scripts\activate.bat

# 在虚拟环境中安装包，执行脚本等
(venv) pip list
Package    Version
---------- -------
pip        18.0
setuptools 40.0.0
wheel      0.31.1

# 退出虚拟环境
(venv) deactivate
```

## 6. 使用virtualenvwrapper-win

这是一个virtualenv的封装，用来简化virtualenv的使用，使用下面的命令进行安装

    py -2 -m pip install virtualenvwrapper-win

> 可选：安装好之后配置一个环境变量（高级系统设置-环境变量），名为`WORKON_HOME`，值是你想保存虚拟环境的路径，你之后创建的所有虚拟环境都会保存到这里，配置好之后可以通过`echo %WORKON_HOME%`来查看。

安装好之后可以使用它来管理虚拟环境

```bash
workon name-of-venv # 进入虚拟环境
deactive # 退出虚拟环境
lsvirtualenv # 列出可用的虚拟环境
mkvirtualenv name-of-venv # 创建虚拟环境
rmvitualenv name-of-venv # 删除虚拟环境
cdvirtualenv name-of-venv # 进入虚拟环境目录
cdsitepackages name-of-venv # 进入虚拟环境的site-packages目录
lssitepackages name-of-venv # 列出site-packages目录的所有软件包
```

上述命令都可以用`command-name -h`来查看具体的参数。

## 7. 多个环境之间快速迁移包

```bash
pip freeze > packages.txt # 导出原有的包列表
pip install -r packages.txt # 在新的环境快速安装包
```

---
via: https://testerhome.com/topics/15192
