# Pkg——打包node为可执行文件(.exe)工具

## 这有什么用呢？

我呢，就是为了演示 `web` 界面（+ 仿真数据），又不想依赖于 `node` 。。。等一系列的前端环境

这个工具，直接会生成一个 `exe` 文件，双击即可运行我们的 `node` 程序

## 如何使用，可参考以下两篇文章

- [node打包可执行文件工具——Pkg使用心得](https://www.cnblogs.com/mordom/archive/2018/01/23/8337337.html)
- [使用pkg打包Node.js应用](https://jingsam.github.io/2018/03/02/pkg.html)

### 简单说说呢

#### 第一步，先安装 `Pkg`

    npm install -g pkg

安装完后，可以通过 `pkg -h` 查看相关命令

#### 第二步， 尝试运行

```bash
pkg api.js           // 将 api.js 编译成 api.exe 可执行文件
pkg -t win api.js    // 上面的命令会同时编译出 linux 、windows 、mac 版的 exe，加 -t win 就可以只编译 windows 下的
```

这时，可能会有部分同学**遇到问题, 报错了！！！**

```node
> pkg@4.4.0
> Fetching base Node.js binaries to PKG_CACHE_PATH
  fetched-v10.15.3-win-x64     [                    ] 0%
> Error! connect ETIMEDOUT 13.229.188.59:443
> Asset not found by direct link:
  {"tag":"v2.6","name":"uploaded-v2.6-node-v10.15.3-win-x64"}
```

**如何解决？**

到 [github.com/zeit/pkg-fetch](https://github.com/zeit/pkg-fetch/releases) 下载对应 `node` 版本的包（比如，我下的就是 `uploaded-v2.6-node-v10.15.3-win-x64`）

然后复制粘贴到 `C:\Users\aaa\.pkg-cache\v2.6` 这个目录下，重命名（我的就重命名为 `fetched-v10.15.3-win-x64`），同时将这个目录下的 `.downloading` 结尾的文件删除了

重新输入命令，嗯，生成对应的 `.exe` 文件了，双击该文件，与我们直接运行 `node api.js` 的效果是一样的

同理，如果想要在编译 `linux` 版本的的话，就把对应的 `node` 包，名字修改为 `fetch-vXX.XX.X-linux-x64` 即可

#### 其他打包方式

`pkg` 可以根据 `package.json` 下的配置进行打包

```json
// 以 express 为例
// bin 对应的 express 的入口文件，即 输入 node api.js，即可运行我的 express 代码（这个版本有点旧，新的话好像是 bin/www）
// assets 对应的是 express.static 下的静态文件目录
{
    "bin": "api.js",
    "pkg": {
        "assets": [
            "dist/**/*"    //静态文件的目录，可添加多个
        ]
    }
}
```

此时运行 `pkg -t win .` 即可根据 `package.json` 进行编译

#### 点击 .exe，可能还有问题

点击 `.exe`，发现有些接口返回 `404 Not Found`

例如下例，将 `exe` 放到其他文件下，尝试打开页面，发现报 `Can't get /`

    app.use('/', express.static('dist'));

因为 `pkg` 没有把这些文件给打包进去，他只会打包使用 `require` 或者 通过 `__filename` 和 `__dirname` 的文件

所以需要修改为

    app.use('/', express.static(path.join(__dirname, 'dist')));

#### 其他注意项

如果有些配置文件，不想被打包进 `exe` 文件中，而是想把部分配置文件放在外面可被修改，则可以使用 `process.cwd()` 来引用文件

```node
const port = require(process.cwd() + '/port');

// 不过编译的时候，pkg 有告警提示
Dynamic require may fail at run time, because the requested file
is unknown at compilation time and not included into executable.
Use a string literal as an argument for 'require', or leave it
as is and specify the resolved file name in 'scripts' option.
```

`process.cwd()` 和 `__diranme` 的区别可以参考这文章： [process.cwd()和__dirname的区别](process.cwd()和__dirname的区别.md)

---
via: https://juejin.im/post/5d653b1051882569d64b9893
