## 开始

### Mithril 是什么？

Mithril 是一个客户端 javascript MVC 框架，即它是一个工具，使应用程序代码分为数据层(**M**odel)， UI 层(**V**iew)，黏合层(**C**ontroller)。

Mithril 通过 gzip 压缩后，仅有 12kb 左右，这要归功于 [small, focused, API](mithril.md)。它提供了一个模板引擎与一个虚拟 DOM diff 实现高性能渲染，还提供了其它高级工具，以及支持路由和组件化。

框架的目标是使应用程序代码更容易组织，可读和可维护，帮助你成为一个更好的开发者。

不像某些框架，Mithril 努力避免将您锁定到某个 web 框架上：您可以尽量**少**地使用您所需要的框架。

然而，使用其整个工具库可以带来很多好处：学习使用函数式编程和巩固良好的编码实践，OOP 和 MVC 只是其中的一些。

## 一个简单的应用

一旦你安装了 [Mithril](installation.md)，那么开始写 Mighril 代码会让你感到惊讶：

```html
<!doctype html>
<title>Todo app</title>
<script src="mithril.min.js"></script>
<script>
// App 放在这里
</script>
```

是的，这是一个有效的 HTML5! 根据规范，`<html>`，`<head>`，`<body>` 标签可以省略，但各自的 DOM 元素仍将作为一个标记碑浏览器隐式地渲染出来。

### 模型（Model）

在 Mithril 中，应用程序通常在一个命名空间中，他包含了一些组件。组件代表了一个可视“页面”或页面的一部分。此外,应用程序可以组织分为三大层：模型、控制器和视图。

为简单起见，我们的应用程序将只有一个组件，我们将使用它作为应用程序的命名空间。

在 Mithril 中，**组件**是一个对象，包含两个函数：`controller` 和 `view`。

```javascript
// 一个空的 Mithril 组件
var myComponent = {
	controller: function() {},
	view: function() {}
}
```

除了控制器和视图，组件也可以用来存储数据。

让我们创建一个组件。

```html
<script>
// 这个应用只有一个组件：todo
var todo = {};
</script>
```

Typically, model entities are reusable and live outside of components (e.g. `var User = ...`). In our example, since the whole application lives in one component, we're going to use the component as a namespace for our model entities.

通常，模型实体可以在组件之外重用（例如，`var User = ...`）。在我们的示例中，由于整个应用程序只有一个组件,我们将使用组件作为我们的模型实体的命名空间。

```javascript
var todo = {};

// 为简单起见，我们使用这个组件作为模型的命名空间

// Todo 类有 2 个属性
todo.Todo = function(data) {
	this.description = m.prop(data.description);
	this.done = m.prop(false);
};

// TodoList 类是 Todo 的列表
todo.TodoList = Array;
```

[`m.prop`](mithril.prop.md) 只是 getter-setter 函数的工厂。getter-setter 是这样工作的：

```javascript
// 定义一个 getter-setter，初始值为 `John`
var a_name = m.prop("John");

// 读，结果为 John
var a = a_name(); //a == "John"

// 将 `Mary` 的值写入
a_name("Mary"); // Mary

// 读，结果为 Mary
var b = a_name(); //b == "Mary"
```

注意，`Todo` 和 `TodoList` 类是普通 javascript 构造函数。他们可以被初始化和使用：

```javascript
var myTask = new todo.Todo({description: "Write code"});

// read the description
myTask.description(); // Write code

// is it done?
var isDone = myTask.done(); // isDone == false

// mark as done
myTask.done(true); //true

// now it's done
isDone = myTask.done(); // isDone == true
```

`TodoList` 类是 javascript 原生 `Array` 类的别名。

```javascript
var list = new todo.TodoList();
list.length; // 0
```

根据经典 MVC 模式的定义，模型层负责数据存储、状态管理和业务逻辑。

