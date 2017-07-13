如何优雅的解析node参数

## node服务获取参数

```js
console.log(process.argv)
```

process.argv,第一个为node路径,第二个为执行文件路径,身下的为参数

## 如何优雅的解析参数

使用process.argv的封装库,如[optimist](https://github.com/substack/node-optimist),源码不过400行/10K,可放心食用

直接运行test例子即可,以下内容纯copy,纯海货





