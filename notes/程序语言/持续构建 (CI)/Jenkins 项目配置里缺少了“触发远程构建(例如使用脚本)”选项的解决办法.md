# Jenkins 项目配置里缺少了“触发远程构建(例如使用脚本)”选项的解决办法

原因是因为没有在Jenkins上没有开启安全选项，其实很简单

系统管理 -> Configure Global Security

如图配置好就额可以了

![](vx_images/20200204203939068_4109.png)

---
via: https://blog.csdn.net/taoli1986/article/details/52789434
