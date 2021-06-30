# 通过容器提交镜像（docker commit）以及推送镜像（docker push）笔记

在本地创建一个容器后，可以依据这个容器创建本地镜像，并可把这个镜像推送到Docker hub中，以便在网络上下载使用。

## 查看镜像

```bash
[root@docker-test1 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu    16.04               7aa3602ab41e        5 weeks ago         115 MB
```

## 创建一个名为myubuntu的容器

```bash
[root@docker-test1 ~]# docker run -ti --name myubuntu -d docker.io/ubuntu
[root@docker-test1 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
651a8541a47d        docker.io/ubuntu    "/bin/bash"         37 seconds ago      Up 36 seconds                           myubuntu
```

docker commit :从容器创建一个新的镜像。

    # docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

- -a: 提交的镜像作者；
- -c: 使用Dockerfile指令来创建镜像；
- -m: 提交时的说明文字；
- -p: 在commit时，将容器暂停。

## 根据这个myubuntu容器提交镜像

```bash
[root@docker-test1 ~]# docker commit -a "wangshibo" -m "this is test" 651a8541a47d myubuntu:v1
sha256:6ce4aedd12cda693d0bbb857cc443a56f9f569106e09ec61aa53563d0932ea4d
```

再次查看镜像,发现镜像myubuntu:v1已经提交到本地了

```bash
[root@docker-test1 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
myubuntu            v1                  6ce4aedd12cd        59 seconds ago      84.1 MB
docker.io/ubuntu    16.04               7aa3602ab41e        5 weeks ago         115 MB
```

## 在mybuntu:v1镜像推送到docker hub仓库中去

    # docker push [OPTIONS] NAME[:TAG]

OPTIONS说明：

--disable-content-trust :忽略镜像的校验,默认开启

首先是登录docker hub （用户名：wangshibo    密码:\*\*\*\*\*\*\*）

```bash
[root@docker-test1 ~]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username (wangshibo): wangshibo
Password:
Login Succeeded

[root@docker-test1 ~]# docker push wangshibo/myubuntu:v1
The push refers to a repository [docker.io/wangshibo/myubuntu]
An image does not exist locally with the tag: docker.io/wangshibo/myubuntu
```

这里需要将ubuntu:v1镜像改名，在名称前加上自己的docker hub的Docker ID，即wangshibo

```bash
[root@docker-test1 ~]# docker tag 6ce4aedd12cd wangshibo/myubuntu:v1
[root@docker-test1 ~]# docker images
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
myubuntu             v1                  6ce4aedd12cd        6 minutes ago       84.1 MB
wangshibo/myubuntu   v1                  6ce4aedd12cd        6 minutes ago       84.1 MB
docker.io/ubuntu     16.04               7aa3602ab41e        5 weeks ago         115 MB
```

再次进行推送（注意：下面的v1的tag标签可以不打，默认是latest）。推送操作时间稍微会有一点长，耐心等待～

```bash
[root@docker-test1 ~]# docker push wangshibo/myubuntu:v1
The push refers to a repository [docker.io/wangshibo/myubuntu]
b5948ba9486d: Pushed
8d7ea83e3c62: Mounted from library/ubuntu
6a061ee02432: Mounted from library/ubuntu
f73b2816c52a: Mounted from library/ubuntu
6267b420796f: Mounted from library/ubuntu
a30b835850bf: Mounted from library/ubuntu
v1: digest: sha256:e9cd9075d262848a307c92751e1a5890d883b814a31abd118161442461a1ca2d size: 1564
```

最后登录自己的Docker Hub，即<https://hub.docker.com/>

登录后，在Repositories里面就可以看到自己在上面推送的镜像wangshibo/myubuntu:v1了，这是个对外的镜像，可以在网络上下载。

在Docker hub上可以看到这个镜像的下载命令（注意下载时跟上tag标签，如果是latest的默认tag可以不跟）

也可以直接在Docker hub上删除这个镜像（Repositories-镜像-Settings-delete）

比如在另一台服务器上下载这个镜像

```bash
[root@kevin-test ~]# docker pull wangshibo/myubuntu
Pulling repository wangshibo/myubuntu
Repository not found
```

需要跟上tag标签

