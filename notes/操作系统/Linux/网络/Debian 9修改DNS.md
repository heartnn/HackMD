---
tags: debian, dns
via: https://www.jianshu.com/p/2756d587ce1e
---

# Debian 9修改DNS

本文以127.0.0.1为例，可根据实际需要改成自己的DNS，如8.8.8.8, 114.114.114.114, etc.

首先用以下命令查看当前的DNS：

```bash
systemd-resolve --status
```

## 方法一

直接修改`/etc/resolv.conf`。

```bash
cat <<EOF >/etc/resolv.conf
nameserver 127.0.0.1
EOF
```

如果系统默认开启了`systemd-resolved`服务，配置`/etc/systemd/resolved.conf`文件中的DNS。

```
DNS=127.0.0.1
```

多个DNS用空格隔开

也可以直接停掉`systemd-resolved`服务，然后修改`/etc/resolv.conf`

如果按照本方法修改，重启后恢复成系统默认，则参考以下两种方法。

## 方法二

`/etc/network/interfaces`追加一行，如果有多个dns用空格隔开。

```bash
cat <<EOF >>/etc/network/interfaces
dns-nameservers 127.0.0.1
EOF
```

## 方法三

`/etc/dhcp/dhclient.conf`文件中，移除request配置项的domain-name、domain-name-servers、domain-search，文件末尾追加一行，注意要带上分号(`;`)。

```bash
supersede domain-name-servers 127.0.0.1;
```

如果只是想改为localhost，只要取消以下行的注解(#)即可。

```bash
#prepend domain-name-servers 127.0.0.1;
```

## 方法四

使用 DHCP 钩子，推荐使用这种方法。

修改`/etc/dhcp/dhclient-enter-hooks.d/nodnsupdate`文件。

```bash
cat <<EOF >/etc/dhcp/dhclient-enter-hooks.d/nodnsupdate
#!/bin/sh
make_resolv_conf(){
    :
}
EOF
```

给文件`nodnsupdate`添加可执行权限

```bash
chmod +x /etc/dhcp/dhclient-enter-hooks.d/nodnsupdate
```

重启系统后修改`/etc/resolv.conf`
