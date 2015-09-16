## 路由

路由使我们可以创建单页应用（SPA），即可以从一个页面跳转到另一个页面，而不会不需要刷新浏览器。

它使我们可以使用导航功能无缝的切换页面，同时可以为每个单独页面添加到收藏夹里，并且能够使用浏览器的历史记录功能。

Mithril 的提供了处理路由的实用工具，以便我们：

-	定义路由列表
-	以编程方式处理路由跳转
-	生成模板中的超链接：显式的或隐式的

### 定义路由

定义路由列表，您需要指定一个 host DOM 元素，一个默认路由和一个键值对映射，这个键值对包括路由定义和需要呈现的 [modules](mithril.mount.md)。注意：`module` 已经重命名为了 `mount`。相关文档也正在更新中。

下面的例子定义了三个路由，呈现在 `<body>` 中。`home`, `login` 和 `dashboard` 是模块。我们将会看到如何定义一个模块。

```javascript
m.route(document.body, "/", {
	"/": home,
	"/login": login,
	"/dashboard": dashboard,
});
```

路由可以带参数，通过添加一个冒号 `:` 前缀。

下面的例子演示了一个带有 `userID` 参数的路由。

```javascript
// 模块
var dashboard = {
	controller: function() {
		return {id: m.route.param("userID")};
	},
	view: function(controller) {
		return m("div", controller.id);
	}
}

//setup routes to start w/ the `#` symbol
// 设置路由为 `#`
m.route.mode = "hash";

// 定义路由
m.route(document.body, "/dashboard/johndoe", {
	"/dashboard/:userID": dashboard
});
```

访问 URL `http://server/#/dashboard/johndoe` 将得到：

```html
<body>johndoe</body>
```

上面示例中，`dashboard` 是一个模块。它包含 `controller` 和 `view` 属性。当 URL 和路由规则匹配时，模块的控制器将会实例化并作为参数传递给视图。

由于只有一个路由规则，应用重定向到默认路由 `"/dashboard/johndoe"` 然后调用 `m.mount(document.body, dashboard)`。

字符串 `johndoe` 绑定到 `:userID` 参数，控制器通过 `m.route.param("userID")` 访问此参数。

`m.route.mode` 属性定义了 URL 的哪个部分用来实现路由机制。它的值可以是 "search"、hash" 或 "pathname"，默认为 "search"。

-	`search` 模式使用查询字符串。这样超链接锚点(即 `<a href="#top">Back to top</a>`，`<a name="top"></a>`)可以在页面上正常工作,但在 IE8 上，路由的变化将导致页面刷新，因为 IE8 不支持 `history.pushState`。

	Example URL: `http://server/?/path/to/page`

-	`hash` 模式使用 `#`。这是唯一的路由变化不会导致任何浏览器刷新页面的机制。然而，这种模式不支持锚点和浏览器历史记录。

	Example URL: `http://server/#/path/to/page`

- 	`pathname` 模式的路由的 URL 不能包含特殊字符，然而这种模式想要支持收藏夹和页面刷新，需要服务器端进行设置。 它也会导致 IE8 浏览器的页面刷新。

	Example URL: `http://server/path/to/page`

	最简单的支持 pathname 模式的服务器端设置是不论 URL 请求是什么，都提供相同的内容。在 Apache 中，这可以通过使用 [mod_rewrite](https://httpd.apache.org/docs/current/mod/mod_rewrite.html) 模块来实现 URL 重写。


### 重定向

You can programmatically redirect to another page. Given the example in the "Defining Routes" section:

您可以通过编程方式重定向到另一个页面。参考 “定义路由” 一节中的例子：

```javascript
m.route("/dashboard/marysue");
```

重定向到 `http://server/#/dashboard/marysue`

### Mode abstraction 模式抽象

这种方法是：使用一个虚拟元素的 `config` 属性。例如:

```javascript
//Note that the '#' is not required in `href`, thanks to the `config` setting.
// 注意，由于使用了 `config`，因此 `href` 不需要包含 '#'
m("a[href='/dashboard/alicesmith']", {config: m.route});
```

这使得 href 的行为肯定是正确的，而不管 `m.route.mode` 定义了什么模式。这是一个很好的编程实践，我们应该使用上面的编程方式，而不是把 `?` 或 `#` 硬编码到 href 属性。

阅读 [`m()`](mithril.md) 可以了解关于虚拟元素的更多信息。

### Redrawing semantics of routing

By default, changing routes causes templates to be re-rendered from scratch. This behavior can be changed either via the [retain flag in a config's context object](mithril.md#persisting-dom-elements-across-route-changes), or the [`m.redraw.strategy("diff")` hint](mithril.redraw.md#changing-redraw-strategy).
