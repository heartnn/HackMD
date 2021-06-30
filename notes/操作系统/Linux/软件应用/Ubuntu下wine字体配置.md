---
tags: ubuntu, wine, font
via: http://clayz.iteye.com/blog/206661
---

# Ubuntu下wine字体配置

将 windows xp 的 Fonts 目录下 simsun.ttc 复制到 `~/.wine/drive_c/windows/fonts/` 目录中。

把下列内容另存为 fonts.reg，保存到 `~/.wine/` 目录下

```reg
REGEDIT4

[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontSubstitutes]
"Arial"="simsun"
"Arial CE,238"="simsun"
"Arial CYR,204"="simsun"
"Arial Greek,161"="simsun"
"Arial TUR,162"="simsun"
"Courier New"="simsun"
"Courier New CE,238"="simsun"
"Courier New CYR,204"="simsun"
"Courier New Greek,161"="simsun"
"Courier New TUR,162"="simsun"
"FixedSys"="simsun"
"Helv"="simsun"
"Helvetica"="simsun"
"MS Sans Serif"="simsun"
"MS Shell Dlg"="simsun"
"MS Shell Dlg 2"="simsun"
"System"="simsun"
"Tahoma"="simsun"
"Times"="simsun"
"Times New Roman CE,238"="simsun"
"Times New Roman CYR,204"="simsun"
"Times New Roman Greek,161"="simsun"
"Times New Roman TUR,162"="simsun"
"Tms Rmn"="simsun"
```

导入上一步保存的 fonts.reg

```bash
regedit ~/.wine/fonts.reg
```

好了，中文显示基本上没有问题了，并且可以用 scim 输入法输入中文。
