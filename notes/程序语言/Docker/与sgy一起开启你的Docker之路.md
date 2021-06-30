# 与sgy一起开启你的Docker之路

## 写在前面

- 这篇博客适合谁？
    - 对于Docker并不了解，只是有一点模糊的感觉，觉得Docker可以当成虚拟机用之类的
    - 只是下载了Docker软件，对于怎么配置，怎么玩，第一步干什么，完全一无所知
    - 本文适用于mac，PC用户不保证文章的效果，现在离开来得及
- 网上Docker相关的教程都泛滥了，为啥还要写，为啥我要看你的文章
    - 首先欢迎你，能搜索到我的博客就是缘分
    - 其次，确实，现在Docker相关的文章真的太多了，那我为什么还要写呢？原因有三：
        - 其一，文章是很多，各种1小时入门什么的，相信你已经在知乎看过很多了，我也看过，因为我就是从知乎那几篇教程为起点，自己抠出来的，现在把自己的心得和经验贴出来，我觉得既是对自己负责也能服务他人。
        - 其二，我从对Docker只有及其模糊（真的是模糊，我除了知道Docker是跟虚拟系统有关其他啥都不知道），到现在起码一问一不知（对应一问三不知）的状态，大概花了8个小时。这期间全靠我从网络上搜索到的资料。现在用我的话写出来，我觉得对于跟我相同开始情况的小白用户，能省不少时间，所以我要写。
        - 其三，因为我自己是纯靠搜索到的教程和技术文章学习的Docker，我自然是懂得干涩的语言对学习的拖慢。所以我自己写的时候，自然会考虑到这个问题。本文尽量不用技术用语，尽量使用通俗易懂的文字，为进一步拉低Docker的入门门槛作出我自己的贡献。
- 这篇文章主要是哪方面的Docker文章，纯入门？你做了哪些工作？
    - 很高兴你问了我这个问题。本文主要：
        - 首先对于Docker的概念作出我的解释，尽量通俗易懂
        - 接着针对我用Docker的目的，从最开始的软件下载，到最后push一个自己的镜像到hub，整个过程以白话的形式描述出来，降低入门门槛，节省你的学习时间
    - 我做了哪些工作？这是个好问题！
        - 首先，我一直都很好奇Docker这个东西，但从来没有机会去使用它。但是，这学期OS课的lab，纯Linux系统编程，虽然使用双系统或者虚拟机都是纯小学生的难易程度了，也不麻烦，但我想，为什么不用Docker呢？用双系统或者虚拟机也太过小儿科！所以，我就这样接触了Docker
        - 那么我使用Docker实现了什么？在lab中，我们需要用到的是纯终端下的gcc工具链还有vim等，那么这就是我需要的全部软件了！于是我在Docker中实现了「gcc+gdb+vim」环境，并push到了hub中。
- 你这篇博客有没有抄别人的？
    - 额，我学习Docker时确实看了很多很多（真的是很多）文章，现在写这篇博客，除了自己的经验心得，自然还得去看那些文章。但我能保证的是，我写的都是取精华去糟粕，不然这篇文章就不必存在了你说是不是？
    - 参考资料会在文后统一附上，谢谢！

好了，闲话说完，我们开始吧！

## 入门Docker，你要下载什么？注册什么？

### Docker.app你肯定是要下载的！

