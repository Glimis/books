# modal

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

但此处并没有处理复用,逼近,复用是"框架"需要处理的



