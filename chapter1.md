# z-index {#httpswwww3orgtrcss2visurenhtmlz-index}

横向x\(overflow-x\),纵向y,空间z,简单的说z-index就是堆叠顺序,更高堆叠顺序的元素总是会处于堆叠顺序较低的元素的前面。

其具体规范包括

* [https://www.w3.org/TR/CSS2/visuren.html\#z-index](https://www.w3.org/TR/CSS2/visuren.html#z-index)
* [https://www.w3.org/TR/css-position-3/\#propdef-z-index](https://www.w3.org/TR/css-position-3/#propdef-z-index)

## 窗口元素

select,老版iframe,object等,其本身为一个独立的窗口,不必遵守当前窗口的渲染规则

感受一下:

![](/assets/import.png)

![](/assets/select2.png)

浏览器都能打穿,更不用说其他元素,可以使用div等元素替换这种性质,但是相应的,也需要遵守z-index的规则

select\(combobox\)组件的创建方式,也决定的其使用场景\(见实例影响\)

# 实例影响

1. 自定义下拉![](/assets/combobox.png)以上为iuap下拉实现的方式,