Docker for mac，这个你肯定要下载：[点我下载Docker for mac的社区免费版本](https://www.docker.com/community-edition)

### 注册Docker官方账号，你需要它！

注册一个Docker的官方账号，有利而无害，相信我！[点我注册Docker Cloud官方账号](https://cloud.docker.com/swarm/sgyzetrov/dashboard/onboarding/cloud-registry)

Docker安装好，账号也注册后，点击桌面顶栏的Docker图标，点击sign in，登陆你的Docker账号。

### 注册DaoCloud账号，获取加速服务！

不可否认，有时直接从Docker官方往本地pull镜像会十分缓慢。。。这时我们可以通过国内的Docker服务提供商免费获取加速pull镜像服务，阿里网易好像都有这种服务，我选择的是DaoCloud：[点我注册DaoCloud账号](https://account.daocloud.io/signup)

注册后登陆DaoCloud，找到这个按钮：

![](vx_images/20200208150749963_5746.png)

接着，按照它的步骤，为自己添加Docker加速服务

![](vx_images/20200208151756369_19103.png)

### 到此准备工作结束，进入下一部分！

## 要想入门Docker，首先你需要理解Docker！

我也不扯淡了，直接上我对Docker的理解：

Docker，可以说是一个终端命令行的虚拟机，但更准确的说法，其实应该是一个虚拟环境。比如，你想要在PC上无缝使用Linux么？那么虚拟机并不是你唯一的出路，你还有Docker！我更愿意称Docker为一个容器，当然这只是Docker的一个狭义解释，Docker不止是一个容器。Docker包含3个重要概念：

- 一个，是镜像（Image），镜像是静态的、可以被用户互相分享的文件。我们玩过双系统和虚拟机的人都知道，首先你需要一个.iso镜像，才能安装系统。Docker中的镜像也是这个东西，镜像是静态的，你不能对他操作，只能pull别人的镜像或者push自己的镜像。
- 还有一个，是容器（Container），前面说过，镜像是静态不可操作的，只能被分享和下载，那什么是能被操作的呢？就是容器里！容器可以理解为镜像的动态状态，也就是我们虚拟机中系统装好后的状态，其实这么说是不对的，容器最符合的描述应该是Linux的iso文件的`Live CD`模式，比如我们玩双系统时都进入过`Live CD`模式，不安装系统而直接进入系统，很神奇是吧，Docker的容器就是这个概念，只不过更加轻量更加迅速便捷。但是`Live CD`的害处就是你关机后作出的修改安装的软件全部gg，容器也是一样，一旦被直接推出，之前安装的gcc啊vim啊啥的就会全部gg掉。如果要保存修改，就需要将当前容器封装成一个新的镜像，这样下次启动这个新的镜像后之前作出的修改还都在。
- 最后，是仓库（Repository）。各位在前面看到我写的pull和push什么的，有没有晕？不知道各位对于git熟悉不熟悉，Docker中的仓库很像git的代码仓库，你可以pull自己之前push到自己仓库的镜像到本地，也可以pull别人push到公共仓库的镜像到自己本地。说白了就是百度云盘，你可以上传（push）自己做好环境的Docker上去，也可以下载（pull）自己云端的镜像到本地。同时，我们知道百度云最大的特点就是分享（你懂的嘿嘿嘿），类比Docker，如果你得到百度云分享链接（别人的镜像名字、标签和别人的用户名），你还可以下载（pull）别人分享的镜像到自己的本地，别人也可以下载（pull）你的镜像，因为Docker仓库都是公共的。当然，每个免费用户有一个名额把自己的一个镜像设为私有，也就是禁止被分享给别人，类比百度云上你自己保存的而没有被生成分享链接的小姐姐。

接下来来张高大上的概念图，各位看个热闹2333

![](vx_images/20200203194017828_3140.png)

## 接下来就是实战了！打开你的iTerm2！（我是iTerm2党2333，打开普通terminal也行，这个不影响）

### 查看Docker版本信息

终端输入：

    docker version

显示的我的版本信息

```shell
~$ docker version
Client:
 Version:      17.06.2-ce
 API version:  1.30
 Go version:   go1.8.3
 Git commit:   cec0b72
 Built:        Tue Sep  5 20:12:06 2017
 OS/Arch:      darwin/amd64
 ...
```

不过我一般不需要那么多信息，所以一直用的是`docker -v`命令

### 是时候pull你的第一个镜像下来的！

Docker安装好后是不会自带镜像的，你需要从仓库自己pull一个镜像下来，自己制作自己的镜像也是一个道理，你可以通过在已有的镜像基础上生成自己的镜像或者看一下这篇博客: [随便百度的如何创建自己Docker镜像得到的教程](http://blog.csdn.net/shiqiangdexin/article/details/52472195)

Docker镜像官方好像只提供Linux，这个很容易想，windows和mac是要交钱的吧大概？

搜索ubuntu的Docker镜像`docker search ubuntu`，这个如果你想要什么centos，直接改就行，不区分写法,我的返回结果：

```shell
~$ docker search ubuntu
NAME                               DESCRIPTION                                     STARS   OFFICIAL   AUTOMATED
ubuntu                             Ubuntu is a Debian-based Linux operating s...   6636                [OK]
dorowu/ubuntu-desktop-lxde-vnc     Ubuntu with openssh-server and NoVNC            131                 [OK]
rastasheep/ubuntu-sshd             Dockerized SSH service, built on top of of...   105                 [OK]
ansible/ubuntu14.04-ansible        Ubuntu 14.04 LTS with ansible                   86                  [OK]
ubuntu-upstart                     Upstart is an event-based replacement for ...   80                  [OK]
neurodebian                        NeuroDebian provides neuroscience research...   40                  [OK]
ubuntu-debootstrap                 debootstrap --variant=minbase --components...   31                  [OK]
...
```

拉取官方最新版的ubuntu镜像：`docker pull ubuntu:latest`，其中的latest是一个标签（tag），表示是当前最新版本。你应该得到的信息，类似这样的

```shell
~$ docker pull ubuntu:latest
Trying to pull repository docker.io/library/ubuntu ...
latest: Pulling from docker.io/library/ubuntu
aed158d74952: Pull complete
773ae8273d14: Pull complete
d1d487w88782: Pull complete
cd3d6cd6c0cf: Pull complete
8d73bu79120c: Pull complete
Digest: sha256:35bc48a1ca97c3f74rhf378hj92hd82j29i4hf4hf84nf0dhnsid232de8d8
Status: Downloaded newer image for docker.io/ubuntu:latest
```

你输入的命令实际上相当于`docker pull registry.hub.docker.com/ubuntu:latest`命令，即从注册服务器`registry.hub.docker.com`中的名为`ubuntu`的仓库中下载标签为`latest`的镜像。

由于我的lab统一要求，ubuntu需要14.04版本，所以我在hub里面搜了搜，找到了一个用户分享的14.04 64位纯净镜像（base image），下面将他的镜像扒下来

    ~$ docker pull chug/ubuntu14.04x64

这个用户还有很多其他版本的ubuntu系统，12 13 14的32位64位都有，全是纯净镜像。

### 查看你本地的镜像仓库！

把初始镜像拉下来后，就可以启动它了，不过，可以先使用`docker images`命令查看你自己的本地镜像，我随便找了个例子，你的也应该是类似这样的：

```
~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu    16.04               e4415b714b62        11 days ago         128.1 MB
docker.io/ubuntu    latest              e4415b714b62        11 days ago         128.1 MB
docker.io/ubuntu    12.04               aefa163f7a7e        11 days ago         103.5 MB
docker.io/centos    latest              0584b3d2cf6d        3 weeks ago         196.5 MB
```

从网上一个教程中找到以下说法，比我说的好，那就看这个吧！

> 在列出信息中，可以看到几个字段信息:
>
> - 来自于哪个仓库，比如 ubuntu
> - 镜像的标记，比如 16.04
> - 它的 ID 号(唯一)，比如e4415b714b62
> - 创建时间
> - 镜像大小
>
> 其中镜像的 ID 唯一标识了镜像，注意到 ubuntu:16.04 和 ubuntu:latest具有相同的镜像 ID ，说明它们实际上是同一镜像。 TAG 信息用来标记来自同一个仓库的不同镜像。例如 ubuntu 仓库中有多个镜像，通过 TAG 信息来区分发行版本，例如10.04 、 12.04 、 12.10 、 13.04 、 14.04 等。例如可以使用`docker run -t -i ubuntu:16.04 /bin/bash`命令指定使用镜像`ubuntu:16.04`来启动一个容器。如果不指定具体的标记，则默认使用`latest`标记信息。

### 启动你的镜像并尽情安装软件吧！

以下内容均以我自己pull下来的chug的初始镜像为例：

现在你已经有一个初始的镜像了，注意这个里面是什么都没有的，连vim都没装，是精简到不能再精简的镜像了。

首先启动它：

```shell
~$ docker run -it chug/ubuntu14.04x64 /bin/bash
root@aa97ba3292ce:/#
```

`-it` 表示运行在交互模式，是`-i -t`的缩写，即`-it`是两个参数：`-i`和`-t`。前者表示打开并保持stdout，后者表示分配一个终端（pseudo-tty）一般这个模式就是可以启动bash，然后和容器有命令行的交互

可以看到我们终端的字头变成`root@aa97ba3292ce:/#`了，这就意味着我们的镜像已经创建了一个容器实例。现在我们可以对这个“ubuntu系统”进行操作了

比如安装vim：

```bash
root@aa97ba3292ce:/# apt-get install vim
Reading package lists... Done
Building dependency tree... Done
The following extra packages will be installed:
  file libexpat1 libffi6 libgpm2 libmagic1 libpython2.7 libpython2.7-minimal libpython2.7-stdlib libsqlite3-0 libssl1.0.0 mime-support vim-common vim-runtime
Suggested packages:
  gpm ctags vim-doc vim-scripts
The following NEW packages will be installed:
  file libexpat1 libffi6 libgpm2 libmagic1 libpython2.7 libpython2.7-minimal libpython2.7-stdlib libsqlite3-0 libssl1.0.0 mime-support vim vim-common vim-runtime
0 upgraded, 14 newly installed, 0 to remove and 0 not upgraded.
Need to get 10.7 MB of archives.
After this operation, 50.7 MB of additional disk space will be used.
Do you want to continue? [Y/n]
```

看到我没有用sudo，是因为本来就已经是超级用户（root）状态了。

同理按照我自己的需求，我安装了gcc和gdb

### 想退出容器？很简单！

如果使用exit，命令退出，则容器的状态处于Exit，而不是后台运行。如果想让容器一直运行，而不是停止，可以使用快捷键 ctrl+p ctrl+q 退出，此时容器的状态为Up。

查看当前正在运行的容器：

```shell
~$ docker ps
CONTAINER ID     IMAGE        COMMAND      CREATED       STATUS       PORTS        NAMES
aa97ba3292ce  chug/ubuntu14.04x64   "/bin/bash"    7 minutes ago  Up 7 minutes   relaxed_hoover
```

看到当前有一个ID为aa97ba3292ce的容器。

启动、停止、重启容器aa97ba3292ce的命令：

```shell
~$ docker start aa97ba3292ce
~$ docker stop aa97ba3292ce
~$ docker restart aa97ba3292ce
```

后台启动一个容器后，如果想进入到这个容器，可以使用attach命令：

    ~$ docker attach aa97ba3292ce

### 软件装完，想保存环境？commit帮你！

将容器转化为一个镜像，即执行commit操作，完成后可使用`docker images`查看

```shell
root@aa97ba3292ce:/#exit //先退出容器
~$ docker commit -m "ubuntu with vim" -a "sgy" aa97ba3292ce sgy/ubuntu:vim
~$ docker images
REPOSITORY                    TAG    IMAGE ID         CREATED             SIZE
sgy/ubuntu                    vim    52166e4475ed     5 seconds ago       358.1 MB
chug/ubuntu14.04x64           latest 0584b3d2cf6d     9 days ago          196.5 MB
```

其中，`-m`指定说明信息；`-a`指定用户信息；`aa97ba3292ce`代表容器的`id`；`sgy/ubuntu:vim`指定目标镜像的用户名、仓库名和 `tag` 信息。我这里都是为了博客瞎编的用户名，我自己的用户名也不是sgy，你运行命令的时候使用自己注册Docker时的用户名。

此时Docker中就有了我们新建的镜像`sgy/ubuntu:vim`，此镜像和原有的ubuntu镜像区别在于多了个vim工具。此时我们利用新镜像创建的容器，本身就自带vim了。

启动新创建的镜像，可以看到vim已经自带了。

```shell
~$ docker run -it sgy/ubuntu:vim /bin/bash
root@520afc596c51:/# vim --version
VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Apr  4 2017 18:14:54)
...
```

利用exit退出容器。此时Docker引擎中就有了两个容器，可使用`docker ps -a`查看。

### 想要删除容器或者镜像？用这个！

如果想删除容器或者镜像，可以使用rm命令，注意：删除镜像前必须先删除以此镜像为基础的容器（哪怕是已经停止的容器），否则无法删除该镜像，会报错`Failed to remove image (e4415b714b62): Error response from daemon: conflict: unable to delete e4415b714b62 (cannot be forced) - image has dependent child images`类似这种。

```shell
~$ docker rm container_id
~$ docker rmi image_id
```

有的时候尽管删除了全部容器，镜像还是无法删除，这时点击mac顶栏中的docker logo，选择restart，然后再试一次rmi，应该就没问题了。

### 附上一张高大上的Docker命令图

![](vx_images/20200203194050992_13818.png)

### 一次配置，到处使用？那就push到hub上吧！

因为之前已经在Docker.app中登陆了Docker账号，所以现在直接

    ~$ docker push sgy/ubuntu:vim

就可以了！

下次到了机房，在ubuntu系统中安装Docker，配置好加速器，启动Docker，`docker login`登陆自己的账号，然后直接`~$ docker pull sgy/ubuntu:vim`就可以把你push到hub的已经配置好的环境的镜像给扒下来。做完实验，再push上去就ok了！

## Docker中安装gcc、gdb时遇到的问题

### add-apt-repository: command not found

14.04系统解决办法：

    apt-get install software-properties-common

older版本的系统：

    apt-get install python-software-properties

### Docker中使用gdb无法进入断点，无法调试

加上`--privileged`参数

    ~$ docker run -it --privileged sgy/ubuntu:vim /bin/bash

## 结语

到此本文就结束了，我说得很清楚，这只是一篇入门教程，可以让你从“一问三不知”到达“一问一不知”的状态，再高层次？那就靠自己了！

## References

1. https://zhuanlan.zhihu.com/p/23599229
2. https://zhuanlan.zhihu.com/p/22259092
3. http://cpper.info/2016/12/03/docker-image-introduction.html
4. http://blog.csdn.net/ture_dream/article/details/52651746>
5. http://www.leesven.com/2254.html
6. https://askubuntu.com/questions/778318/how-do-i-install-gcc-6-latest-6-1-on-ubuntu-14-04-lts-make-c14-the-default
7. http://blog.qiniu.com/archives/805
8. http://blog.csdn.net/ziren235/article/details/78064936?locationNum=5&fps=1
9. https://segmentfault.com/a/1190000002766882
10. http://blog.csdn.net/jiang_xinxing/article/details/58025417
11. http://blog.csdn.net/yyw794/article/details/78048372
12. http://p.primeton.com/articles/549ad953e13823790300009d

---
via: https://blog.csdn.net/S_gy_Zetrov/article/details/78161154
