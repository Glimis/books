## \(jq\)组件

传统意义上的组件,组件的内部包含事件,函数与属性,通过少量的js进行初始化,一般不包含html,即由js配合固定的html取代view,即是有html,也仅作初始化数据\(配合后端渲染\),如

[datatables](http://www.datatables.club/example/diy.html)



然而...



### 灵活性低

这是新版的展示风格,来撸一个



不要说不适合的话,第一版的时候与PM直接沟通,非常适合,此处为追加ui/ue/ux后,出的第一版效果图,注意措辞,第一版

整体思路是通过固有api进行修改,比如进度,可以使用如下方式进行拼接

```js
{
    targets: 1,
    data: null,
    title: "",
    render: function (data, type, row, meta) {
        //拼接html
        var labels = "";
        if (row.labels.length) {
            for (var j = 0, labelslen = row.labels.length; j < labelslen; j++) {
                labels += "" +
                        row.labels[j].name + "";
                if (j != labelslen - 1) {
                    labels += ",";
                }
            }
        }
        return labels;
    }
}
```

至于整体css,可以通过以下方式进行修改

```css
table.dataTable tbody tr {
    background-color: white;
}
```

感觉,我用js描述了每一个单元格\(render\),使用的还是拼接的方式,还不如直接写table-.-

### 附加功能过多

没必要的功能太多,此处指引入脚本过大

### 学习周期长

依赖文档,依赖文档,依赖文档

### iuap

没错,datatable~~代~~明指的就是iuap,其bug修复的速度与出错的方式,你懂的

### 初始化时间长

过于依赖js\(datatable可解决,特指iuap\),有理论白屏时间

## 轻量级组件

再次强调bootstrap下的组件的概念,组件有别于js组件,即此处组件可叫ui组件/css组件,没有js,那么

* 灵活性,足够
* 学习周期,有吗
* 初始化时间,与css/html加载时间同步\(无视渲染时间\)
* 附加功能,没有

什么都好,但没有js









