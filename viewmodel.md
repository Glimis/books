后台结构

```
{
  body:[{
    name:'某公司',
    //报价次数
    priceList:[{
        price:10,
        tax:10
    }],
  }],
  //专家与指标信息
  exportList:[{
      //专家id
      id:1,
      //指标id
      index:1,
      //分值
      score:10
  },{
      //专家id
      id:1,
      //指标id
      index:2,
      //分值
      score:10
  }],
  //指标信息
  indexs:[{
    id:1,
    name:'A指标'
  },{
    id:2,
    name:'B指标'
  }]
}
```

需要转换成如下UI

用任意喜欢的方式进行实现,伪代码,概念图,涂鸦均可

并回答如下问题：

什么是viewModel?

## 实现

## 问题

在view中进行大量函数操作如结构转换导致的代码混乱,过度依赖后台模型,如题中后台明显有问题的结构,也会在后台进行结构修改时,危机前端view

jsp就是java,但绝不轻易调用java方法

