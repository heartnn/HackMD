# process.cwd()和__dirname的区别

process.cwd()返回当前工作目录。如：调用node命令执行脚本时的目录。
 __dirname返回源代码所在的目录。

eg：对于d:\dir\index.js。

```node
console.log(`cwd: ${process.cwd()}`);
console.log(`dirname: ${__dirname}`);
```

| 命令 | process.cwd() | __dirname |
| --- | --- | --- |
| node index.js | d:\dir | d:\dir |
| node dir\index.js | d: | d:\dir |

---
via: https://www.jianshu.com/p/a80c59abd1fb