通过上面的步骤你可以看到，我们的课程符合标准：他们的所有方法和属性需要组装成一个有意义的状态。一个 `Todo` 可以实例化，它的属性可以发生变化。可以通过 `push` 方法将代办事项添加到列表中。等等。

#### 视图模型（View-Model）

我们的下一步是编写视图模型（VM），将使用我们的模型类（M）。视图模型（VM）是一个模型级别的实体，用来存储 UI 状态。 在许多框架中，UI 状态通常存储在控制器（C）中，但是这样做使代码难以扩展，因为控制器不是数据提供者。在 Mithril 中，UI 状态在模型数据中，尽管它不一定会通过 ORM 实体映射到数据库。

视图模型（VM）还负责处理 UI 上的业务逻辑。例如一个表单可能包含一个输入按钮和一个取消按钮。在这种情况下,视图模型（VM）的任务是跟踪当前的输入状态和原始状态，如果点击了取消按钮，则恢复到原始状态。当表单保存后，视图模型将委托更合适的 ORM 模型实体处理保存事件。

对于我们的 todo 应用程序，视图模型需要做：它需要跟踪待办事项列表，还需要一个字段用来添加新的待办事项，并且它还需要处理一些逻辑，包括添加待办事项以及响应 UI 的相应动作。

```javascript
// 定义视图模型
todo.vm = {
	init: function() {
		// 待办事项列表
		todo.vm.list = new todo.TodoList();
		
		//a slot to store the name of a new todo before it is created
        // 在新的任务创建之前，存储待办事项的名称
		todo.vm.description = m.prop('');
		
        // 添加一个 todo 到列表中，并清空 description 字段
		todo.vm.add = function(description) {
			if (description()) {
				todo.vm.list.push(new todo.Todo({description: description()}));
				todo.vm.description("");
			}
		};
	}
};
```

上述代码定义了一个视图模型对象 `vm`。它仅仅是一个 javascript 对象，包含 `init` 函数。该函数初始化 `vm` 对象的三个成员：`list`，这是一个数组；`description`，这是一个 `m.prop` 的 getter-setter 函数，以一个空字符串作为初始值；`add`，这是一个方法，当输入的参数 getter-setter 不为空时，将 Todo 实例添加到 `list` 上。

在本指南的后面，我们将使用 `description` 属性作为函数的参数。当我们到达那里时，我将解释为什么我们将 description 作为参数，而不是仅仅使用 OOP 风格的成员属性。

您可以这样使用视图模型：

```javascript
// 初始化视图模型
todo.vm.init();

todo.vm.description(); //[empty string]

// 添加一个待办事项
todo.vm.add(todo.vm.description);
todo.vm.list.length; //0, 因为待办事项的 description 为空

//add it properly
todo.vm.description("Write code");
todo.vm.add(todo.vm.description);
todo.vm.list.length; //1
```

### 控制器（Controller）

在经典 MVC 中，控制器的作用是在视图和模型层中分发 action。在传统的服务器端框架中，控制器层是最重要复杂的，由于 HTTP 是基于“请求-响应”的，因此框架抽象了这个过程，将控制器作为适配器层将 HTTP 请求转换为序列化数据，然后传递给 ORM 模型方法。

然而，在客户端 MVC 中，不存在这种情况，控制器可以是非常简单的。Mithril 的控制器可以精简到最低限度，这样他们只执行一个重要的角色：调用模型视图的功能。您可能还记得，模型负责封装业务逻辑，视图模型负责封装 UI 状态的逻辑，所以不需要具有抽象功能的控制器，我们现在需要的是连接模型和 UI。

换句话说，我们所有的控制器需要做的是：

```javascript
todo.controller = function() {
	todo.vm.init()
}
```

### View

