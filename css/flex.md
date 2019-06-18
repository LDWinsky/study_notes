# flex 布局快速上手

flex 的布局个人感觉就是为了对付日益增长的布局需求出来的一系列特别熟悉。让我们这些小的们可以高效的布局。

# 应用在父元素的flex属性

## display: flex | flex-inline

基本上就只会使用flex, flex-inline 太少见了不谈。

这个属性的作用是为了开启flex的布局。使用后他还有他的子元素就可以开始我们的玩耍了。

## flex-direction: row | column | row-reverse | column-reverse

顾名思义这是个规定其子元素布局方向的属性，见鬼的是其不仅能指定布局的向右，还能从下往上`column-reverse`。
注意如果不设置其子元素宽度并flex-direction === row; 那么子元素的布局就像子元素的display是inline-block一样。

## flex-wrap: wrap | nowrap | wrap-reverse

换行的问题。其中两个很明显就知道什么意思，但是还有其知识点，我们结合到后面的数据一起说。最后一个就不是很懂了。他是这样的，如果溢出，溢出的部分往相反的方向布局。如果direction是row的话，溢出后的效果就是，溢出的哪行在上面，原来的在下面。

## 还有三个属性 align-items, align-content, justify-content

> 很三个很难记住。justify-content 定义一行内如何显示元素。 align-items 定义以什么基准对齐一行就像vertical-align一样。align-content 像是定义全部行如何布局的属性一样。

### just-content: flex-start, flex-end, center, space-around, space-between;

前三个一看就知道，space-around 和 space-between 都是把一行中剩余的空间填补到元素中间，最大的不同的space-around会在开头和结尾分配空间。但是左右两边的空间明显只有中间间隔的一半

### align-content: flex-start, flex-end, center, space-around, space-between, stretch

把它当成纵向的 just-content 就行了，不过他控制的是纵向的行。默认值是stretch。默认值就是把空间全部填满的操作，其他的跟just-content在横向的作用基本一致。

### align-items: flex-start | flex-end | center | baseline | stretch;

作用在一行上，效果为对齐。stretch为把那一行填满。

* 如果觉得难记可以用我的方法，justify-content 联想到 text-align: justify 也就是一行如何处理其空白。 align-content 联想到 content 就是处理一整块的内容，justify 是定以横行的，那么这个肯定是定义一整块在水平的布局。剩下的 align-items 就只能联系 vertical-align 啦。

# 应用在子元素上

## flex: [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]

一般我们使用就只会只有这个缩写。

### flex-grow

我们知道 justify-content 没有 stretch 这个选项，如果想把内容填满就需要这个属性。 他是一个比例。比如如何行剩下10个单位。并且那一行的全部 flex-grow 合为10； 那么一个flex就是1；这样来加多少长度；

### flex-shrink

如果内容不够放又不能换行呢。就是用shrink啦。同样的算法只是这次是减少

### flex-basis 

这个很少用，感觉效果同 width 相同；

### align-self

可用的属性值跟 align-items 一样，效果也一样，这是这个是来定义自己的。

(完)


