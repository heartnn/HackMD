---
author: hwrenx
tags: ubuntu, kernel
via: https://www.jianshu.com/p/f284bc90944f
---

# Ubuntu删除多余内核

## 第一步：查看当前内核

```bash
rew $ uname -a
Linux rew 4.15.0-42-generic #45~16.04.1-Ubuntu SMP Mon Nov 19 13:02:27 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

当前使用版本为：`4.15.0-42-generic`

## 第二步：查看所有内核

```bash
rew $ dpkg --get-selections | grep linux
console-setup-linux                     install
libselinux1:amd64                       install
libselinux1:i386                        install
linux-base                              install
linux-firmware                          install
linux-generic-hwe-16.04                 install
linux-headers-4.15.0-42                 install
linux-headers-4.15.0-39                 install
linux-headers-4.15.0-39-generic         install
linux-headers-4.15.0-42-generic         install
linux-headers-generic-hwe-16.04         install
linux-image-4.15.0-39-generic           install
linux-image-4.15.0-42-generic           install
linux-image-generic-hwe-16.04           install
linux-libc-dev:amd64                    install
linux-libc-dev:i386                     install
linux-modules-4.15.0-39-generic         install
linux-modules-4.15.0-42-generic         install
linux-modules-extra-4.15.0-39-generic   install
linux-modules-extra-4.15.0-42-generic   install
linux-sound-base                        install
pptp-linux                              install
syslinux                                install
syslinux-common                         install
syslinux-legacy                         install
util-linux                              install
```

## 第三步：移除冗余内核

所有39版本的对我来说都是多余的，进行删除：

```bash
rew $ sudo apt-get remove \
linux-headers-4.15.0-39 \
linux-headers-4.15.0-39-generic \
linux-image-4.15.0-39-generic \
linux-modules-4.15.0-39-generic \
linux-modules-extra-4.15.0-39-generic
```

卸载后重新检查：

```bash
rew $ dpkg --get-selections | grep linux
console-setup-linux                     install
libselinux1:amd64                       install
libselinux1:i386                        install
linux-base                              install
linux-firmware                          install
linux-generic-hwe-16.04                 install
linux-headers-4.15.0-42                 install
linux-headers-4.15.0-42-generic         install
linux-headers-generic-hwe-16.04         install
linux-image-4.15.0-39-generic           deinstall
linux-image-4.15.0-42-generic           install
linux-image-generic-hwe-16.04           install
linux-libc-dev:amd64                    install
linux-libc-dev:i386                     install
linux-modules-4.15.0-39-generic         deinstall
linux-modules-4.15.0-42-generic         install
linux-modules-extra-4.15.0-39-generic   deinstall
linux-modules-extra-4.15.0-42-generic   install
linux-sound-base                        install
pptp-linux                              install
syslinux                                install
syslinux-common                         install
syslinux-legacy                         install
util-linux                              install
```

状态为deinstall即已经卸载，如果觉得看着不舒服的话可以使用purge连配置文件里一起彻底删除，清理内核列表：

```bash
rew $ sudo apt-get purge \
linux-headers-4.15.0-39 \
linux-headers-4.15.0-39-generic \
linux-image-4.15.0-39-generic \
linux-modules-4.15.0-39-generic \
linux-modules-extra-4.15.0-39-generic
```

## 第四步：更新系统引导

删除内核后需要更新grub移除失效的启动项

    rew $ sudo update-grub  #根据情况选择grub/grub2

