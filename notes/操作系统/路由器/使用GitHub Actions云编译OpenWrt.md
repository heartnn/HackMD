---
tags: github, openwrt
via: https://p3terx.com/archives/build-openwrt-with-github-actions.html

---

# 使用 GitHub Actions 云编译 OpenWrt

## 前言

Github Actions 是 Microsoft 收购 GitHub 后推出的 CI/CD 服务，它提供了性能配置非常不错的[虚拟服务器环境](https://p3terx.com/archives/github-actions-virtual-environment-simple-evaluation.html)（E5 2vCPU/7G RAM），基于它可以进行构建、测试、打包、部署项目。对于公开仓库可免费无时间限制的使用，且单次使用时间长达 6 个小时，这对于编译 OpenWrt 来说是非常充足的。不过 GitHub Actions 有一定的使用门槛，首先要了解[如何编写 workflow 文件](https://p3terx.com/archives/github-actions-started-tutorial.html)。不过不用担心，博主已经编写好了相关的 workflow 文件模版，只需要按照教程的步骤来操作即可。

## 方案特点

* 免费
* 一键快速编译
* 定时自动编译
* 客制化编译
* 并发编译（可同时进行20个编译任务）
* 无需搭建编译环境（在线`make menuconfig`生成配置文件)
* 无需消耗自己的计算机与服务器的计算资源（性感E5在线编译）
* 无需担心磁盘空间不足（近60G磁盘空间）
* 无需使用清理文件（内核更新不怕 boom ）
* 编译速度快（编译时间1-2小时）
* 编译成功率提升200%（万兆自由网络环境）
* 全新环境（杜绝编译环境不干净导致编译失败）

> 本解决方案是一个开放平台，任何人都可以基于此打造自己专属的编译方案。

## 项目地址

<https://github.com/P3TERX/Actions-OpenWrt>

支持项目请随手点个 `star`，让更多的人发现、使用并受益。

## 准备工作

