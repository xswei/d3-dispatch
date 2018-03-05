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

The specified *typenames* is a string, such as `start` or `end.foo`. The type may be optionally followed by a period (`.`) and a name; the optional name allows multiple callbacks to be registered to receive events of the same type, such as `start.foo` and `start.bar`. To specify multiple typenames, separate typenames with spaces, such as `start end` or `start.foo start.bar`.

To remove all callbacks for a given name `foo`, say `dispatch.on(".foo", null)`.

If *callback* is not specified, returns the current callback for the specified *typenames*, if any. If multiple typenames are specified, the first matching callback is returned.

<a name="dispatch_copy" href="#dispatch_copy">#</a> *dispatch*.<b>copy</b>() [<>](https://github.com/d3/d3-dispatch/blob/master/src/dispatch.js#L49 "Source")

Returns a copy of this dispatch object. Changes to this dispatch do not affect the returned copy and <i>vice versa</i>.

<a name="dispatch_call" href="#dispatch_call">#</a> *dispatch*.<b>call</b>(<i>type</i>[, <i>that</i>[, <i>arguments…</i>]]) [<>](https://github.com/d3/d3-dispatch/blob/master/src/dispatch.js#L54 "Source")

Like [*function*.call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call), invokes each registered callback for the specified *type*, passing the callback the specified *arguments*, with *that* as the `this` context. See [*dispatch*.apply](#dispatch_apply) for more information.

<a name="dispatch_apply" href="#dispatch_apply">#</a> *dispatch*.<b>apply</b>(<i>type</i>[, <i>that</i>[, <i>arguments</i>]]) [<>](https://github.com/d3/d3-dispatch/blob/master/src/dispatch.js#L59 "Source")

Like [*function*.apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call), invokes each registered callback for the specified *type*, passing the callback the specified *arguments*, with *that* as the `this` context. For example, if you wanted to dispatch your *custom* callbacks after handling a native *click* event, while preserving the current `this` context and arguments, you could say:

```js
selection.on("click", function() {
  dispatch.apply("custom", this, arguments);
});
```

You can pass whatever arguments you want to callbacks; most commonly, you might create an object that represents an event, or pass the current datum (*d*) and index (*i*). See [function.call](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Function/Call) and [function.apply](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Function/Apply) for further information.
