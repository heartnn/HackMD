# 将pip源更换到国内镜像

用pip管理工具安装库文件时，默认使用国外的源文件，因此在国内的下载速度会比较慢，可能只有50KB/s。幸好，国内的一些顶级科研机构已经给我们准备好了各种镜像，下载速度可达2MB/s。

其中，比较常用的国内镜像包括：

- 阿里云 <https://mirrors.aliyun.com/pypi/simple>
- 腾讯 <https://mirrors.cloud.tencent.com/pypi/simple>
- 豆瓣 <https://pypi.doubanio.com/simple>
- 清华大学 <https://pypi.tuna.tsinghua.edu.cn/simple/>

## 1. 临时使用

可以在使用pip的时候，加上参数-i和镜像地址(如
<https://mirrors.cloud.tencent.com/pypi/simple>)，例如：`pip install -i https://mirrors.cloud.tencent.com/pypi/simple pandas`，这样就会从腾讯镜像安装pandas库。

## 2. 永久修改，一劳永逸

(1) **Linux下**，修改 `~/.pip/pip.conf` (没有就创建一个文件夹及文件)，内容如下：

```ini
[global]
index-url = https://mirrors.cloud.tencent.com/pypi/simple
[install]
trusted-host = mirrors.cloud.tencent.com
```

(2) **windows下**，直接在user目录中创建一个pip目录，如：`C:\Users\xx\pip`，然后新建文件`pip.ini`，即 `%HOMEPATH%\pip\pip.ini`，在`pip.ini`文件中输入以下内容（以腾讯镜像为例）：

```ini
[global]
index-url = https://mirrors.cloud.tencent.com/pypi/simple
[install]
trusted-host = mirrors.cloud.tencent.com
```

---
via: https://blog.csdn.net/sinat_21591675/article/details/82770360
