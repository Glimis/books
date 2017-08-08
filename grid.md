### 初始化形式

封装以下内容

```
    <table>
      <thead>
        <th>姓名</th>
        <th>年龄</th>
      </thead>
      <tbody data-bind="foreach : list">
        <tr >
          <td data-bind="text:name"></td>
          <td data-bind="text:age"></td>
        </tr>
      </tbody>
    </table>
```

```js
  ko.applyBindings({
    list:[{
      name:'张三',
      age:11
    },{
      name:'李四',
      age:12
    }]
  });
```

table至少包括两个部分,thead,tbody,他是天生的复合组件,想用纯粹的自定义标签\(不适用js\),很难,他大概会长下面这个样子

```
<Table params="data:data">
  <Theadr>
    <Cell>姓名</Cell>  
  </Thead>
  <Tbody>
    <Cell data-bind="name:name"></Cell>
  <Tbody>
</Table>
```

写法上与原生没毛线区别...说到底,就是嵌套`Cell`,且作为grid的thead部分的变化,基本无需求,更常用的方式是将其转换为config

即是是使用config,也包含以下两种风格

```
<Table >
  <Thead params="config:header">
  </Thead>
  <Tbody params="config:tbody,data:data">
  <Tbody>
</Table>


<Table params="data:data"></Table>
```

第一种,组件化嵌套思路,table是复合组件,只引用其他组件,会在各种山寨的MVVM组件中可以遇见,更多的是纯标签的形式

第二种,虽然为复合组件,但tbody与thead为table的私有组件,属于tabler自行产生的分支,毫无复用性可言

此处选用第二种方式

注:thead通过js控制后,其内容无法第一时间显示,如有类似需求,需要转换创建方式,默认认为对tbody的展示时间更宽泛些

[https://github.com/Glimis/practice/blob/master/grid/index1.html](https://github.com/Glimis/practice/blob/master/grid/index1.html)

### config内容

```
<grid params="config:config"></grid>


<template id="#grid">
  <table >
      <thead>
        <tr data-bind="foreach : config.header">
          <th data-bind="text:title"></th>
        </tr>
      </thead>
      <tbody data-bind="foreach : {data:config.data ,as :'row'}">
        <tr data-bind="foreach : $parent.config.header">
          <td data-bind="text:row[$data.value]"></td>
        </tr>
      </tbody>
  </table>
</template>
```

```js
  ko.components.register('grid', {
      template: { element: '#grid' },
      viewModel: function(params){
        this.config=params.config;
      }
  });

  ko.applyBindings({
    config:{
      header:[{
        title:'姓名',
        value:'name'
      },{
        title:'年龄',
        value:'age'
      }],
      data:[{
        name:'张三',
        age:11
      },{
        name:'李四',
        age:12
      }]
    }
  });
```

雏形\(以上内容由侯姓后端提供\)

新需求,个性化单元格

[https://github.com/Glimis/practice/blob/master/grid/index2.html](https://github.com/Glimis/practice/blob/master/grid/index2.html)

### 自定义单元格

* 子组件

组件嵌套的标准思路,将tr内部改为自定义标签

```
<tr data-bind="foreach : $parent.config.header" >
  <td>
    <!-- ko component: {
        name: type,
        params: { row:row }
    } -->
    <!-- /ko -->
  </td>
</tr>
```

```js
{
    title:'姓名/年龄',
    type:'grid-cell-nameage'
}
```

* 动态生成\(render\)

```js
<tr data-bind="foreach : $parent.config.header" >
  <td data-bind="html:render(row)">

  </td>
</tr>
```

```js
{
  title:'姓名/年龄',
  render:function(row){
    return '<div><span >'+row.name+'</span>(<span>'+row.age+'</span>)</div>'
  }
}
```

编辑内容同理,加上ko.observable即可

新需求,排序,新旧对比...

[https://github.com/Glimis/practice/blob/master/grid/index3.html](https://github.com/Glimis/practice/blob/master/grid/index3.html)

[https://github.com/Glimis/practice/blob/master/grid/index4.html](https://github.com/Glimis/practice/blob/master/grid/index4.html)

### 数据源'弱'关联

排序很简单,`computed`产生新的数据源即可,但到了新旧对比\(已修改单元格标红\)就涉及到对数据源"双向绑定"到那种程度,两种方式

* 对数据源直接修改+备份老数据
* 参照数据源+对备份数据进行修改

使用`ko.observable`时第一种涉及范围过大,有性能问题,回头解释

#### 排序

```js
//新的vm对象
this.store = ko.computed(function(){
  //基于原始数据与列数据进行重新分配
  var data = self.data(),
      header = self.header;
  //简易排序
  var sort = _.filter(header,function(item){
    return item.sort();
  })

  return _.sortBy(data,_.map(sort,'value'));
})
```

使用新store做vm,方便排序

点击排序,更改,根据console可以推测,ko内部使用的是dom替换的方式而非重新render

[https://github.com/Glimis/practice/blob/master/grid/index5.html](https://github.com/Glimis/practice/blob/master/grid/index5.html)

#### 新旧对比

编辑时,需要将数据改为`observable`格式,用以监听对比

```js
//新的vm对象，丢至全局用于测试
window.storeObservable = this.storeObservable = ko.computed(function(){
  //基于原始数据与列数据进行重新分配
  var data = self._data = self.data();
  return _.map(data,function(item){
      var rs = {}
      _.each(item,function(val,key){
        rs[key]=ko.observable(val)
      })
      return rs;
  })
})
//封装监听的对象-->追加排序
this.store = ko.computed(function(){

  var data = self.storeObservable(),
      header = self.header;
  //简易排序
  var sort = _.filter(header,function(item){
    return item.sort();
  })
  return _.sortBy(data,function(item){
    return _.map(sort,function(obj){
      return item[obj.value]()
    })
  });
})
```

观察console,此时,修改源数据,将会render多次\(storeObservable对其监听\),而使用vm做内部修改,则只render一次\(修改当前行\),且只有通过内部修改的方式才会标红,然而又不会对外部数据源产生影响...\(有点绕口,看图说话\)

总之一句话,复杂组件如grid,其数据源为一个对象,再设计时,不能像简单组件一样直接进行双向绑定,会基于数据源,产生新的vmStore,渲染时甚至会使用依赖vmStore与vmConfig产生的新的vm用于做排序,分组等其他操作,此时,对grid的操作如修改,排序均不会对原数据源生影响,即`组件内部将data理解为model而后生成自己的vm`,故保存时需要注意使用以下api

```js
//将模型指向改为对应组件修改后的数据(vm)
data.list = element.data;
```

### 参考

[初始化组件方案](https://github.com/Glimis/books/blob/master/chuang-5efa-chu-shi-hua-jq-zu-jian-fang-an.md)

