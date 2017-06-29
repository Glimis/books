# z-index {#httpswwww3orgtrcss2visurenhtmlz-index}

横向x,纵向y,空间z,简单的说z-index就是堆叠顺序,更高堆叠顺序的元素总是会处于堆叠顺序较低的元素的前面。

其具体规范包括

* [https://www.w3.org/TR/CSS2/visuren.html\#z-index](https://www.w3.org/TR/CSS2/visuren.html#z-index)
* [https://www.w3.org/TR/css-position-3/\#propdef-z-index](https://www.w3.org/TR/css-position-3/#propdef-z-index)

## stacking order

> 1. the background and borders of the element forming the stacking context.
> 2. the child stacking contexts with negative stack levels \(most negative first\).
> 3. the in-flow, non-inline-level, non-positioned descendants.
> 4. the non-positioned floats.
> 5. the in-flow, inline-level, non-positioned descendants, including inline tables and inline blocks.
> 6. the child stacking contexts with stack level 0 and the positioned descendants with stack level 0.
> 7. the child stacking contexts with positive stack levels \(least positive first\).

此处所说的层叠顺序包括背景色,背景图,内容以及其他元素,具体分层如图

![](/assets/608782-20160923104742809-2054066790.png)

此处层叠上下文\(stacking context\),默认为body

简单的说

* 底色与背景图\(无子节点的元素\)

常见的黑底+图 

* 修改position后,立马变成z-index的比较 z-index

z-index的使用规范,如

[http://www.w3school.com.cn/cssref/pr\_pos\_z-index.asp](http://www.w3school.com.cn/cssref/pr_pos_z-index.asp)

* float 脱离文档流

本身用来做环绕效果,float至于block之上

若float兄弟节点修改为relative后,此时激活z-index,参照stacking order  demo3

## stacking context

层叠上下文是层叠顺序最底层的"画板",默认情况下为body,z-index也会创建一个新的上下文

故层叠顺序其实就是层叠上下文内的其他子层叠上下文与float,block,inline的关系

css3中增加了创建stacking context的方式,其具体的规则,参考

[https://www.w3.org/TR/css-position-3/\#propdef-z-index](#)

蛋疼的化,有中文的简化版

[https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Understanding\_z\_index/The\_stacking\_context](#)

总之与具体的css3样式的理念有关,不在详细描述

## 窗口元素

select,老版iframe,object等,其本身为一个独立的窗口,不必遵守当前窗口的渲染规则

感受一下:

浏览器都能打穿,更不用说其他元素,可以使用div等元素替换这种性质,但是相应的,也需要遵守z-index的规则

select\(combobox\)组件的创建方式,也决定的其使用场景\(见实例影响\)

# 实例影响

* 自定义下拉

以上为iuap下拉实现的方式,根据z-index的规则,当外框必须为overflow:hide时,就会出现各种意外,如

1. 模态框中的最后一行下拉
2. table中某个最后一列的单元格使用该组件

...

* tips与悬浮提示框

如果需要创建tips,默认会在哪一行?不放iuap,容易瞎

当然,每一个tips组件都会有一个根节点的概念\(默认body\),以此来保证tips会跟根节点保持滚动一直

* 模态框

如果使用组件的方式去描述整个页面,而在某个form表单中需要使用模态框,此时,如果依然在子组件内部描述模态框,依然容易瞎

模态框是最特殊的组件,开单章

# 参考

[http://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/](http://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/)