下一步是编写一个视图，这样用户就可以与应用程序交互。在 Mithril 中，视图是普通的 javascript 对象。这有几个好处（错误报告，作用域，等等），同时仍然允许[通过预处理工具使用 HTML 语法](https://github.com/insin/msx)。

```javascript
todo.view = function() {
	return m("html", [
		m("body", [
			m("input"),
			m("button", "Add"),
			m("table", [
				m("tr", [
					m("td", [
						m("input[type=checkbox]")
					]),
					m("td", "task description"),
				])
			])
		])
	]);
};
```

工具方法 `m()` 创建虚拟的 DOM 元素。正如您可以看到的，您可以使用 CSS 选择器来指定属性。您还可以使用 `.` 添加 CSS 类和 `#` 添加一个 id。

事实上，当不使用 [MSX](https://github.com/insin/msx) HTML 语法预处理器时，建议您使用 CSS 选择（例如：`m(".modal-body")`），这样更符合真正的语义。

渲染视图可以使用 `m.render` 方法：

```javascript
m.render(document, todo.view());
```

注意，我们将模板代码渲染到了根 DOM 元素，我们的模板自身也包括了根节点。

以上代码的运行结果如下：

```html
<html>
	<body>
		<input />
		<button>Add</button>
		<table>
			<tr>
				<td><input type="checkbox" /></td>
				<td>task description</td>
			</tr>
		</table>
	</body>
</html>
```

注意，`m.render` 是 Mithril 中非常低级别的方法，它只绘制一次，并不运行 auto-redrawing 系统。为了使用 auto-redrawing，`todo` 组件必须通过 `m.mount` 初始化或通过 `m.route` 创建一个路由。还要注意，不同于 Knockout.js 这种 observable-based 框架，使用 `m.prop` getter-setter 设置一个值时，**不**会触发 Mithril 的重绘。

#### 数据绑定（Data Bindings）

让我们在 text 输入框中实现一个**数据绑定**。数据绑定将 DOM 元素连接到一个 javascript 变量上，以便更新其中一个时，也可以更新另一个。

```javascript
// 将模型的值绑定到 input 上
m("input", {value: todo.vm.description()})
```

以上代码将 `description` 绑定到文本输入框上。更新模型中 description 的值，当 Mithril 重绘时，DOM 的值也会更新。

```javascript
todo.vm.init();

todo.vm.description(); // 空字符串
m.render(document, todo.view()); // input is blank

todo.vm.description("Write code"); //set the description in the controller
m.render(document, todo.view()); // input now says "Write code"
```

乍一看似乎我们做了一些非常昂贵的操作——重绘，但事实证明，多次调用 `todo.view` 方法其实并没有多次重新渲染整个模板。Mithril 又一个虚拟 DOM 的缓存，当扫描到系统有更改时，只修改所需要的最小变化应用，然后应用到 DOM 上。在实践中，这将大大提升重绘的效率。

在上面的案例中，Mithril 只操作了文本框的 `value` 属性 。

注意，上面的例子只是**设置**了 DOM 的 input 元素的值，但没有**读取**它。这意味着我们在文本框中输入值时，当系统重绘时并不会把这个值显示在屏幕上。

---

幸运的是，绑定也可以**双向**：也就是说，我们可以通过编码的方式，除了设置 DOM 值外，还把这个值读取到一个用户自定义类型中，然后更新 视图模型（VM）的 `description` getter-setter。

这是最基本的方式实现视图到模型的绑定：

```javascript
m("input", {onchange: m.withAttr("value", todo.vm.description), value: todo.vm.description()})
```

The code bound to the `onchange` can be read like this: "with the attribute value, set todo.vm.description".

Note that Mithril does not prescribe how the binding updates: you can bind it to `onchange`, `onkeypress`, `oninput`, `onblur` or any other event that you prefer.

You can also specify what attribute to bind. This means that just as you are able to bind the `value` attribute in an `<select>`, you are also able to bind the `selectedIndex` property, if needed for whatever reason.

The `m.withAttr` utility is a functional programming tool provided by Mithril to minimize the need for anonymous functions in the view.

The `m.withAttr("value", todo.vm.description)` call above returns a function that is the rough equivalent of this code:

```javascript
onchange: function(e) {
	todo.vm.description(e.target["value"]);
}
```

The difference, aside from avoiding an anonymous function, is that the `m.withAttr` idiom also takes care of catching the correct event target and selecting the appropriate source of the data - i.e. whether it should come from a Javascript property or from `DOMElement::getAttribute()`

---

In addition to bi-directional data binding, we can also bind parameterized functions to events:

```javascript
var vm = todo.vm

m("button", {onclick: vm.add.bind(vm, vm.description)}, "Add")
```

In the code above, we are simply using the native Javascript `Function::bind` method. This creates a new function with the parameter already set. In functional programming, this is called [*partial application*](http://en.wikipedia.org/wiki/Partial_application).

The `vm.add.bind(vm, vm.description)` expression above returns a function that is equivalent to this code:

```javascript
onclick: function(e) {
	todo.vm.add(todo.vm.description)
}
```

Note that when we construct the parameterized binding, we are passing the `description` getter-setter *by reference*, and not its value. We only evaluate the getter-setter to get its value in the controller method. This is a form of *lazy evaluation*: it allows us to say "use this value later, when the event handler gets called".

Hopefully by now, you're starting to see why Mithril encourages the usage of `m.prop`: Because Mithril getter-setters are functions, they naturally compose well with functional programming tools, and allow for some very powerful idioms. In this case, we're using them in a way that resembles C pointers.

Mithril uses them in other interesting ways elsewhere.

Clever readers will probably notice that we can refactor the `add` method to make it much simpler:

```javascript
vm.add = function() {
	if (vm.description()) {
		vm.list.push(new todo.Todo({description: vm.description()}));
		vm.description("");
	}
};
```

The difference with the modified version is that `add` no longer takes an argument.

With this, we can make the `onclick` binding on the template *much* simpler:

```javascript
m("button", {onclick: todo.vm.add}, "Add")
```

The only reason I talked about partial application here was to make you aware of that technique, since it becomes useful when dealing with parameterized event handlers. In real life, given a choice, you should always pick the simplest idiom for your use case.

---

To implement flow control in Mithril views, we simply use Javascript Array methods:

```javascript
//here's the view
m("table", [
	todo.vm.list.map(function(task, index) {
		return m("tr", [
			m("td", [
				m("input[type=checkbox]")
			]),
			m("td", task.description()),
		])
	})
])
```

In the code above, `todo.vm.list` is an Array, and `map` is one of its native functional methods. It allows us to iterate over the list and merge transformed versions of the list items into an output array.

As you can see, we return a partial template with two `<td>`'s. The second one has a data binding to the `description` getter-setter of the Todo class instance.

You're probably starting to notice that Javascript has strong support for functional programming and that it allows us to naturally do things that can be clunky in other frameworks (e.g. looping inside a `<dl>/<dt>/<dd>` construct).

---

The rest of the code can be implemented using idioms we already covered. The complete view looks like this:

```javascript
todo.view = function() {
	return m("html", [
		m("body", [
			m("input", {onchange: m.withAttr("value", todo.vm.description), value: todo.vm.description()}),
			m("button", {onclick: todo.vm.add}, "Add"),
			m("table", [
				todo.vm.list.map(function(task, index) {
					return m("tr", [
						m("td", [
							m("input[type=checkbox]", {onclick: m.withAttr("checked", task.done), checked: task.done()})
						]),
						m("td", {style: {textDecoration: task.done() ? "line-through" : "none"}}, task.description()),
					])
				})
			])
		])
	]);
};
```

Here are the highlights of the template above:

-	The template is rendered as a child of the implicit `<html>` element of the document.
-	The text input saves its value to the `todo.vm.description` getter-setter we defined earlier.
-	The button calls the `todo.vm.add` method when clicked.
-	The table lists all the existing to-dos, if any.
-	The checkboxes save their value to the `task.done` getter setter.
-	The description gets crossed out via CSS if the task is marked as done.
-	When updates happen, the template is not wholly re-rendered - only the changes are applied.

---

So far, we've been using `m.render` to manually redraw after we made a change to the data. However, as I mentioned before, you can enable an [auto-redrawing system](auto-redrawing.md), by initializing the `todo` component via `m.mount`.

```javascript
//render the todo component inside the document DOM node
m.mount(document, {controller: todo.controller, view: todo.view});
```

Mithril's auto-redrawing system keeps track of controller stability, and only redraws the view once it detects that the controller has finished running all of its code, including asynchronous AJAX payloads. Likewise, it intelligently waits for asynchronous services inside event handlers to complete before redrawing. 

You can learn more about how redrawing heuristics work [here](auto-redrawing.md).

---

### Summary

Here's the application code in its entirety:

```html
<!doctype html>
<script src="mithril.min.js"></script>
<script>
//this application only has one component: todo
var todo = {};

//for simplicity, we use this component to namespace the model classes

//the Todo class has two properties
todo.Todo = function(data) {
	this.description = m.prop(data.description);
	this.done = m.prop(false);
};

//the TodoList class is a list of Todo's
todo.TodoList = Array;

//the view-model tracks a running list of todos,
//stores a description for new todos before they are created
//and takes care of the logic surrounding when adding is permitted
//and clearing the input after adding a todo to the list
todo.vm = (function() {
	var vm = {}
	vm.init = function() {
		//a running list of todos
		vm.list = new todo.TodoList();
		
		//a slot to store the name of a new todo before it is created
		vm.description = m.prop("");
		
		//adds a todo to the list, and clears the description field for user convenience
		vm.add = function() {
			if (vm.description()) {
				vm.list.push(new todo.Todo({description: vm.description()}));
				vm.description("");
			}
		};
	}
	return vm
}())

//the controller defines what part of the model is relevant for the current page
//in our case, there's only one view-model that handles everything
todo.controller = function() {
	todo.vm.init()
}

//here's the view
todo.view = function() {
	return m("html", [
		m("body", [
			m("input", {onchange: m.withAttr("value", todo.vm.description), value: todo.vm.description()}),
			m("button", {onclick: todo.vm.add}, "Add"),
			m("table", [
				todo.vm.list.map(function(task, index) {
					return m("tr", [
						m("td", [
							m("input[type=checkbox]", {onclick: m.withAttr("checked", task.done), checked: task.done()})
						]),
						m("td", {style: {textDecoration: task.done() ? "line-through" : "none"}}, task.description()),
					])
				})
			])
		])
	]);
};

//initialize the application
m.mount(document, {controller: todo.controller, view: todo.view});
</script>
```

This example is also available as a [jsFiddle](http://jsfiddle.net/fbgypzbr/16/).
There is also [Extended example](http://jsfiddle.net/glebcha/q7tvLxsa/) available on jsfiddle.

---

## Notes on Architecture

Idiomatic Mithril code is meant to apply good programming conventions and be easy to refactor.

In the application above, notice how the Todo class can easily be moved to a different component if code re-organization is required.

Todos are self-contained and their data aren't tied to the DOM like in typical jQuery based code. The Todo class API is reusable and unit-test friendly, and in addition, it's a plain-vanilla Javascript class, and so has almost no framework-specific learning curve.

[`m.prop`](mithril.prop.md) is a simple but surprisingly versatile tool: it's functionally composable, it enables [uniform data access](http://en.wikipedia.org/wiki/Uniform_data_access) and allows a higher degree of decoupling when major refactoring is required.

When refactoring is unavoidable, the developer can simply replace the `m.prop` call with an appropriate getter-setter implementation, instead of having to grep for API usage across the entire application.

For example, if todo descriptions needed to always be uppercased, one could simply change the `description` getter-setter:

```javascript
this.description = m.prop(data.description)
```

becomes:

```javascript
//private store
var description;

//public getter-setter
this.description = function(value) {
	if (arguments.length > 0) description = value.toUpperCase();
	return description;
}

//make it serializable
this.description.toJSON = function() {return description}

//set the value
this.description(data.description)
```

In the view-model, we aliased the native Array class for `TodoList`. Be aware that by using the native Array class, we're making an implicit statement that we are going to support all of the standard Array methods as part of our API.

While this decision allows better API discoverability, the trade-off is that we're largely giving up on custom constraints and behavior. For example, if we wanted to change the application to make the list be persisted, a native Array would most certainly not be a suitable class to use.

In order to deal with that type of refactoring, one can explicitly decide to support only a subset of the Array API, and implement another class with the same interface as this subset API.

Given the code above, the replacement class would only need to implement the `.push()` and `.map()` methods. By freezing APIs and swapping implementations, the developer can completely avoid touching other layers in the application while refactoring.

```javascript
todo.TodoList = Array;
```

becomes:

```javascript
todo.TodoList = function () {
	this.push = function() { /*...*/ },
	this.map = function() { /*...*/ }
};
```

Hopefully these examples give you an idea of ways requirements can change over time and how Mithril's philosophy allows developers to use standard OOP techniques to refactor their codebases, rather than needing to modify large portions of the application.

---

The first and most obvious thing you may have noticed in the view layer is that the view is not written in HTML.

While superficially this may seem like an odd design, this actually has a lot of benefits:

-	No flash-of-unbehaviored-content (FOUC). In fact, Mithril is able to render a fully functional application - with working event handlers - before the "DOM ready" event fires!

-	There's no need for a parse-and-compile pre-processing step to turn strings containing HTML + templating syntax into working DOM elements.

-	Mithril views can provide accurate and informative error reporting, with line numbers and meaningful stack traces.

-	You get the ability to automate linting, unit testing and minifying of the entire view layer.

-	It provides full Turing completeness: full control over evaluation eagerness/laziness and caching in templates. You can even build components that take other components as first-class-citizen parameters!

And if you really do want to use HTML syntax after all, [you can use a package called MSX](https://github.com/insin/msx).

Views in Mithril use a virtual DOM diff implementation, which sidesteps performance problems related to opaque dirty-checking and excessive browser repaint that are present in some frameworks.

Another feature - the optional `m()` utility - allows writing terse templates in a declarative style using CSS shorthands, similar to popular HTML preprocessors from server-side MVC frameworks.

And because Mithril views are Javascript, the developer has full freedom to abstract common patterns - from bidirectional binding helpers to full blown components - using standard Javascript refactoring techniques.

Mithril templates are also more collision-proof than other component systems since there's no way to pollute the HTML tag namespace by defining ad-hoc tag names.

A more intellectually interesting aspect of the framework is that event handling is encouraged to be done via functional composition (i.e. by using tools like [`m.withAttr`](mithril.withAttr.md), [`m.prop`](mithril.prop.md) and the native `.bind()` method for partial application).

If you've been interested in learning or using Functional Programming in the real world, Mithril provides very pragmatic opportunities to get into it.

---

## Learn More

Mithril provides a few more facilities that are not demonstrated in this page. The following topics are good places to start a deeper dive.

-	[Routing](routing.md)
-	[Web Services](web-services.md)
-	[Components](components.md)

## Advanced Topics

-	[Optimizing performance](optimizing-performance.md)
-	[Integrating with the Auto-Redrawing System](auto-redrawing.md)
-	[Integrating with Other Libraries](integration.md)

## Misc

-	[Differences from Other MVC Frameworks](comparison.md)
-	[Benchmarks](benchmarks.md)
-	[Good Practices](practices.md)
-	[Useful Tools](tools.md)
