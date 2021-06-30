---
via: http://fool.is-programmer.com/2011/3/6/undefined-reference-to-rpl_malloc.25019.html
---

# undefined reference to `rpl_malloc'

今天交叉编译一个程序時遇到了一个很奇怪的问题，编译到最后一步链接時，发生了下面的错误：

```
/opt/toolschain/3.4.1/arm-linux/lib/libjson.so: undefined reference to `rpl_realloc'
/opt/toolschain/3.4.1/arm-linux/lib/libjson.so: undefined reference to `rpl_malloc'
```

很显然，在链接libjson库時发生了错误，libjson不知道为什么链接了两个不存在的函数：`rpl_realloc`和`rpl_malloc`。因为工具链中的所有库都是自己编译的，所以只能从自己身上找错误==。

遂重新编译libjson，发现config.h里有下面的几句话：

```c
/* Define to 1 if your system has a GNU libc compatible `malloc' function, and
to 0 otherwise. */
#define HAVE_MALLOC 0
/* Define to 1 if your system has a GNU libc compatible `realloc' function,
and to 0 otherwise. */
#define HAVE_REALLOC 0
...
/* Define to rpl_malloc if the replacement function should be used. */
#define malloc rpl_malloc
/* Define to rpl_realloc if the replacement function should be used. */
#define realloc rpl_realloc
```

看来是交叉编译時autotools认为我的工具链的libc中不包含`malloc`和`realloc`，然后擅自做主张给我替换成了`rpl_malloc`和`rpl_realloc`。把上面的几句话删掉后重新编译，就正常了。
