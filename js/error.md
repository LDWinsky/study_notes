# JS Error 的笔记

> 我的JS启蒙书籍是高级程序色剂，里面对Error的描写是比较简单的，所以我对JS Error理解并不深入。

## Error 对象

Error 对象本身并没有属性。`Object.keys(error).length === 0`; 其属性跟方法都放在其原型中，很奇葩真是。

不同的浏览器对Error有不同的实现。其中最重要的有3个。name、message、stack。其中 name 、message 是w3c的标准，stack 不是标准，但是主流的浏览器都会支持。

### name

name 默认是其错误类型（下文会提及），可以被设置。会直接影响其 toString 的输出。

### message

message 包含了其报错的日志。

### stack

stack 可以看错是 message 的细节描述，其包含了执行链上的信息，可以看到上一个执行的函数等等信息。可以拿来做封装来地位报错文件的详细代码。

## 七种不同的Error对象

### Error

可以自定义一个Error对象。可以通过 new Error(message) 或者 Error(message) 产出。一般配合 throw 输出。其实理论上其他的Error类型也能自定义只是用得少。Error 是其他 Error 类型的继承对象。

```js
  var message = 'error message'
  var diyError = new Error(message)
  
  diyError.message === message // true
  diyError.name === 'Error'
```

### RangeError

这个类型一般用于对操作JS操作范围的错误进行报错处理。例如

```js
  try {
    new Array(10000000000)
  } catch (e) {
    e.name === 'RangeError' // true
    e // Uncaught RangeError: Invalid array length
  }
```

### ReferenceError

当出现变量在其上下文中未声明的时候的报错。

```js
  console.log(bar);    // Uncaught ReferenceError: bar is not defined
```

### SyntaxError

语法错误。
一般出现在少了一个括号等，低级错误上。这种情况一般不会对我们的程序有什么影响。

```js
  if () {)} // Uncaught SyntaxError: Unexpected token )
```

还有一种比较常见的就是 JSON.parse | JSON.stringify 。

```js
  JSON.parse('fawefa') // Uncaught SyntaxError: Unexpected token w in JSON at position 2
```

### TypeError

这个跟 SyntaxError 可能有点混淆。经常发生在错误把一个非函数值当变量使用的情况。

```js
  a = '123'
  a() // Uncaught TypeError: a is not a function
```

### URIError

當使用 encodeURI() 或 decodeURI() 的方法，但確有給了不合法的 URI 時會拋出這個錯誤：

### EvalError

抱歉不知道什么时候会输出。

## try catch finally

普通的用法可以查看[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch)。这里要说下其跟return有关的一些东西。

```JS
  try {
    return 'try';
  } catch(e) {
    return 'catch'
  } finally {
    return 'finally'
  }
```

无论如何都是finally。所以如果你要用return 的话建议就不要用finally了，反人类。但是 try catch 跟 return 配合还是写起来还是挺舒服的。

```js
  function jsonParse(string) {
    try {
      return JSON.parse(string)
    } catch(e) {
      return {}
    }
  }  
```

## 当 JS 遇到 Error 时

当 JS 遇到 js 会判断是否当前语句被包含在 try catch 中，如果是跑catch中的语句，然后在执行catch 之后的语句。如果不是那么 js 会退出执行栈。
浏览器退出执行栈不影响之前的操作，比如事件，对象的保存等。所以 JS 如果报错了，并不是整个程序都没有运行了。JS 还是会继续执行 event loop 的周期。
Node 默认情况下，只要发生没有被catch的报错，就会退出线程。
