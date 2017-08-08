此处单指调用,初始化,即'api'设计

# 方式

## 标签占坑+config

js即组件,组件内部实现一切信息,撑死允许在标签中设置class\(修改皮肤,但没卵用\),一般标签不具有任何意义\(默认div\),使用自定义标签亦可正常运行,使用`textarea`的去死

如地图,chart图,甚至grid,特指对ui要求不大的插件

## 自定义属性

html即组件,通过自定义属性配合委托 or "定时"初始化进行组件初始化

如bootstrap系列

```
<button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="left" title="Tooltip on left">Tooltip on left</button>
```

技术点包括

* 委托

通过`on('data-toggle="tooltip"','click',()=>{})`对节点赋予事件,以不修改ui为主

* 初始化

通过`$('ata-toggle="tooltip"').tooltip()`进行初始化,可能会涉及到ui\(节点\)的变化

## WebComponent

自定义标签,默认为MVVM的实践,jq很少涉及,具体参考相应框架api

# 特性

## 初始化数据/html化/后端渲染

后端组件的必填项,当第一波数据由后端渲染时,直接生成html标签,用于做数据的初始化

关于后台数据:拒绝将数据刷至js中,可刷至input中,js读取input

如`select`系列组件

```
<select id="cc" class="easyui-combobox" name="dept" style="width:200px;">
    <option value="aa">aitem1</option>
    <option>bitem2</option>
    <option>bitem3</option>
    <option>ditem4</option>
    <option>eitem5</option>
</select>
```

注:对于大多数组件,这只是初始化数据获取的一种方式,组件初始化后,对相应节点修改并不会影响数据源,即

第一次扫描节点-》初始化数据源,之后仅能通过事件/函数修改数据源

## 模板

组件占坑会变更整体结构,相对于通过js创建dom,模板的方式对视图与控制的区分更明确

```js
var defaultView = {
	render: function(target, container, data){
		var state = $.data(target, 'combobox');
		var opts = state.options;

		COMBOBOX_SERNO++;
		state.itemIdPrefix = '_easyui_combobox_i' + COMBOBOX_SERNO;
		state.groupIdPrefix = '_easyui_combobox_g' + COMBOBOX_SERNO;		
		state.groups = [];
		
		var dd = [];
		var group = undefined;
		for(var i=0; i<data.length; i++){
			var row = data[i];
			var v = row[opts.valueField]+'';
			var s = row[opts.textField];
			var g = row[opts.groupField];
			
			if (g){
				if (group != g){
					group = g;
					state.groups.push({
						value: g,
						startIndex: i,
						count: 1
					});
					dd.push('<div id="' + (state.groupIdPrefix+'_'+(state.groups.length-1)) + '" class="combobox-group">');
					dd.push(opts.groupFormatter ? opts.groupFormatter.call(target, g) : g);
					dd.push('</div>');
				} else {
					state.groups[state.groups.length-1].count++;
				}
			} else {
				group = undefined;
			}
			
			var cls = 'combobox-item' + (row.disabled ? ' combobox-item-disabled' : '') + (g ? ' combobox-gitem' : '');
			dd.push('<div id="' + (state.itemIdPrefix+'_'+i) + '" class="' + cls + '">');
			if (opts.showItemIcon && row.iconCls){
				dd.push('<span class="combobox-icon ' + row.iconCls + '"></span>');
			}
			dd.push(opts.formatter ? opts.formatter.call(target, row) : s);
			dd.push('</div>');
		}
		$(container).html(dd.join(''));
	}
};
```

easyui-combobox,render函数,主要用于生成html

```js
   {
   //..
   fieldSubTpl: [
      '<div class="{hiddenDataCls}" role="presentation"></div>',
      '<input id="{id}" type="{type}" {inputAttrTpl} class="{fieldCls} {typeCls} {editableCls}" autocomplete="off"',
      '<tpl if="value"> value="{[Ext.util.Format.htmlEncode(values.value)]}"</tpl>',
      '<tpl if="name"> name="{name}"</tpl>',
      '<tpl if="placeholder"> placeholder="{placeholder}"</tpl>',
      '<tpl if="size"> size="{size}"</tpl>',
      '<tpl if="maxLength !== undefined"> maxlength="{maxLength}"</tpl>',
      '<tpl if="readOnly"> readonly="readonly"</tpl>',
      '<tpl if="disabled"> disabled="disabled"</tpl>',
      '<tpl if="tabIdx"> tabIndex="{tabIdx}"</tpl>',
      '<tpl if="fieldStyle"> style="{fieldStyle}"</tpl>',
      '/>',
      {
      compiled: true,
      disableFormats: true
      }
      ],
   }
```

ext-combobox中,render中的tpl

## JSX

模板的另一种格式

如grid,大多数时候单一,但对于部分单元格又会有个性化需求,大多数情况下使用html进行完成,如

```js
$("#issueTable").DataTable({
    ajax: {
        url: "https://api.github.com/repos/ssy341/datatables-cn/issues",
        dataSrc: ""
    },
    //默认最后一列（最后更新时间）降序排列
    order: [[ 2, "desc" ]],
    columnDefs: [
        
        {
            targets: 1,
            data: null,
            title: "发表人",
            render: function (data, type, row, meta) {
                return "<a href='" + row.user.html_url + "' target='_blank'>" + row.user.login + "</a>"
            }
        }]
})
```

以上为datatable对于个性化设置的解决方案,宽松点说也算是小模板...

如果使用了jsx,即可,如

```js
{
             
    columns: [
        {
            title: '姓名',
            key: 'name'
        },
        {
            title: '电话',
            key: 'phone'
        },
        {
            title: '职责',
            key: '_id',
            render:(h,params)=>{
                var clazz;
                if(params.row.clazz){
                    clazz='('+params.row.clazz+')';
                }
                return (
                    <div>
                        <p>{params.row.type}{clazz}</p>
                    </div>
                    )
            }
        },{
            title: '操作',
            key: '_id',
            render: (h, params) => {
                var self = this,
                    _id=params.row._id;
                return (
                    <div>
                        <Button size='small' type='text' onClick={() => {
                            self.$router.push({
                                path:`clazzs/${_id}/edit`
                            })
                          }
                        }>修改</Button>
                        <Button size='small' type='text' onClick={() => 
                            self.$router.push({
                               path:`clazzs/${_id}/show`
                            })
                        }>查看</Button>
                    </div>
                    );
            }
        }
    ]
}
```

一般jsx的模板,无论是语法还是作用域都有别于全局的\(VM\)模板,即jsx并不保证语法的通用,只是相似- 。-

