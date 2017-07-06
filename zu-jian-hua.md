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

接口\(选择器\)权限过大,太深\(精确\)的不想写,太浅的误伤率及高

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

ajax是一个标记

包含局部更新的视图,都属于复用范畴,组件的意义更明显

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

ajax返回html\(即include\)代码,可保证浏览器的效率,减少前端白屏/初始化时间,最重要的是,不需要拼接字符串

### jq时代

种子文件/core/基础/工具...以上说法等价于jq

由此处开始才是"组件"的开始

此时,组件的一般描述为特定的ui下,共享事件与函数/方法

#### 面向对象

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

#### 总结

各类组件争clazz,样式极度冲突依赖作者

风格差异大,api差异更大

依赖js,不加载js时,页面没法看\(白屏时间or加载时间\)

### EasyUI

此处代表组件库,Yui,Ext,Bootstrap你熟悉的任意一款

主要用于解决样式风格差异和api差异的问题

实现上也有一定差异

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
$.fn.combobox = function(options, param){
    if (typeof options == 'string'){
        //执行
        var method = $.fn.combobox.methods[options];
        if (method){
            return method(this, param);
        } else {
            return this.combo(options, param);
        }
    }

    options = options || {};
    //初始化
    return this.each(function(){
        //...
        create(this);
        //...
    });
};
```

其api优势包括,可以使用菊花链,封装错误\(尤其是html不存在,不用判断对象是否存在\),可以预定义未实现的方法,保护对象内部属性,总之,很函数式

毕竟,js并不是专业的面向对象语言

当然,注入bootstrap,会使用其他开源组件,但内部依然会参考此类api

##### 动态加载

没四五十个组件都不好意思叫自己组件库,除了自定义组件外,如何优雅的动态加载也是组件库的标记\(引用.all文件的略\)

最终形式性质类似于requireJs

##### 皮肤/主题

修改css依然蛋疼,但一般会提供几个theme方便参考

#### 总结

* 统一api风格与样式风格
* 允许动态加载
* 包含theme

缺点也很明显

* 基础库不能随意切换
* 可能依赖基础库版本

随手吐槽iuap,引用jq1.7与jq1.9的神级行为

### EXT

这不是解决方案,只是另一个里程碑,此处代表任意一款前端框架,尤其是个厂内使用的

以Ext为例

##### 完善的基础库

一般具有强制性,也可以叫不依赖jq

##### 众多组件

不光能自定义,还能N级嵌套

##### 管理生命周期

生命周期依赖上游组件or框架,至少组件库难以实现

##### 模板

不是拼接html,也不是ajax传递html,而是前端模板,类似于format函数

```
renderTpl: [
    '<span id="{id}-btnWrap" class="{baseCls}-wrap',
        '<tpl if="splitCls"> {splitCls}</tpl>',
        '{childElCls}" unselectable="on">',
        '<span id="{id}-btnEl" class="{baseCls}-button">',
            '<span id="{id}-btnInnerEl" class="{baseCls}-inner {innerCls}',
                '{childElCls}" unselectable="on">',
                '{text}',
            '</span>',
            '<span role="img" id="{id}-btnIconEl" class="{baseCls}-icon-el {iconCls}',
                '{childElCls} {glyphCls}" unselectable="on" style="',
                '<tpl if="iconUrl">background-image:url({iconUrl});</tpl>',
                '<tpl if="glyph && glyphFontFamily">font-family:{glyphFontFamily};</tpl>">',
                '<tpl if="glyph">&#{glyph};</tpl><tpl if="iconCls || iconUrl">&#160;</tpl>',
            '</span>',
        '</span>',
    '</span>',
    // if "closable" (tab) add a close element icon
    '<tpl if="closable">',
        '<span id="{id}-closeEl" class="{baseCls}-close-btn" title="{closeText}" tabIndex="0"></span>',
    '</tpl>'
],
```

##### javaweb/重js

css使用主题,html使用模板,js使用组件,此时的程序猿绝对属于javaweb级

##### 更多的概念

面向对象,继承,混合\(mixins\),按需加载,前端路由,mvc....

只要你能想到的,都有实现

##### 学习成本高

对框架依赖过高

##### 依赖架构\(难以维护\)

没错,所有的技术都由架构组搭建完成,开源or最新技术,需要等架构组撸完才能使用,当然,使用该框架的人定位java web

##### 越来越肿

传统公司的需求,一家需求,必将转换为通用需求,反正留那不算错,类似思想在架构中也存在,如iuap

关键在于,不是动态加载的,一上来就有的NB功能,但可能一辈子都用不上

##### css

window风格到扁平风,主题跟不上

##### 前端太快了

12年,某国企励志,使用最先进的技术,在最牛逼的浏览器上进行开发...三年后,流产了...

没错,当年IE系列一统江湖,其中IE8是大哥...

activex,silverlight,flash,当时最火的东西,集体扑街

### Bootstrap

此处代表的是轻量级,已bootstrap为代表,其主要特色为区分组件与js组件

* 组件

使用固定css描述的html

* js组件

对组件进行生命周期的加载

至此,描述组件仅需要html+css,描述js组件仅需要js,原则上遵循老传统,即不加载js时,页面可以看

主题选用最新的扁平化,css使用less/sass,用以增加对于组件的拓展性

在遵循不加载js也能看的基础上`组件实现一般不涉及dom的修改,且大部分实现可有代理($().on)的方式进行统一实现(不需要创建)`

#### 总结

* "面向对象"的css

css分为结构/布局和业务,增加了点复杂性,需要花点时间了解布局

bootstrap系列\(使用固定css组,描述组件\)属于jq时代组件的最优雅的方式,其中,

* html

include:js并不会修改dom,只用来追加事件

前端模板:require

es6语法:\`\`

* css

默认主题

less/sass,使用特定类与继承风格描述

* js

通过css进行事件注入,动态/按需加载,组件组合,统一api...

### Web Component

自定义标签,一个新世界的大门,html本身允许使用自定义标签/属性,然而没卵用啊,包含四个概念,概念后对应的是bootstrap系列中的处理方式

* Shadow DOM 

生命周期,指定css下通过代理与自定义事件的方式处理

* Custom Elements

创建风格,默认使用闭包的形式

* HTML Imports

require/import

* HTML Templates

单独的html/tpl文件 or es6的\`\`语法

整体设计上类似多窗口or层叠上下文,追加了作用域的概念,但目前过于先进,兼容性感人,估计api略,有兴趣直接看[文档](https://www.w3.org/TR/#tr_Web_Components)

直接使用不行,但并不代表框架不可以借鉴此概念,MVVM系列框架,大多会参考该文档,用以自定义标签,大概是因为vm的作用域天生就适配shadow Dom的作用域吧

总之,标榜自己可以组件化的MVVM框架,基本包含以下几种实现方式

* 手动注册

```
Vue.component('test-component',{  
    props:['message'],  
    template:'<div>{{message}}</div>'  
});
```

* webpack

```
<template lang="jade">
div {{msg}}

</template>

<script>

export default {
  data () {
    return {
      msg:'...'
    }
  }
}
</script>

<style lang="less" scoped>

</style>
```

##### 参考资料

[https://www.w3.org/TR/shadow-dom/](https://www.w3.org/TR/shadow-dom/)

[https://www.w3.org/TR/custom-elements/](https://www.w3.org/TR/custom-elements/)

