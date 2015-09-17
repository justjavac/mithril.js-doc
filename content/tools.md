## 工具

### HTML-to-Mithril 模板转换器

如果你已经有了 HTML 写的网页，想将它转换成一个 Mighril 的模板，您可以使用下面的工具进行手动转换。

[模板转换器](http://mithril.js.org/tools/template-converter.html)

### 自动 HTML-to-Mithril 模板转换器

有一种工具叫做 [MSX by Jonathan Buchanan](https://github.com/insin/msx) 允许您使用 HTML 语法编写模板，然后自动编译成 javascript 文件。

对团队来说是很有用的，我们可以在模板中使用 HTML 语法。

该工具允许您编写如下代码：

```javascript
todo.view = function() {
	return <html>
		<body>
			<input onchange={m.withAttr("value", app.vm.description)} value={app.vm.description()}/>
			<button onclick={app.vm.add}>Add</button>
		</body>
	</html>
};
```

注意，因为上面的代码并不是有效的 javascript，这种语法只能使用预处理构建工具，比如 [Gulp.js](http://gulpjs.com) 脚本。

这个工具也可以使用 [Rails gem](https://github.com/mrsweaters/mithril-rails)，由 Jordan Humphreys 创建。

### Mithril 模板编译器

你可以预编译 Mithril 模板使其运行得更快。更多信息见这个页面:

[编译模板](optimizing-performance.md#compiling-templates)

### Typescript 支持

使用类型定义文件，可以让 Mithril 支持 Typescript 

[mithril.d.ts](mithril.d.ts)

您可以通过添加一个指向 Typescript 文件的引用来使用它。这将允许编译器对 Mithril API 的调用进行类型检查。

```javascript
/// <reference path="mithril.d.ts" />
```

### IE 浏览器的兼容性

Mithril 依赖于一些 ECMAScript 5 的特性，即：`Array::indexOf`、`Array::map` 和 `Object::keys`，以及 `JSON` 对象。IE8 缺乏对这些特性的原生支持。

为 IE8 添加这些特性支持，最简单的方法是使用这个脚本：

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/es5-shim/4.0.3/es5-shim.min.js"></script>
```

这将为浏览器提供所需的所有新特性。另外，你也可以选择性的让浏览器支持某个特性：

```html
<script src="http://cdn.polyfill.io/v1/polyfill.min.js?features=Array.prototype.indexOf,Object.keys,Function.prototype.bind,Array.prototype.forEach,JSON"></script>
```

You can also use other polyfills to support these features in IE7.

您还可以使用其他方式以便让 IE7 支持这些特性。

-	[ES5 Shim](https://github.com/es-shims/es5-shim) 或 Mozilla.org 的 [Array::indexOf](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf), [Array::map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 和 [Object::keys](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)

-	[JSON2.js](https://github.com/douglascrockford/JSON-js/blob/master/json2.js)

Mithril 也依赖于 XMLHttpRequest。如果您希望支持 IE6，你需要 [a shim for it](https://gist.github.com/Contra/2709462)。 IE7及以下版本不支持跨域 AJAX 请求。

此外，请注意 `m.route` 模式依赖 `history.pushState`，此 API 可以实现从一个页面跳转到另一个页面，而不刷新浏览器。[IE9及以下版本浏览器](http://caniuse.com/#search=pushstate)不支持此功能，而是优雅地降级到页面刷新。