```bash
[root@kevin-test ~]# docker pull wangshibo/myubuntu:v1
v1: Pulling from wangshibo/myubuntu
68e2a091ef24: Pull complete
8f9dd35f6299: Pull complete
a81a6171600b: Pull complete
a211a2bc7010: Pull complete
9e71a0b4f83a: Pull complete
0cf75bb335aa: Pull complete
c393a882769e: Pull complete
Digest: sha256:845fa3dcc9d0de1b9c701e1009918995da35a29012015f6c297a05edc489e018
Status: Downloaded newer image for wangshibo/myubuntu:v1
[root@kevin-test ~]# docker  images
REPOSITORY           TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
wangshibo/myubuntu   v1                  c393a882769e        12 minutes ago      84.11 MB
```

## 在本机删除这个镜像

```bash
[root@docker-test1 ~]# docker images
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
myubuntu             v1                  6ce4aedd12cd        15 minutes ago      84.1 MB
wangshibo/myubuntu   v1                  6ce4aedd12cd        15 minutes ago      84.1 MB
docker.io/ubuntu     16.04               7aa3602ab41e        5 weeks ago         115 MB
```

注意上面有两个6ce4aedd12cd的镜像ID，此时直接使用这个镜像ID是删除不掉的

```bash
[root@docker-test1 ~]# docker rmi 6ce4aedd12cd
Error response from daemon: conflict: unable to delete 6ce4aedd12cd (must be forced) - image is referenced in multiple repositories
```

应该先删除docker tag改名前的镜像，使用镜像名称删除。（一般在docker tag镜像改名后，最好删除改名前的镜像）

```bash
[root@docker-test1 ~]# docker rmi myubuntu:v1
Untagged: myubuntu:v1
Untagged: wangshibo/myubuntu@sha256:e9cd9075d262848a307c92751e1a5890d883b814a31abd118161442461a1ca2d
 
[root@docker-test1 ~]# docker images
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
wangshibo/myubuntu   v1                  6ce4aedd12cd        15 minutes ago      84.1 MB
docker.io/ubuntu     16.04               7aa3602ab41e        5 weeks ago         115 MB
```

这个时候就可以删除镜像ID删除了

```bash
[root@docker-test1 ~]# docker rmi 6ce4aedd12cd
Untagged: wangshibo/myubuntu:v1
Deleted: sha256:6ce4aedd12cda693d0bbb857cc443a56f9f569106e09ec61aa53563d0932ea4d
Deleted: sha256:0ddeb6a16badd042914c2e72b9ef0331550c1cdcc4bdc6650a90cd9f57f1501b
[root@docker-test1 ~]# docker images         
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu    16.04               7aa3602ab41e        5 weeks ago         115 MB
```

### 删除镜像失败一例分享

```bash
[root@docker-test1 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@docker-test1 ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

打算删除掉docker的nginx镜像, 但是发现这个镜像就是删除不了，加了-f强制删除也不行，一直报Error: No such image的错误，最后终于找到了办法直接删除文件！

```bash
[root@docker-test1 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              62f816a209e6        7 days ago          109MB

[root@docker-test1 ~]# docker rmi nginx
Error: No such image: nginx

[root@docker-test1 ~]# docker rmi 62f816a209e6
Error: No such image: 62f816a209e6

[root@docker-test1 ~]# docker rmi 62f816a209e6 -f
Error: No such image: 62f816a209e6
```

如上,怎么都删除不掉nginx镜像！！！

#### 处理办法

```bash
[root@docker-test1 ~]# systemctl stop docker
[root@docker-test1 ~]# rm -rf /var/lib/docker
rm: cannot remove ‘/var/lib/docker/containers’: Device or resource busy
```

删除不了的原因是: 在建立容器的时候做了相应目录的挂载，没有卸载，所以Device or resource busy

解决办法: 查找挂载的目录并卸载

```bash
[root@docker-test1 ~]# cat /proc/mounts | grep "docker"
/dev/mapper/centos-root /var/lib/docker/containers xfs rw,relatime,attr2,inode64,noquota 0 0
proc /run/docker/netns/default proc rw,nosuid,nodev,noexec,relatime 0 0
proc /run/docker/netns/a0626c54fd03 proc rw,nosuid,nodev,noexec,relatime 0 0
proc /run/docker/netns/b18072de4224 proc rw,nosuid,nodev,noexec,relatime 0 0
proc /run/docker/netns/b5298f643455 proc rw,nosuid,nodev,noexec,relatime 0 0
proc /run/docker/netns/9f5e97637c98 proc rw,nosuid,nodev,noexec,relatime 0 0

[root@docker-test1 ~]# umount /var/lib/docker/containers
[root@docker-test1 ~]# rm -rf /var/lib/docker

[root@docker-test1 ~]# systemctl start docker
```

再次查看就没有这个镜像了

```bash
[root@docker-test1 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

---
via: https://www.cnblogs.com/kevingrace/p/9599988.html