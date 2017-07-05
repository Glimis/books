# 组件化

> 卡耐基梅隆大学：
>
> 一个不透明的功能实体，能够被第三方组装，且符合一个构件模型。
>
> 计算机百科全书：
>
> 是软件系统中具有相对独立功能、接口由契约指定、和语境有明显依赖关系、可独立部署、可组装的软件实体。
>
> 软件构件著作：
>
> 是一个组装单元，它具有约定式规范的接口，以及明确的依赖环境。构建可以被独立的部署，由第三方组装。

摘自卖假药的X度

总之,就是不同场景下公共服务\(代码\)的提取方案,比如包,模块,工具等理念一致,实现与运行场景有所区别,此处特指前端组件

## 前端组件

即如何优雅的区分/描述/写 html/css/js

根据不同实现\(时代\),对公共代码\(组件\)实现做以下区分

实现与场景有关,大部分实现可相互组合

### cv大法

CV镇楼

通用css与简单的js

ajax不流行,所有接口均由后台进行重定向,分页也是

只有java web,没有前端

也会有简单的封装,比如"轮播"通知,后台将数据至于js中or 隐藏的textarea中,前端获取数据,创建动态轮播效果,也可以后端渲染好结构,前端根据渲染的数据结构,在进行dom加工\(html化\),比如table,select,渲染为标准的table,select,但最终展现可能为div+css

* js

接口权限过大,使用选择器,太深\(精确\)的不想写,太浅的误伤率及高

默认使用业务类做顶级,但嵌套业务等...

* css

依赖选择器,同js,或者说js同css

* html

后台渲染结构\(传递数据\)+ js中拼接\(追加交互\)

#### 总结

如果没有脚手架,就用CV搭建初始页面

如果背不了api,想不起class,忘记了url...CV是编程的基础,你懂的

### iframe

frame\(frameset,iframe\)家族提出了多窗口的概念,允许我们将业务进行简单的分离

通过iframe嵌套也是常用的布局,比如dw下的各种经典布局,简单粗暴,还不带套

业务/技术均复杂的内容区也会由iframe嵌套,比如流程图,报表,图标,甚至地面勘探的详情

总之,iframe可以通过url获取一张有交互的图片

然而,iframe对seo并不友好\(没错,性能可以通过其他方式解决,但是seo...\),至此,依赖搜索的网站拒绝iframe,而传统erp...你懂的

如果不在意seo,使用iframe的理由最有意思:

\(当年\)用iframe,反正用不用大家都卡

\(现在\)用iframe,反正加载的东西有缓存

#### 总结

多窗口理念

各类地图,客服,尽量减少

### ajax

这不是解决方案,只是一个里程碑

看一个典型组件的实现

```
<select>
  <option>1</option>
  <option>2</option>
</select>
```

上面代表初始化数据

```
<span class="select2 select2-container select2-container--classic" dir="ltr" style="width: 100%;"><span class="selection">
  <span class="select2-selection select2-selection--single" role="combobox" aria-haspopup="true" aria-expanded="false" tabindex="0" aria-labelledby="select2-4b07-container">
    //...
  </span>
</span>
```

这个代表组件实现的结构

使用原生组件结构传递数据,通过js\($\(''\).select\(\)\),改变组件的样式与交互,达到封装具体css/js的目的,html化\(用原生组件传递初始数据\)的方式,是当时组件的一大特色

ajax+json是一个里程碑

数据,视图和控制的分离是实现\(现在\)组件的基础

此后,组件可以只有一种数据入口,setData,不用纠结于初始化数据与动态数据的区别,尤其是复杂的数据结构,不是原生组件能够描述的清的\(比如select,只能表示key,value\),当然,为了照顾后端渲染的方式,很多组件并没有直接放弃html化

### include

后端版的iframe,用以解决seo的问题

配合ajax使用,url返回已经做好处理的html

百度分页

博客园分页

#### 总结

* html

ajax返回html\(即include\)代码,可保证浏览器的效率,减少前端白屏/初始化时间,最重要的是,你不需要拼接字符串

### jq时代

种子文件/core/基础/工具...以上说法等价于jq

由此处开始才是"组件"的开始

此时,组件的一般描述为特定的ui下,共享事件与函数/方法

#### 共享事件

##### 面向对象

软件中做共享的常用方式--&gt;面向对象

js引入对象概念,即pototype

```js
var element = document.getElementById("pagination");
var comp = new u.pagination({ el: element,showState:false });
comp.update({ totalPages: 100, pageSize: 20, currentPage: 1, totalCount: 200 });
comp.on('pageChange', function(pageIndex) {
    console.log('新的页号为' + pageIndex);
});
comp.on('sizeChange', function(arg) {
    console.log('每页显示条数为' + arg[0]);
});
```

以上为iuap的实现方式,创建后返回的是一个对象,一般通过$\('\#pagination'\).pagination\(\),的方式用以获取对象

即pagination即代表初始化返回,也代表直接返回,这很jq

当然,iuap并不一般...

##### 面向闭包

作为一个组件/类/对象,使用pototype没毛病,作为一组组件,依赖于某个指向时\(比如$\),直接使用闭包即可

```
$('#cc').combobox({
    url: ...,
    required: true,
    valueField: 'id',
    textField: 'text'
});

$('#cc').combobox('getData');
```

以上为easyui的api,其内部直接使用闭包

```

```

其api包括,可以使用菊花链,初始化错误\(html不存在\),不为报错,可以预定义未实现的方法,总之,很函数式

毕竟,js并不是专业的面向对象语言

#### 总结

老版,且单个的组件开发者,使用面向对象的方式实现

组件库,则会将组件收集起来使用闭包的api进行拓展

[https://www.w3.org/TR/shadow-dom/](https://www.w3.org/TR/shadow-dom/)

[https://www.w3.org/TR/custom-elements/](https://www.w3.org/TR/custom-elements/)

