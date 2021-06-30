---
tags: wine, font
via: http://www.91linux.com/html/article/go/20071120/8415.html
---

# wine字体设置

首先wine自己的系统中必须有中文字体，我的Ubuntu系统中已经安装了宋体，这也是Windows Vista之前的几个版本的系统使用的中文字体，所以：

```bash
cd ~/.wine/drive_c/windows/fonts
ln -s /usr/share/fonts/truetype/simsun.ttf simsun.ttc
```

当然，也可以将该字体复制到fonts目录，这里直接建立连接，省空间。

然后修改wine的注册表：

```bash
vi ~/.wine/system.reg
```

找到 `[System\CurrentControlSet\Hardware Profiles\Current\Software\Fonts]` ，将其中的 `"LogPixels"=dword:00000060` 改成 `"LogPixels"=dword:00000070` 。

再找到 `[Software\Microsoft\Windows NT\CurrentVersion\FontSubstitutes]` xxxx项，将其中的 `MS Shell Dlg` 相关的两项修改成如下内容（即更换字体为宋体）：

```reg
"MS Shell Dlg"="SimSun"
"MS Shell Dlg 2"="SimSun"
```

然后：

```bash
vi ~/.wine/drive_c/windows/win.ini
```

将 `Desktop` 块修改成如下内容（即增大字体）：

```ini
[Desktop]
menufontsize=13
messagefontsize=13
statusfontsize=13
IconTitleSize=13
```

经过这番折腾，大部分情况下，中文显示应该就正常了。
