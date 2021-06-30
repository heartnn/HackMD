---
tags: flash, win7
---

# Win7网页Flash卡的解决办法

装了Win7正式旗舰版，软激活，打开带有Flash的网页很卡（特别是QQ牧场农场），上下滚动都有困难，CPU 80-100%。

解决办法：

- 可能是Win7自带的Flash插件运行不稳定，把`C:\windows\system32\Macromed\Flash`下的所有文件删除。
- 有可能Flash10c.ocx文件无法删除，是因为系统占用的原因。删除Flash10c.ocx文件的办法在网上很多，可以去搜一搜，我的办法是下载一下暴力删除文件的工具地址：超级巡警暴力删除器 或在PE下删除。
- 在网上下载一个最新的Flash插件地址，然后重启Win7就OK了。
