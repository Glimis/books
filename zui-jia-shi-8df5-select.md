以vue为例,ko同

## 纯选择

select最经典场景,基于kv,对具体属性赋值,使用v-model即可,当初始化v-model的内容不存在options时,不选中任何选项,当更改v-model的内容不存在options时,强制转换为undefined,即

* 允许初始化不在选项内\(未做关联\)
* 不允许设置v-model非options下的内容

```js
  <select v-model="s1">
      <option value="value" v-for="(item,i) in list" :key="i" :value="item.value">{{item.name}}</option>
  </select>
```

需要注意以下情况

* list异步加载对model初始化的影响
* v-model内容值,并不存在list时的影响

当内容不存在select中时,部分组件会强制将v-model设置为undefined,异步加载时,也不会正确的选中对应值\(iuap\)

[https://github.com/Glimis/practice/tree/master/select](https://github.com/Glimis/practice/tree/master/select)

## 参照

对应的为ko\(key/object\),为最常用的情况,在展示时,选项所对应的具体text...真没几个后台愿意做关联

```js
<select v-model="s1">
  <option value="value" v-for="(item,i) in list" :key="i" :value="item">{{item.name}}</option>
</select>
```

注意点同纯选择

[https://github.com/Glimis/practice/tree/master/select-ko](https://github.com/Glimis/practice/tree/master/select-ko)

## 参照-修改

未选中修改,选中年龄,选中会消失,选中选项,修改年龄,选项信息会跟随改变,即

* v-model与options做值SameValueZero,本身也不会保留function
* v-model选中后,会直接获取option的指向

即,对于以下交互,需要配合change函数进行处理,主要解决一下情况

#### 可修改内容,参照根据修改一同改变

如联系人选择后,修改电话,此处要求,参照中的电话信息为最新\(最后一次修改\)内容

可使用v-model,但对于老电话,无法找到正确options,即

#### 参照根据options.id or 关联主键进行选中判断

同样的,也包括

#### 可修改内容,参照不可改变

如评分模板,修改内容与参照无关,再次选择参照会重置修改内容

[https://github.com/Glimis/practice/tree/master/select-ko-edit](https://github.com/Glimis/practice/tree/master/select-ko-edit)

## 参照-修改-解决

#### change

```markdown
<select  @change="change($event,list)">
    <option value="value" v-for="(item,i) in list" :key="i" :value="item.name">{{item.name}}</option>
</select>
   
//...         
change(e,list){
  var val = e.target.value;
  this.s1.name=val;
  var obj = list.find(function(item){
    return item.name ==val;
  })
  Object.assign(this.s1,obj)
}
```

MVC风格,当然使用change后

* 直接修改v-model的内容,无法正确指向选中
* e.target.value不可为undefined

#### watch

```
<select v-model="s2.name" >
  <option value="value" v-for="(item,i) in list" :key="i" :value="item.name">{{item.name}}</option>
</select>

//...
watch:{
  's2.name'(val){
      var obj = this.list.find(function(item){
        return item.name ==val;
      })
      Object.assign(this.s2,obj)
  }
}
```

常用,可配合函数使用

#### change+v-model

代码略

较特殊,应对主动选择会触发修改,但手动修改时,不进行修改的蛋疼需求

### 通用函数

自行空判断

```js
function change(val,sourceName,key,mappingName){
    var source = this[sourceName],
        mapping = this[mappingName];
    var obj = source.find(function(item){
      return _.get(item,key) ==val;
    })
    Object.assign(mapping,obj)
}
```

使用

```js
watch:{
            's2.name':_.partial(change,_,'list','name','s2')
     }
```

[http://127.0.0.1:60001/practice/select-ko-edit-change/index.html](http://127.0.0.1:60001/practice/select-ko-edit-change/index.html)

### 总结

以上也是大多数框架并不允许\(推荐\)options.value为一个对象的原因

# 下拉变种

## 参照-弹出框

下拉内容多时,可直接转换为弹出框模式,用以分页

## remote

动态获取数据源,根据输入的关键字,获取数据,用以快速选择

## refer组件

弹出框与remote的合体组件,与后台接口有关



