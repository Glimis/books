响应modal

[http://v3.bootcss.com/javascript/\#modals](http://v3.bootcss.com/javascript/#modals)

[http://www.runoob.com/bootstrap/bootstrap-modal-plugin.html](http://www.runoob.com/bootstrap/bootstrap-modal-plugin.html)

相对于其他组件,模态框最为特殊,主要在与

> 务必将模态框的 HTML 代码放在文档的最高层级内（也就是说，尽量作为 body 标签的直接子元素），以避免其他组件影响模态框的展现和/或功能。

其具体原因参考 z-index

## 实现

```
<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        <h4 class="modal-title" id="myModalLabel">Modal title</h4>
      </div>
      <div class="modal-body">
        ...
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div>


```

与其他jq组件一样,将以上代码至于body下,并使用$\(\).modal系列进行交互即可

各种MVVM系列的bootstrap的实现,大多数依然是使用相应的标签进行实现,并没有任何区别,比如

以上为elementUI,Vue版的dialog,换了个马甲而已

## 复用

对于可复用的modal,称为参照\(本公司\),最大特点在于

> 使用人不直接对modal进行样式的更改

当然,也包括使用组件化的方式,在写到页面底层组件时,响应的首页追加迷之依赖\(参照第一条\),尤其是单页面,如果使用vue全家桶,且有N多的modal,那基本上,根节点需要N多的,不知道可能在哪里会被引用的modal标签存在

参考几种老板弹出层的实现方式

### layer

jq时代,弹出界的扛把子

```
layer.open({
  type: 1,
  title: false,
  closeBtn: 0,
  shadeClose: true,
  skin: 'yourclass',
  content: '自定义HTML内容'
});

//iframe层-父子操作
layer.open({
  type: 2,
  area: ['700px', '450px'],
  fixed: false, //不固定
  maxmin: true,
  content: 'test/iframe.html'
});
```

其复用的方式依然是"经典"的自定义html内容,因为仅包含html内容,故弹出框所对应的事件与样式\(js/css\),需要额外的处理\(引用\)

第二种则是更"经典"的iframe复用,一个iframe,正好包括了弹出框所有要的html/js/css,且不涉及到

