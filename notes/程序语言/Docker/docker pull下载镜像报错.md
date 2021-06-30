# docker pull下载镜像报错

使用docker pull从镜像仓库拉取镜像时报错如下：

```bash
[root@docker-registry ~]# docker pull centos
Using default tag: latest
Trying to pull repository docker.io/library/centos ...
Get https://registry-1.docker.io/v2/library/centos/manifests/latest: Get https://auth.docker.io/token?scope=repository%3Alibrary%2Fcentos%3Apull&service=registry.docker.io: net/http: TLS handshake timeout
```

解决办法：

```bash
[root@docker-registry ~]# yum install bind-utils                          // 安装dig工具
[root@docker-registry ~]# dig @114.114.114.114 registry-1.docker.io

; <<>> DiG 9.9.4-RedHat-9.9.4-61.el7_5.1 <<>> @114.114.114.114 registry-1.docker.io
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51198
;; flags: qr rd ra; QUERY: 1, ANSWER: 8, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;registry-1.docker.io.          IN      A

;; ANSWER SECTION:
registry-1.docker.io.   31      IN      A       34.206.236.31
registry-1.docker.io.   31      IN      A       54.175.43.85
registry-1.docker.io.   31      IN      A       34.205.207.96
registry-1.docker.io.   31      IN      A       34.233.151.211
registry-1.docker.io.   31      IN      A       52.70.175.131
registry-1.docker.io.   31      IN      A       34.200.28.105
registry-1.docker.io.   31      IN      A       54.165.149.19
registry-1.docker.io.   31      IN      A       34.201.236.93

;; Query time: 10 msec
;; SERVER: 114.114.114.114#53(114.114.114.114)
;; WHEN: Wed Oct 31 11:18:55 CST 2018
;; MSG SIZE  rcvd: 177
```

选择上面命令执行结果中的一组解析放到本机的/etc/hosts文件里做映射

```bash
[root@docker-registry ~]# vim /etc/hosts
54.175.43.85    registry-1.docker.io
```

接着再进行docker pull镜像下载操作

```bash
[root@docker-registry ~]# docker pull centos
Using default tag: latest
Trying to pull repository docker.io/library/centos ...
latest: Pulling from docker.io/library/centos
aeb7866da422: Pull complete
Digest: sha256:67dad89757a55bfdfabec8abd0e22f8c7c12a1856514726470228063ed86593b
Status: Downloaded newer image for docker.io/centos:latest

[root@docker-registry ~]# docker images
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
docker.io/centos     latest              75835a67d134        3 weeks ago         200 MB
```

---
via: https://www.cnblogs.com/kevingrace/category/839227.html