* [GitHub](https://github.com/) 账号
* 搭建编译环境，生成`.config`文件。(可选)

> **TIPS:** 关于编译环境的搭建，推荐去看我之前写的相关文章，Windows 10 可以使用 [WSL](https://p3terx.com/archives/compiling-openwrt-with-wsl.html) ，macOS、Linux 可以使用 [Docker](https://p3terx.com/archives/build-openwrt-with-docker.html)。

## 基础使用

首先你必须要熟悉整个 OpenWrt 的编译过程，这会让你非常容易的理解如何使用 GitHub Actions 进行编译，即使你没有成功过。因为实际上本地编译近 90% 失败的原因是因为网络问题导致的，中国大陆特色，咱也不敢多说。GitHub Actions 服务器由 Microsoft Azure 提供，拥有万兆带宽，可以使编译成功率大大提升。

* 在自己搭建编译环境中使用 [Lean's OpenWrt](https://github.com/coolsnowwolf/lede) 源码生成`.config`文件。（或使用直接 SSH 连接到 Actions 进行操作，后面有说明。）

> **TIPS:** 方案默认引用 Lean 的源码，因为他的 README 影响了我开始学习编译，也就有了这个项目，而且他的源码非常的优秀。有其它需求可自行修改 workflow 文件，方法后面的进阶使用中有说明。

* 进入 [P3TERX/Actions-OpenWrt](https://github.com/P3TERX/Actions-OpenWrt) 项目页面，点击页面中的 [Use this template](https://github.com/P3TERX/Actions-OpenWrt/generate)（使用这个模版）按钮。
    
![](vx_images/383407101263.png)
    
* 填写仓库名称，然后点击`Create repository from template`（从模版创建储存库）按钮。
    
    ![](vx_images/4392207119689.png)
    
* 经过几秒钟的等待，页面会跳转到新建的仓库，内容和我的项目是相同的。然后点击`Create new file`（创建新文件）按钮。
    
    ![](vx_images/845708107556.png)
    
* 文件名填写为`.config`，把生成的`.config` 文件的内容复制粘贴到下面的文本框中。
    
    ![](vx_images/3609708127722.png)
    
* 翻到页面最下方，点击`Commit new file`（提交新文件）按钮。
    
    ![](vx_images/55409120391.png)
    
* 在 Actions 页面选择`Build OpenWrt`，然后点击`Run Workflow`按钮，即可开始编译。（如果需要 SSH 连接则把`SSH connection to Actions`的值改为`true`。其它详情参见**进阶使用**相关章节）
    
    ![](vx_images/2727409116946.png)
    
* 最后经过一两个小时的等待，不出意外你就可以在 Actions 页面看到已经打包好的固件目录压缩包。
    
    ![](vx_images/5069009112700.png)
    

> **TIPS:** 如需 ipk 文件可以在**进阶使用**章节找到方法。因为大多数人只需要固件，而且总是有萌新问固件在哪，所以现在默认只上传固件。

## 进阶使用

### 自定义环境变量与功能

打开 workflow 文件（`.github/workflows/build-openwrt.yml`），你会看到有如下一些环境变量，可按照自己的需求对这些变量进行定义。

```none
env:
  REPO_URL: https://github.com/coolsnowwolf/lede
  REPO_BRANCH: master
  FEEDS_CONF: feeds.conf.default
  CONFIG_FILE: .config
  DIY_P1_SH: diy-part1.sh
  DIY_P2_SH: diy-part2.sh
  UPLOAD_BIN_DIR: false
  UPLOAD_FIRMWARE: true
  UPLOAD_COWTRANSFER: false
  UPLOAD_WETRANSFER: false
  UPLOAD_RELEASE: false
  TZ: Asia/Shanghai
```

> **TIPS:** 修改时需要注意`:`(冒号)后面有空格。

| 环境变量 | 功能 |
| --- | --- |
| `REPO_URL` | 源码仓库地址 |
| `REPO_BRANCH` | 源码分支 |
| `FEEDS_CONF` | 自定义`feeds.conf.default`文件名 |
| `CONFIG_FILE` | 自定义`.config`文件名 |
| `DIY_P1_SH` | 自定义`diy-part1.sh`文件名 |
| `DIY_P2_SH` | 自定义`diy-part2.sh`文件名 |
| `UPLOAD_BIN_DIR` | 上传 bin 目录。即包含所有 ipk 文件和固件的目录。默认`false` |
| `UPLOAD_FIRMWARE` | 上传固件目录。默认`true` |
| `UPLOAD_COWTRANSFER` | 上传固件到奶牛快传。默认`false` |
| `UPLOAD_WERANSFER` | 上传固件到 WeTransfer 。默认`false` |
| `UPLOAD_RELEASE` | 上传固件到 releases 。默认`false` |
| `TZ` | 时区设置 |

### DIY 脚本

仓库根目录目前有两个 DIY 脚本：`diy-part1.sh` 和 `diy-part2.sh`，它们分别在更新与安装 feeds 的前后执行，你可以把对源码修改的指令写到脚本中，比如修改默认 IP、主机名、主题、添加 / 删除软件包等操作。但不仅限于这些操作，发挥你强大的想象力，可做出更强大的功能。

> **TIPS:** 脚本工作目录在源码目录，内附几个简单的例子供参考。

### 添加额外的软件包

* 在 DIY 脚本中加入对指定软件包源码的远程仓库的克隆指令。就像下面这样：

```none
git clone https://github.com/P3TERX/xxx package/xxx
```

* 本地`make menuconfig`生成`.config`文件时添加相应的软件包，如果你知道包名可以直接写到`.config`文件中。

> **TIPS:** 如果额外添加的软件包与 OpenWrt 源码中已有的软件包同名的情况，则需要把 OpenWrt 源码中的同名软件包删除，否则会优先编译 OpenWrt 中的软件包。这同样可以利用到的 DIY 脚本，相关指令应写在`diy-part2.sh`。

原理是把软件包源码放到 `package` 目录下，编译时会自动遍历，与本地编译是一样的。当然方法不止一种，其它方式请自行探索。

### 自定义 feeds 配置文件

把 `feeds.conf.default` 文件放入仓库根目录即可，它会覆盖 OpenWrt 源码目录下的相关文件。

### Custom files（自定义文件）

俗称 “files 大法”，在仓库根目录下新建 `files` 目录，把相关文件放入即可。有关详情请自行搜索了解。

### 定时自动编译

编辑 workflow 文件（`.github/workflows/build-openwrt.yml`）取消注释下面两行。

```none
#  schedule:
#    - cron: 0 8 * * 5
```

例子是北京时间每周五下午 4 点（16 时）开始编译（周末下班回家直接下载最新固件开始折腾）。如需自定义则按照 cron 格式修改即可，GitHub Actions 的时区为 UTC ，注意按照自己所在地时区进行转换。

### 追踪源码更新

### 自定义源码

默认引用的是 Lean 的源码，如果你有编译其它源码的需求可以进行替换。

编辑 workflow 文件（`.github/workflows/build-openwrt.yml`），修改下面的相关环境变量字段。

```none
REPO_URL: https://github.com/coolsnowwolf/lede
REPO_BRANCH: master
```

比如修改为 OpenWrt 官方源码 19.07 分支

```none
REPO_URL: https://github.com/openwrt/openwrt
REPO_BRANCH: openwrt-19.07
```

> **TIPS:** 注意冒号后面有空格

### 编译多个固件

#### 多 repository 方案

通过 [P3TERX/Actions-OpenWrt](https://github.com/P3TERX/Actions-OpenWrt) 项目[创建](https://github.com/P3TERX/Actions-OpenWrt/generate)多个仓库来编译不同架构机型的 OpenWrt 固件。

#### 多 workflow 方案

基于 GitHub Actions 可同时运行多个工作流程的特性，最多可以同时进行至少 20 个编译任务。也可以单独选择其中一个进行编译，这充分的利用到了 GitHub Actions 为每个账户免费提供的 20 个 Ubuntu 虚拟服务器环境。

点击查看

假设有三台路由器的固件需要编译，比如 K2P、x86_64 软路由、新路由3。

* 生成它们的`.config`文件
* 分别将它们重命名为`k2p.config`、`x64.config`、`d2.config`放入本地仓库根目录。
* 复制多个 workflow 文件（`.github/workflows/build-openwrt.yml`）。为了更好的区分可以对它进行重命名，比如`k2p.yml`、`x64.yml`、`d2.yml`。此外第一行`name`字段也可以进行相应的修改。
* 然后分别用上面修改的文件名替换对应 workflow 文件中下面两个位置的`.config`，不同的机型同样可以使用不同的 DIY 脚本。

```none
...
    paths:
      - '.config'
...
        CONFIG_FILE: '.config'
        DIY_SH: 'diy.sh'
...
```

### SSH 连接到 Actions

通过 tmate 连接到 GitHub Actions 虚拟服务器环境，可直接进行 `make menuconfig` 操作生成编译配置，或者任意的客制化操作。也就是说，你不需要再自己搭建编译环境了。这可能改变之前所有使用 GitHub Actions 的编译 OpenWrt 方式。

点击查看

* 在`Run Workflow`时把`SSH connection to Actions`的值改为`true`（或者也可以不修改，而是通过 [webhook 方式](https://p3terx.com/archives/github-actions-manual-trigger.html#toc_2)发送带有`ssh`触发关键词的请求。）
* 在触发工作流程后，在 Actions 日志页面等待执行到`SSH connection to Actions`步骤，会出现类似下面的信息：

```none
To connect to this session copy-n-paste the following into a terminal or browser:

ssh Y26QeagDtsPXp2mT6me5cnMRd@nyc1.tmate.io

https://tmate.io/t/Y26QeagDtsPXp2mT6me5cnMRd
```

* 复制 SSH 连接命令粘贴到终端内执行，或者复制链接在浏览器中打开使用网页终端。（网页终端可能会遇到黑屏的情况，按 `Ctrl`+`C` 即可）
* `cd openwrt && make menuconfig`
* 完成后按`Ctrl`+`D`组合键或执行`exit`命令退出，后续编译工作将自动进行。

> **TIPS:** 固件目录下有个`config.seed`文件，如果你需要再次编译可以使用它。

### 上传固件到奶牛快传

[奶牛快传](https://cowtransfer.com/)是中国大陆的一款临时文件传输分享服务网盘，特点是不限速。因国情所致，中国大陆地区 GitHub 访问速度缓慢，有些小伙伴可能无法正常下载固件，上传固件到奶牛快传是个非常好的选择。

点击查看

* 编辑 workflow 文件（`.github/workflows/build-openwrt.yml`），将环境变量`UPLOAD_COWTRANSFER`的值修改为`true`：

```none
UPLOAD_COWTRANSFER: true
```

* 编译完成后你可以在相关的 workflow 页面或者`Upload firmware to cowtransfer`步骤的日志中找到下载链接。

> CLI 上传工具来自 [Mikubill/transfer](https://github.com/Mikubill/transfer) ，特此感谢。

### 上传固件到 WeTransfer

[WeTransfer](https://wetransfer.com/) 是荷兰的一款临时文件传输分享服务网盘，前面提到的奶牛快传实际上师从自它，二者的网站都非常相似。WeTransfer 使用的是 Amazon S3 存储并通过 Amazon CloudFront CDN 全球加速，它在中国大陆的下载体验完全不输奶牛快传，甚至某些情况下要更好。

* 编辑 workflow 文件（`.github/workflows/build-openwrt.yml`），将环境变量`UPLOAD_WERANSFER`的值修改为`true`：

```none
UPLOAD_WERANSFER: true
```

* 编译完成后你可以在相关的 workflow 页面或者`Upload firmware to WeTransfer`步骤的日志中找到下载链接。

> CLI 上传工具来自 [Mikubill/transfer](https://github.com/Mikubill/transfer) ，特此感谢。

### 上传固件到 Releases 页面

GitHub 的 Releases 页面通常用于发布打包好的二进制文件，无需登录即可下载。Artifacts 和网盘有保存期限，Releases 则是永久保存的。

编辑 workflow 文件（`.github/workflows/build-openwrt.yml`），将环境变量 `UPLOAD_WERANSFER` 的值修改为 `true`：

```none
UPLOAD_RELEASE: true
```

编译完成后你可以在 releases 页面找到下载链接。

> **TIPS:** 为了不给 GitHub 服务器带来负担，默认保留 3 个历史记录。

## 尾巴

感谢 Microsoft 为我们提供 GitHub Actions 这样强大的工具。希望大家合理使用免费的资源，必要时再编译，过度占用资源虽然使用者不会得到任何实质性的惩罚，但会为中国抹黑，造成很多国际争端。只有让开发者来充分利用才能产生更多更好的软件，这样大家才能受益。
