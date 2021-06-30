---
tags: wine, font
---

# 设置Wine程序的字体

分类: Linux
最后修改: Mar 8, 2021 2:30 PM
来源: http://www.linux-wiki.cn/index.php/设置wine程序的字体
标签: wine

Wine虽然还很不成熟，但它的界面经过字体设置还是很清晰的。如果你的 Wine跑的程序字体模糊或难看，不要只是报怨Wine，先尝试一下设置字体。

## Windows风格字体

使用Windows提供的宋体Simsun是最简便的方法。 
复制字体文件

假设Windows分区被挂载到`/mnt/win_c/`，则

```
cp /mnt/win_c/WINDOWS/Fonts/simsun.ttc ~/.wine/drive_c/windows/fonts/
```

此后wine会自动使用复制来的宋体。

## 更改字体大小

由于默认字体很小，宋体字显得很难看。放大字体，需要修改（或新建） `~/.wine/system.reg` ，加入

```
[System\CurrentControlSet\Hardware Profiles\Current\Software\Fonts]
"LogPixels"=dword:00000082
```

保存，重新用Wine运行程序，就能看到效果。

## 使用当前系统字体

方法是在命令行输入`wine regedit`进入注册表，改写默认字体 ,或者直接编辑`~/.wine/system.reg`

```
[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontSubstitutes] 
"MS Shell Dlg"="WenQuanYi Zen Hei"
"MS Shell Dlg 2"="WenQuanYi Zen Hei"
"System"="WenQuanYi Zen Hei"
```

需要安装文泉驿正黑字体。

另外，运行一些软件时可能发现菜单的字体仍不是我们所期望的，此时可以修改`~/.wine/drive_c/windows/win.ini`，在文件中加入：

```
[Desktop]
IconTitleFaceName=SimSun
```
