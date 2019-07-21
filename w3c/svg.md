# SVG 随便学学就会了

> 这两天闲来没事把 Echart 换成 Rechart 感觉世界都清爽了。因为 rechart 使用 svg 来渲染，所以顺带学了下 SVG 感觉很轻松哦。

## 概念

SVG 是 w3c 的退出的规范。(就是 w3c 指定语法。各大厂商自己去实现)。SVG 渲染的是矢量图(它之规定了图像怎么画，而不是规定那个像素画什么颜色)。

## 优点

因为以上两点（w3c, 矢量图）所以他有兼容性好，不失真的优点。除此之外还有：

使用xml的格式规范(HTML 也是)。**在浏览器上基本上可以看做是DOM节点来操作**。（事件，渲染样式）
svg 不同于位图指定每一个像素的渲染，内部其实是用简介的语法指定图应该从哪画到那怎么画。**本质上就是文本**，所以很小。

这些优点决定了他很适合在**前端画一些简单的图**。（画复杂的就算了交给设计师吧）

## Getting Started

在现在的浏览器使用 SVG 太简单不过了。只需要在HTML中直接写

```html
  <!-- version prop in svg will be Deprecated -->
  <!-- xmlns is unnecessary for inner svg elements or inside HTML documents. -->
  <svg width="100" height="100">
    <line x1="0" y1="0" x2="100" y2="100" stroke="red"></line>
  </svg>
```

* 可以清楚的看到 svg 的语法。svg 是 xml 的语法格式。svg 是定义一个容器。其定义一个图的大小。图的内容由其子元素来确定。

## \<svg> props

* width | height 定义元素的大小

与 canvas 不同，width 和 height 是定义一个类似于窗口的显示大小，可以在窗口外画东西，只是你一般看不到。可以是svg显示这个宽口外的图，后面讲。

## svg 子元素

画图总离不开一些简单的图形，svg 对这些进行了封装，成为以下的标签。

`<line x1="2" y1="4" x2="4" y5="7"></line>`: 两点确定一条线段，x1,y1,x2,y2 就是指定这两个点的。
`<rect x="2" y="4" width="100" rx="4" ry="4" height="100"></rect>`: 画长方形的，需要注意的是rx ry 是用来画带圆角的长方形的。就像css的border-**radius**;
`<circle cx="13" cy="13" r="40"></circle>`：cx, cy 确定圆心，r为半径画一个圆。
`<ellipse cx="0" cy="0" rx="132" ry="test"></eliipse>`: 这个是画椭圆，rx, ry 是定义椭圆的x半径， y轴半径的。
`<polygon points="0,0 3,3 4,4"></polygon>`: 等价于你拿着笔按顺序画过经过的 points 中的各个点，最后回到第一个点。围起来的图形。

--- 以上就是一些常用的简单图了 ---

## 曲线（最复杂）

这个是普通 HTML CSS 没办法做到的。所以这个是学 SVG 中最高性价比的东西。

直接上例子

```HTML
  <!-- 画一条从（0，0）到（50，50）的线 -->
  <svg width="100" height="100">
    <path d="M0 0 L50 50"></path>
  </svg>
```

可以看到标签是path，d为属性。属性d的值使用 Commandx y 这样的语法格式。(注意Command后直接跟参数没有空格)

|指令|参数|说明|
| :---: | :---: | :---: |
|M|x y|将点移动到 (x,y) 经过不划线|
|L|x y|将点移动到 (x,y) 并划线|
|H|y|将点的y坐标做变化x不变，并划线|
|V|x|将点的x坐标做变化y不变，并划线|
|说明|x,y|上面的参数都是绝对值，线面都是相对值|
|m|参数都一样|将点的个个坐标都添加上x,y的值，作用跟M一样|
|l|参数都一样|将点的个个坐标都添加上x,y的值，作用跟L一样|
|h|参数都一样|将点的个个坐标都添加上x的值，作用跟H一样|
|v|参数都一样|将点的个个坐标都添加上y的值，作用跟V一样|
|z|没有参数|将点移动到起点，并划线|

（妈妈（M）拉（L）我看湖（H）南卫（V）视。真是智（Z）障。

下面真的搞曲线了

