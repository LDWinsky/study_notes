# grid 快速记忆

## 属性

* 为了快速记忆，去掉少见的复合型属性

### display: grid | inline-grid

不像display flex； grid 设置完后不加其他属性是不会有多大变化的。
只是设置完 float、display: inline-block、display: table-cell、vertical-align 和 column-* 这些属性都会失效，这样考虑的问题就少多了。

### grid-template-[columns|rows]

grid-template-columns 和 grid-template-rows 分别定义横向和纵向分成几块，每一块多长。组合起来就想分田地一样吧容器元素分成若干块正方形的区域。

比如 
```css
  grid-template-columns: 100px 200px 300px;
  grid-template-rows: 300px 400px 500px;
```

无论父元素的长度是太长还是太短，都会分配出来这样的分区。长度不够，溢出来布局, 可以配合使用overflow。长度太长，多出来的不参与布局。

设置的长度单位： 长度，百分比，auto，***fr;

长度跟百分比都很好理解。这里讨论 auto 跟 fr。

fr 可以类比 flex 的 flex-grow; 当没有 fr 的时候，基本上 auto 就可以看成是fr。
而当 fr 跟 auto 混用的时候就有意思了。auto的哪行就会根绝其原始的大小来设置长度。fr意思不变。也就是说内核的算法是先计算 auto 的长度再计算fr应该是多长。

repeat 是一个解决 grid-template-columns: 100px 100x 100px ... 连写100个的麻烦情况。例子： `grid-template-rows: repeat(100, 100px)`; repeat 函数会把第二个到第N，按照顺序写100遍。

通过上面的属性，你大概明白grid是先划分布局的网格在填充内容。这就会出现没办法达到我们最简单的float根绝盒子的长度自动调节一行放多少的子元素的布局。这时候就需要使用`grid-template-rows: repeat(auto-fill, 100px)` 来实现。auto-fill 会自动计算多少个能放在一行里自动变化他的值。

### grid-[row|column]-gap

定义分区间的间隔 一般使用 grid-gap: row-gap, column-gap;

### 定义分区内容布局的属性

大量使用flex的概念，不熟悉的出门左边

* align-items 类比于 flex 的同名属性，只是这个没有baseline了。这个是定义子元素在所属网格中上下的位置。start, end, center, stretch

* justify-items 类比于 align-items 看到justify就是到时很向的布局。

* justify-content 当整块内容没办法填满容器的时候使用。类比与 flex 的justify-content;

* align-content 同上，不过是应用于垂直的

## 应用在子元素的属性

### 区域内布局

* justify-self 和 align-self 分别是 justify-items 和 align-items 对子元素的版本。如果每个子元素都设置同一个属性，那就直接这是在父级就好了。

* place-self: algin-self / justify-self

## 不安分的布局

grid 不仅能一个子元素放在一个划分好的格子里，还能够自由的定义一个元素在多个格子。（其实很少用我觉得）是他的一个特点，看一下吧。

### grid-[column|row]-[start|end]

最直观的设置方式就是在一个子元素中定义其占领的区域的开头和结尾。(start + end) * (column + row) 所以需要4个值来指定。

```css
  .son {
     /* 第一条线开始 */
    .grid-column-start: 1;
    /* 到开始后满一格 */
    .grid-column-end: span 1;
    /* 从 line-name 行开始 */
    .grid-row-start: line-name;
    /* 知道 line-name 行结束 */
    .grid-row-end: span line-name;
  }
```

注释有说明，但是这个 line-name 需要讲一下。 line-name 顾名思义就是划分区域时线的名称，定义是在 grid-template 中定义的。如： `.grid-template-columns: [line-1] 200px [my-line];` 定义开始的先就是line-1, 第二条线就是my-line; 这里定义的名字就是在定义子元素占领区域时使用的。

### grid-template-areas and grid-area

像上面那样定义其实很麻烦，因为每次都要算数，要知道算数不是人干的。所以css的开发者提供了一套更可视化的方式。通过grid-template-areas 定义区域的名称，通过 grid-area 来定义子元素使用的区域。

```css
  .beach {
    grid-template-columns: repeat(3, 200px);
    grid-template-rows: repeat(3, 200px);
    /* 形成一个3*3的9宫格，自行脑补  */

    /* 图形化的区域命名 */
    grid-template-areas: 
      "header header header"
      "sidebar article article"
      "sidebar article article"
  }

  .son-of-beach {
    /* 这个子元素占领了全部header标记的区域 */
    grid-area: "header"
  }

```

### grid-auto-[columns|rows]

因为我们可以通过 grid-column-start 等属性来定义布局的位置，那么就存在定义的区域不在template定义的区域内。这种情况开发者考虑到了，并且开放了这样的定义。只是只有在定义的区域内有宽高，未定义的都设置为0；那么就可能出现布局区域只有宽高中的一项，或者两项都没有（自行画图理解）。
例子是css中就好的导师。

```css
  .beach {
    grid-template-columns: repeat(3, 200px);
    grid-template-rows: repeat(3, 200px);
  }

  .son-of-beach {
    grid-column-start: 100;
    grid-column-end: 101;
    grid-row-start: 1;
    grid-row-end: 2;
  }
```

图上的 .son-of-beach 第一印象他会布局的很远，但其实不然，因为布局在其左边（想象）的盒子没有宽度。所以实际其计算出来的位置就在第一行的第4的位置。

说了那么多就想说 grid-auto-[columns|rows] 这个数据就是为了定义这些不在template定义里的宽高。`grid-auto-columns: 10px` 这是完。就看不到 son-of-beach 了

## grid vs flex

* flex 是定义一个元素中一行的布局。具体布局出来长什么样子，很大程度上决定于子元素属性的设置。grid 更像是先规定布局的格子，再让元素去逐个填坑。所以如果想设置一行的布局，使用flex来布局就可以了。

* grid 设置更多的属性在Parent. 而flex设置更多的属性在子元素。

* grid 支持 gap 。flex 不支持。

* grid 可以通过设置area来定义在平面图中的区域。flex 实现比较困难。

## 注意的点

* 跟flex的大致与别在与，grid不仅仅可以定义元素在其格子内的属性，还可以定义其占领别的领地

* 使用一个子元素占用多个格子时，如果发现多个盒子占用相同的格子，这时候是重叠的，可以使用z-index来选择显示的那个。

* 子元素不会用float,vertical-align 等属性。

## 兼容性

flex 基本上不用考虑兼容性了。主流浏览器都兼容。 grid 有Opera mini, IE 11 不兼容。我已经考虑转向专门写应用给主流客户的应用型开发人了，不再考虑这种鸡毛事情。

(完)