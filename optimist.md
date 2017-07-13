如何优雅的解析node参数

## node服务获取参数

```js
console.log(process.argv)
```

process.argv,第一个为node路径,第二个为执行文件路径,身下的为参数

## 如何优雅的解析参数

使用process.argv的封装库,如[optimist](https://github.com/substack/node-optimist),源码不过400行/10K,可放心食用

### 源码

* 依赖minimist

解析命令的核心包,将参数\(str\)转换为数据\({}\),定义key以--/-或开头,或value 以=开头,其他情况key为true等相关内容

minimist的变化对optimist有一定影响,部分example可能是错的,影响不大,不纠结

* 依赖wordwrap,打印内容

optimist,在minimist基础上追加各种错误提示,格式验证/转换,当然,顺便固定了一下参数

##### 获取固定参数

```js
var inst = Argv(process.argv.slice(2));
Object.keys(inst).forEach(function (key) {
    Argv[key] = typeof inst[key] == 'function'
        ? inst[key].bind(inst)
        : inst[key];
});
```

Object.keys,会触发两次argv

##### 封装minimist参数

```js
var options = {
    boolean: [],
    string: [],
    alias: {},
    default: []
};

self.boolean = function (bools) {
    options.boolean.push.apply(options.boolean, [].concat(bools));
    return self;
};
//......
```

##### 监听属性

```js
Object.defineProperty(self, 'argv', {
    get : function () { return parseArgs(processArgs) },
    enumerable : true,
});
```

即调用argv时直接调用parseArgs,而非函数式,6不6

##### 调用核心,追加必填/验证

```js
var argv = minimist(args, options);

argv.$0 = self.$0;

if (demanded._ && argv._.length < demanded._) {
    fail('Not enough non-option arguments: got '
        + argv._.length + ', need at least ' + demanded._
    );
}
//....
```

##### 例子

直接运行example例子即可,纯海货

