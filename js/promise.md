# Promise 的复习

先来无事，复习下旧知识。

## API

`new Promise(executor)`

构建一个对象(闭包)，然后用闭包中的对象当参数执行executor函数。
new Promise 返回的实例中维护者闭包中的一些回调，实例方法中有两个触发器，resolve和reject。当resolve调用之后，闭包中的部分回调将会触发。executor是当实例构建完成时候Promise实现中主动触发的函数。可以作为开始触发resolve的逻辑。

* resolve & reject 方法只存在于 Promise 的实现中，并没有暴露给promise实例，所以只能在executor中调用。
* 报出错误是会触发错误的回调。

`promise.then() & promise.catch()`

上面我们说到闭包中存在类似于栈的回调组合。一般实例都会有维护闭包的方法，then 和 catch 就是。前者是添加成功的回调函数（第二个参数是失败的回调，但是一般很少用，因为代码丑），后者是失败的。

`Promise.all(iterable)`

```js
  var promise1 = Promise.resolve(3);
  var promise2 = 42;
  var promise3 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 100, 'foo');
  });

  Promise.all([promise1, promise2, promise3]).then(function(values) {
    console.log(values);
  });
```

注意是Promise的方法，不是实例的方法。all 可以等待全部的任务成功，或者一个失败的情况。

`Promise.allSettled()`

跟 all 差不过，只是他会等全部任务结束，才会触发。并且只会成功不会触发失败。

`Promise.race(iterable)`

跟 all 差不多，只是他会在第一个任务触发后触发回调。第一个是成功的就then触发，失败就catch的回调触发。

`Promise.reject()`

返回一个status为失败的promise实例，单看这个可能会觉得没用，但是其实觉大大了增加了promise处理的灵活性，请看

```js
  (new Promise(
    (res) => {setTimeout(res(), 100)}
  )).then((response) => {
    if (!response) {
      return Promise.reject('undefined')
    }
  }).catch(error => {
    console.log(error)
  })
  // 这里会触发catch添加的回调。打出日志 undefined
```

`Promise.resolve()`

返回一个status是成功的promise实例。

## 坑（小心）

`promise.then()`

promise.then 中的回调的返回会是下一个回调的参数，所以每次当你不想做任何操作的时候最好把当前参数return出来，以防同事要用。

`promise.catch()`

如果catch不返回下一个catch连触发都不会触发。所以除非你特意为止，不然还是返回吧。

## 进阶

在then添加的回调中 return 一个promise实例，中断当前的回调栈，等待新promise 的状态改变再进行回调栈。

```js
  new Promise((res, rej) => {
    setTimeout(res, 100) 
  }).then(() => {
    console.log('hello')
  }).then(() => {
    console.log('world')
  }).catch(() => {
    console.log('this log won\'t print')
  })

  // hello world

  new Promise((res, rej) => {
    setTimeout(res, 100)
  }).then(() => {
    console.log('hello')
    return new Promise((res, rej) => {
      console.log('one hours latter')
      setTimeout(rej, 1000 * 60 * 60)
    })
  }).then(() => {
    console.log('world')
  }).catch(() => {
    console.log('fucking world')
  })

  // hello
  // one hours latter
  // fucking world
```

* 分析：如果then只是单纯的做添加一个回调到回调栈中，那么res触发之后就把回调栈清空，很明显这个then还做了一些特别的事。

因为Promise是ES的实现，所以我们比较难看到他的内部实现。但是JQuery这个库很早就已经实现了这个方法。虽然API不完全一样，但是then可以说内部思想是一样的。下面我们通过观看JQ源码理解Promise是怎么做到的。

多代码警告⚠️！！！！ 不想看的可以,看我最后的总结。

```js
  // 这里就是 jQuery Deferred 的回调栈。知道我们的成功失败的回调放在这里就行了
  // action, add listener, callbacks,
  // ... .then handlers, argument index, [final state]
  [ "notify", "progress", jQuery.Callbacks( "memory" ),
    jQuery.Callbacks( "memory" ), 2 ],
  [ "resolve", "done", jQuery.Callbacks( "once memory" ),
    jQuery.Callbacks( "once memory" ), 0, "resolved" ],
  [ "reject", "fail", jQuery.Callbacks( "once memory" ),
    jQuery.Callbacks( "once memory" ), 1, "rejected" ]
```

```js
	then: function( onFulfilled, onRejected, onProgress ) {
    /* 省略看一个函数要先看其副作用的东西，辅助方法后面在看 */

    return jQuery.Deferred( function( newDefer ) {
      // 向回调栈添加回调。
      // fulfilled_handlers.add( ... ) 
      tuples[ 1 ][ 3 ].add(
        resolve(
          0,
          newDefer,
          onFulfilled 
        )
      );
      // 向回调栈压入错误处理回调
      // rejected_handlers.add( ... )
      tuples[ 2 ][ 3 ].add(
        resolve(
          0,
          newDefer,
          isFunction( onRejected ) ?
            onRejected :
            Thrower
        )
      );
    } ).promise();
  }
```

可以看出then方法返回了一个新的promise实例。为什么看下面分析。
then 将相应的回调用resolve包装起来压入栈中，因为压入栈中的只能是回调函数，所以可以预见resolve函数肯定会返回一个函数。
通过对thenAPI的分析，我们可以猜测resolve的内部实现：

1. 肯定会执行我们传入的回调函数，以达到对最基本的回调对接。
2. 肯定会对我们传入的回调的返回的类型进行特殊处理，以达到根据返回的是否为promise实例判断是否执行下一个回调栈的函数。

```js
  function resolve( depth, deferred, handler, special ) {
    return function() {
      // 下段js说明
    }
  }
```

这里说下这种结构，一个函数返回另一个函数，这是种典型的闭包。返回的函数可以拿参数来做逻辑的判断，亦可以改变参数的值，来达到下一次的调用跟上次逻辑不同的作用。所以看源码的时候需要注意。自己写的时候也可以这么用，但是不建议太过复杂，可读性太差。维护是个麻烦。

```js
  // 这里对内部实现进行了精简，只写核心的代码，解决我们关心的问题
  function resolve(depth, deferred, handler) {
    return function() {
      var args = arguments;
      var res = handler.apply(this, args);
      if (res.then) {
        res.then(
          (resThenResolveValue) => {deferred.resolveWith(this, resThenResolveValue)},
          (resThenRejectError => {deferred.rejectWith(this, resThenRejectError)},
        )
      } else {
        deferred.resolveWith(this, res)
      }
    }
  }
```

为什么要返回一个新的promise。这主要是为了实现👆的2. 如果不返回新的promise而是继续使用原来的promise。那么只要promise一旦把状态改为完成，那么成功回调会全部触发。因为回调都统一放在promise的闭包里，不好控制啊。new Promise().then(a).then(b)。a 和 b 都会触发。而因为因为then返回的是一个新的promise实例。上面的第二个then添加的b回调就会放在新的闭包里，我们可以很好的控制是否触发。从而轻松的通过判断a的返回来觉得是否执行下一个回调。

## 总结

promise 实现在then中通过放回promise来阻止回调栈的继续弹出的逻辑，是通过在then的实现中返回新的promise实例将后面通过then再添加的回调放到新的实例闭包中，然后当监听被触发之后判断返回的是否为promise来判断是否触发新实例的成功事件，还是等待。
这种通过将本来存在同一个闭包中的栈拆到不同闭包，再通过上一个闭包判断是否触发下一个闭包函数的思想简直厉害。d=====(￣▽￣*)b厉害