接触过曲线的都知道贝塞尔曲线，SVG 画曲线也玩不出花来肯定是用[贝塞尔曲线](https://zh.wikipedia.org/wiki/%E8%B2%9D%E8%8C%B2%E6%9B%B2%E7%B7%9A)的。

[Codepen](https://codepen.io/ahole/pen/rXVmWK?editors=1000)

```HTML
  <svg width="100" height="100">
    <path d="M0 0 Q0 100, 100 100"></path>
  <svg>
```

画贝塞尔曲线一般需要3个参数，但是画图的语言，一般会记录上一个点，所以SVG的命令就是 `Qx y,targetX targetY`。

|指令|参数|说明|
| :---: | :---: | :---: |
|Q|x y,targetX targetY|以(x, y)为切点，上一个点位起点，(targetX, targetY)为终点画一条贝塞尔曲线|
|T|targetX targetY|以上条贝塞尔曲线的弧度画一条到(targetX, targetY)的贝塞尔曲线，如果上一条划线不是贝塞尔那就是画一条直线|

腾讯QT语音的两个字的灵感不会是从这里来的吧。

当然还有三次贝塞尔曲线的。语法类似。

|指令|参数|说明|
| :---: | :---: | :---: |
|C|x y, x2 y2,targetX targetY|以(x, y),(x2,y2)为3次贝塞尔的曲线参数，上一个点位起点，(targetX, targetY)为终点画一条贝塞尔曲线|
|S|targetX targetY|类比上一个|

用QT语音打CS？？？？

## 样式的定义

SVG画的线部分已经结束了，接下来我们上色，跟描边。

上面的全部标签都可以分为两部分，一部分是线，已经线包围起来的内容。

线的样式可以在标签上用stroke来定义, 包围起来的内容用fill。

* stroke 有 stroke stroke-width stroke-linecap stroke-opacity等
* fill 有 fill fill-opacity fill-rule 等

[svg fill & stroke](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Tutorial/Fills_and_Strokes)

```HTML
  <svg width="100" height="100">
    <circle
      width="100" height="100" x="0" y="0"
      stroke="red" stroke-width="2" stroke-linecap="round" stroke-opacity="0.8" stroke-linejoin="round"
      fill="green" fill-opacity="0.8"
    ></circle>
  </svg>
```

## 图形的复用

就这么老老实实的搬一般就够用了，但是这程序最重要的就是少搬砖，多偷懒。

有时候我们可能一个图中有了很多个相同的图，这时候就copy就惨了。偷懒第一招。

```HTML
  <svg width="100" height="100">
    <defs>
      <g id="ahole">
        <rect x="0" y="0" width="50" height="50" id="rect1"></rect>
        <circle
          width="100" height="100" x="0" y="0"
          stroke="red" stroke-width="2" stroke-linecap="round" stroke-opacity="0.8" stroke-linejoin="round"
          fill="green" fill-opacity="0.8"
          id="circle1"
        ></circle>
      </g>
    </defs>
    <use href="#ahole"/>
    <use href="#rect1" x="123123"/>
    <!-- you can add any props you want to replace the svg element you used -->
    <use href="#circle1" y="123123"/>
  </svg>
```

解析：defs 就是定义声明不会在svg中划出。g是将其子元素统统打包，方便use的时候使用。use 就是调用啦，通过href：#id的方式来指定使用哪一个小图形，你可以在use的标签中使用任何属性来替掉你use的标签原来的属性。这里要说明，use 不一定只能应用 defs 中定义的，只要在上下文前，有用id命名的都可以。

偷懒第二招

上面这招只能在同一个svg中使用，但是我们想在每一个svg中使用呢。比如UI库中的icon可能有大量的svg，这是用引用一个就画一个svg显然有点浪费。

```HTML
  <svg style="display: none;">
    <symbol id="beaker">
      <!-- <path>s and whatever other shapes in here -->  
      <rect width="40" height="100" x="0" y="0" fill="red"></rect>
    </symbol>
  </svg>

  <svg width="100" height="100" style="border:solid 1px green">
    <use href="#beaker"></use>
  </svg>
```

解析：把包含symbol的svg隐藏，并注入大量要使用的svg图，后面的svg就可以简单的通过use来应用了。（这里的svg版本都是最新的，以前版本的语法可能需要xmlns:href等复杂的写法，我选择拥抱最新）

## 与CSS的结合

我们可以通过给svg元素添加class，并在 css 中设置样式。

[🌰](https://codepen.io/ahole/pen/eqNjYX)

```HTML
  <css>
  .rect {
    fill: green;
    x: 20px;
  }
  </css>
  <svg fill="red">
    <rect x="0" y="0" width="100" height="100" class="rect"></rect>
  </svg>
```

个人建议：css 虽然可以设置定位的属性，但是最好还是不要使用定位，不然维护起来会很麻烦，尽量只是用css来定义颜色样式。另外结合 css3 的 transform 和 animation 能实现很好的动画效果。

总结: 入门款就是这个样子了，后面有时间再跟新进阶版。包括 preserveAspectRatio filter text 动画等知识。

<!-- ## Canvas & Viewport & preserveAspectRatio

## svg <filter>

## text

## svg 动画 -->
