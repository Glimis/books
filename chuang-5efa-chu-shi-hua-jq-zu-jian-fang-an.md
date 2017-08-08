此处单指调用,初始化,即'api'设计

## 标签占坑+config

js即组件,组件内部实现一切信息,撑死允许在标签中设置class\(修改皮肤,但没卵用\),一般标签不具有任何意义\(默认div\),使用自定义标签亦可正常运行,使用`textarea`的去死

如地图,chart图等,对ui要求不大的插件

## 自定义属性

html即组件,通过自定义属性配合委托 or "定时"初始化进行组件初始化

如bootstrap系列

```
<button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="left" title="Tooltip on left">Tooltip on left</button>
```



