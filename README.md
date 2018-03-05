# d3-dispatch

Dispatching(分发)是一个用来降低代码耦合度的便捷方式: 注册回调函数然后使用任意参数调用.很多D3组件比如[d3-request](https://github.com/xswei/d3js_doc/blob/master/API_Reference/d3-request)，通过这种机制触发事件监听器。与 Nodejs 的 [EventEmitter](https://nodejs.org/api/events.html) 类似，只不过每个监听器都有确定的名字以方便移除或替换。

例如为 *start* 和 *end* 事件创建分发:

```js
var dispatch = d3.dispatch("start", "end");
```

然后使用 [*dispatch*.on](#dispatch_on) 注册回调函数：

```js
dispatch.on("start", callback1);
dispatch.on("start.foo", callback2);
dispatch.on("end", callback3);
```

然后可以通过 [*dispatch*.call](#dispatch_call) 或者 [*dispatch*.apply](#dispatch_apply) 来调用所有的 *start* 回调：

```js
dispatch.call("start");
```

和 *function*.call 类似，你也可以指定 `this` 环境和任意参数：

```js
dispatch.call("start", {about: "I am a context object"}, "I am an argument");
```

更多分发的例子参考 [d3-dispatch for coordinated views](http://bl.ocks.org/mbostock/5872848).

## Installing

NPM 安装: `npm install d3-dispatch`. 或者下载 [latest release](https://github.com/d3/d3-dispatch/releases/latest). 也可以直接从 [d3js.org](https://d3js.org) 以 [standalone library](https://d3js.org/d3-dispatch.v1.min.js) 或作为 [D3 4.0](https://github.com/d3/d3) 的一部分直接引入. 支持 AMD, CommonJS 和基本的标签引入形式，如果使用标签引入会暴露全局 `d3` 变量:

```html
<script src="https://d3js.org/d3-dispatch.v1.min.js"></script>
<script>

var dispatch = d3.dispatch("start", "end");

</script>
```

[在浏览器中测试 d3-dispatch](https://tonicdev.com/npm/d3-dispatch)

## API Reference

<a name="dispatch" href="#dispatch">#</a> d3.<b>dispatch</b>(<i>types…</i>) [<>](https://github.com/d3/d3-dispatch/blob/master/src/dispatch.js "Source")

为指定的事件 *types* 创建一个新的分发器。每个 *type* 都是一个字符串，比如 `"start"` 或 `"end"`.

<a name="dispatch_on" href="#dispatch_on">#</a> *dispatch*.<b>on</b>(<i>typenames</i>[, <i>callback</i>]) [<>](https://github.com/d3/d3-dispatch/blob/master/src/dispatch.js#L26 "Source")

添加、移除或者获取指定 *typenames* 的 *callback*。如果指定了 *callback* 则将其注册为对应的 *typenames* 的回调，如果对应的 *typenames* 已经有回调则将其替换。

*typenames* 是一个字符串，包含(`.`)分开的 *type* 和 *names* ，其中 *names* 可选。比如 `start` or `end.foo` 这种形式。也就是说同一种 *type* 可以指定多个不同 *name* 的回调，比如 `start.foo` 和 `start.bar `，在同时指定多个 *typenames* 时可以使用空格隔开: `start end` or `start.foo start.bar`.

移除所有 *name* 为 *foo* 的回调，使用`dispatch.on(".foo", null)`.

如果没有指定 *callback* 则返回当前 *typenames* 对应的回调(如果有的话)。如果有多个符合条件的则返回第一个匹配的回调。

<a name="dispatch_copy" href="#dispatch_copy">#</a> *dispatch*.<b>copy</b>() [<>](https://github.com/d3/d3-dispatch/blob/master/src/dispatch.js#L49 "Source")

返回当前分发对象的副本。修改原分发对象不会影响副本，反之亦然。

<a name="dispatch_call" href="#dispatch_call">#</a> *dispatch*.<b>call</b>(<i>type</i>[, <i>that</i>[, <i>arguments…</i>]]) [<>](https://github.com/d3/d3-dispatch/blob/master/src/dispatch.js#L54 "Source")

与 [*function*.call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) 类似, 调用对应 *type* 的每个回调，*that* 参数表示回调的 *this* 上下文并传递指定的 *arguments*，参考 [*dispatch*.apply](#dispatch_apply)。

<a name="dispatch_apply" href="#dispatch_apply">#</a> *dispatch*.<b>apply</b>(<i>type</i>[, <i>that</i>[, <i>arguments</i>]]) [<>](https://github.com/d3/d3-dispatch/blob/master/src/dispatch.js#L59 "Source")

与 [*function*.apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) 类似, 调用 *type* 的每个回调，*that* 参数表示回调的 *this* 上下文并传递指定的 *arguments*，例如，如果你想在原始的点击事件后分发一个自定义回调，并给回调传递 `this` 上下文和指定的参数，可以: 

```js
selection.on("click", function() {
  dispatch.apply("custom", this, arguments);
});
```

你可以传递任何想传递的参数，更一般的情况下，你可能会创建一个对象表示当前事件，或者传递当前的数据 (*d*) 和索引 (*i*)；参考[function.call](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Function/Call) 和 [function.apply](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Function/Apply) 获取更多信息。
