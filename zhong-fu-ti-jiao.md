# 重复提交\(高频处理\)

* 重复提交

> 单位时间内,多次非幂等\(post\)请求,造成的后台重复录入现象

此处重点讨论前端的防重复提交的方式

* 快速点击

> 单位时间内,快速点击,触发异步事件,照成数据与ui混乱的现象

如导航与内容区,导航快速点击,会使内容区异常抖动,且可能会与导航具体标签不一致

属于高频,但比较独特,包含了异步操作,故单独拿出来处理

* 高频事件

> 单位时间内,触发的各类事件,且响应速度低于请求,照成的卡顿或其他混乱现象,如快速点击,鼠标移动

以上三种情况之后统一描述重复提交

## 最终效果

* 阻止过多的ajax请求\(post/get\)
* 正确响应最后一次结果

## 关键技术点

* Aborted

xhr的abort函数

* setTimeout

* 限制性UI

修改按钮/UI,弹菊花

...

## 重复判断

> 保存正在进行的ajax操作\(0&lt;readyState&lt;4\)及其url,当该操作存在,且又要发送相同请求时,视为重复提交

## 处理

根据影响范围,做一下区分

### ajax拦截/包裹/封装

#### abort函数

对原有xhr\(ajax\),进行abort处理

对于post,并没有阻止ajax请求,请求已经发送,后台依然在处理请求

对于get,可以保证当前请求为正确的结果\(解决快速点击\)

##### 实现

```
var global={};

$.ajaxSetup({
  beforeSend:function(jqXHR, s ){
  //为jqXHR注入url
    var callbackContext = this;
    jqXHR._url=callbackContext.url
  }
});


function abort(ajax){
  //获取上一个请求
  var preajax = global[ajax._url];
  preajax&&preajax.abort();
  global[ajax._url]=ajax
};

var abort_ajax=_.flow($.get,abort);

abort_ajax('/');
abort_ajax('/');
abort_ajax('/');
abort_ajax('/');
```

此种实现默认url获取数据后,会对数据进行分发

分页,导航,搜索\(度娘\)均可使用

##### 其他

若为传统\(10年前-。-\)的组件+url的形式,如

```
<select url="api/students"></select>
<select url="api/students"></select>
```

此时,会产生两条相同请求,获取数据后并不分发,需要改写代码

若对ajax进行了封装,使用注入Store/Model,vuex,flux之类的模型

根据不同的场景描述不同的action,性质是一样的

> abort思路,可以解决异步交互\(get\)下,最后一次响应的问题
>
> js是同步的,无法解决同步的多次提交\(同步为卡顿,响应顺序并不会错误\)

#### 忽略\(单例\)

根据url,对ajax实现单例,即当前url正在通讯时,返回上一个ajax,不在创建信息ajax

##### 实现

```
var ignore_ajax=function(params){
    var preajax = global[params.url];
    if(preajax){
      return preajax
    }
    var ajax = $.ajax(params)
    ajax.then(function(){
        global[this.url]=undefined;
    })
    global[params.url] = ajax;
    return ajax;
}

ignore_ajax({url:'/'});
ignore_ajax({url:'/'});
ignore_ajax({url:'/'});
ignore_ajax({url:'/'});
```

例子中省略global,ajaxSetup步奏,下同

一般由Store,Model等封装ajax的模块,配合readyState进行操作,即将ajax改成单例

无法保证最后一次请求的响应,故本身会配合ui进行限制性操作

保存系列,均可使用

##### 其他

* 对于神之iuap的超级接口,需要个性化处理

* 会无视,结果未返回时,请求参数\(body内\)已变更的请求

#### throttle/debounce

高频\(鼠标移动,窗口大小\)请求中,调整请求和响应速度的方式,属于setTimeout的花样使用

##### 实现

```
var debounce_ajax = _.debounce($.ajax,5000);
debounce_ajax('/')
```

##### 其他

* throttle

> 优先执行,之后放哑炮,类似于忽略\(单例\),无法保证能够准确的响应最后一次操作

不依赖最后一次响应的事件,比如mousemove,对于异步需要配合ui限制\(也可以改用忽略的方式\),总之,不是最好的选择

* debounce

> 单位时间内收集数据,而后进行请求,

包含数据收集的输入框,都可以使用比如触发框,富文本自动保存

另:度娘的服务器就是屌

#### memoize

get幂等

##### 实现

```
var memoize_ajax =_.memoize($.ajax)
```

##### 其他

没错,就是缓存,主要用于数据更新频率低的幂等异步操作

比如requireJS,加载导航数据,菜单栏,甚至各种树结构

### 限制ui

此处ajax依赖ui,属于ajax处理后台进阶表现,尤其是单例的情况下

当然,只用ui控制也无所谓,多入口保存的情况还是比较少的

#### disabled/active系列

> 将整个操作函数封装为promise,并通过e获取相应ui

```js
export function buttonDisable(promise){
    //寻找依赖ui
    var e=arguments[arguments.length-1]
    var btn=e.target.parentElement;
    //设置类 or 属性
    btn.setAttribute('disabled','')
    promise()
      .then(()=>{
        btn.removeAttribute('disabled');
      })
      .catch(()=>{
        btn.removeAttribute('disabled');
      })
}

@buttonDisable
async  submit(){
    var data=await Post('/');
    console.log(data);
}
```

es6写法\(es5略\)

整体包裹/限制的范围为submit的执行过程,若单独对ajax进行限制,对于嵌套/并行等各种花式ajax完全无爱

当然,也可以做如下操作

```js
export function buttonDisable(vm){
  return function(promise){
    vm(false);
    promise()
      .then(()=>{
        vm(true);
      })
      .catch(()=>{
        vm(true);
      })
    }
}

@buttonDisable(vm.submitFlag)
async  submit(){
    var data=await Post('/');
    console.log(data);
}

```

```
<button class="u-button u-button-info " data-bind="{click:submit,attr:{disabled:submitFlag}}">提交</button>
```

> 封装ajax,监听readyState

```js

async  submit(){
    var data=await Store.load('');
    console.log(data);
}
```

```
<button class="u-button u-button-info " data-bind="{click:submit,attr:{disabled:Store.readyState}}">提交</button>
```

Store为ajax的封装,其中readyState为监听对象\(属性\),如果没有这一层...

#### 转菊花

## 后台重复验证

提前提供id,将创建,改写为"修改",防止一对一的关系转换为一对多

ssh系列的token防重复提交,指的是该系列

缓存系列与数据库平级,不讨论

其他情况并不处理...

