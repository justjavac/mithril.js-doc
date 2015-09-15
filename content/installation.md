## 安装

Mithril 有多种安装方式

### 直接下载

直接在 github 下载[ zip 格式的最新版本](http://lhorie.github.io/mithril/mithril.min.zip)。

旧版本的链接可以在[更新日志](change-log.md)中找到。

使用 Mithril 时，需要从 zip 压缩包里面提取出 `.js` 文件：

```html
<script src="mithril.min.js"></script>
```

注意：为了在低版本的 IE 上使用 Mithril，你需要 [shim 兼容库](tools.md#internet-explorer-compatibility).

### CDN（内容分发网络）

你也可以通过CDN 使用 Mithril，比如 [cdnjs](http://cdnjs.com/libraries/mithril/) 或 [jsDelivr](http://www.jsdelivr.com/#!mithril)。

CDN 允许用户端缓存相同版本的框架，即使这些框架被不同的网站使用。通过把 JS 库缓存到距离用户最近的服务器上，以提升性能。

#### cdnjs

```html
<script src="//cdnjs.cloudflare.com/ajax/libs/mithril/$version/mithril.min.js"></script>
```

#### jsDelivr

```html
<script src="//cdn.jsdelivr.net/mithril/$version/mithril.min.js"></script>
```

### NPM

NPM 是 [NodeJS](http://nodejs.org/) 的默认包管理器。如果你曾经使用过 NodeJS，或者打算使用 [Grunt](http://gruntjs.com/) 构建系统，你可以很方便的使用 NPM 得到 Mithril 的最新版。

一旦你安装了 NodeJS，你可以使用如下命令下载 Mithril 的最新版：

```
npm install mithril
```

然后，在 `script` 标签中插入下载的 js 文件：

```html
<script src="/node_modules/mithril/mithril.min.js"></script>
```

### Bower

[Bower](http://bower.io) 是 [NodeJS](http://nodejs.org/) 的前端包管理器。如果你曾经使用过 NodeJS，或者打算使用 [Grunt](http://gruntjs.com/) 构建系统，你可以很方便的使用 NPM 得到 Mithril 的最新版。

一旦你安装了 NodeJS，你可以使用如下命令安装 Bower：

```
npm install -g bower
```

再输入以下命令下载 Mithril：

```
bower install mithril
```

然后，在 `script` 标签中插入下载的 js 文件：

```html
<script src="/bower_components/mithril/mithril.min.js"></script>
```

### Component

[Component](http://component.github.io) 是 [NodeJS](http://nodejs.org/) 的另一个包管理器。如果你曾经使用过 NodeJS，或者打算使用 [Grunt](http://gruntjs.com/) 构建系统，你可以很方便的使用 NPM 得到 Mithril 的最新版。

一旦你安装了 NodeJS，你可以使用如下命令安装 Component：

```
npm install -g component
```

再输入以下命令下载 Mithril：

```
component install lhorie/mithril
```

然后，在 `script` 标签中插入下载的 js 文件：

```html
<script src="/components/lhorie/mithril/master/mithril.js"></script>
```

### Rails

Jordan Humphreys 创建了一个 gem，用来在 Rails 上集成 Mithril

[Mithril-Rails](https://github.com/mrsweaters/mithril-rails)

他支持 Jonathan Buchanan 创建 [MSX](https://github.com/insin/msx) HTML 模板语法。

### Github

你也可以从 Github 上 fork 项目的[最终版本](https://github.com/lhorie/mithril)。

如果你想使用前沿版本，你可以 fork [development 分支](https://github.com/lhorie/mithril.js)。

注意，Mithril 的测试运行在一个持续集成环境中，前沿版本可能偶尔通不过测试。如果你有兴趣帮我们改善 Mithril，欢迎你使用前沿版本并报告任何你发现的 bug。

为了更好的更新 Mithril 的分支版本，阅读[这篇文章](https://help.github.com/articles/syncing-a-fork)。

#### 通过 NPM 使用前沿版本

从 npm 使用前沿版本，使用以下命令：

```
npm install git://github.com/lhorie/mithril.js#next --save
```

