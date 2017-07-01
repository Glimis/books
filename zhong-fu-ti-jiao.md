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

阻止ajax请求\(post/get\)

正确响应最后一次结果

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

根据具体情况以及涉及范围,做以下区分

#### ajax拦截

##### abort函数

对原有xhr\(ajax\),进行abort处理,类似于timeout,触发条件不一样

对于post,并没有阻止ajax请求,请求已经发送,后台依然在处理请求

对于get,可以保证当前请求为正确的结果\(最终效果第二条\)

```
$.ajaxSetup({
  beforeSend:function(jqXHR, s ){
    var callbackContext = this;
    jqXHR._url=callbackContext.url
  }
})

var global={};
function abort(ajax){
  var preajax = global[ajax._url];
  preajax&&preajax.abort();
  global[ajax._url]=ajax
}

abort($.get('http://127.0.0.1:3001/cpu-project-def/ycctrl/projectdef/list?pageIndex=0&statuses=%5B12%2C14%2C15%2C16%2C17%2C29%2C32%2C33%2C34%2C35%2C36%5D&_=1498811377585&1498811382867'))
```

##### 忽略

1. 对于神之iuap的超级接口,需要个性化处理
2. 会无视,结果未返回时,请求参数已变更的请求

快速搭建时使用

#### ajax包裹

##### throttle/debounce

高频\(鼠标移动,窗口大小\)请求中,调整请求和响应速度的方式,属于setTimeout的花样使用

主要用于富文本下的快捷操作,按钮和自动保存,用以减少保存次数

##### memoize

get幂等缓存

主要用于数据更新频率低的异步加载树系列,如省,城,区

前端利用幂性,当然,后台挂了得刷新

#### 限制ui

此处需要ajax依赖ui

##### disabled

尤指保存

##### 转菊花

## 后台重复验证

提前提供id,将创建,改写为"修改",防止一对一的关系转换为一对多

ssh系列的token防重复提交,指的是该系列

缓存系列与数据库平级,不讨论

其他情况并不处理...

