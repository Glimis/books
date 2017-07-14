WebpackDevServer,用于调用基于express搭建的开发环境

```js
var webpack = require('webpack');
var WebpackDevServer = require('webpack-dev-server');
var Server = new WebpackDevServer(webpack(config));
Server.listen(config.devServer.port, localhost, function (err, result) {})
```

## weback\(config\)产生何物?

```js
function webpack(options, callback) {
    var compiler;
    if(Array.isArray(options)) {
        //迭代
        compiler = new MultiCompiler(options.map(function(options) {
            return webpack(options);
        }));
    } else if(typeof options === "object") {
        //初始化全局参数
        new WebpackOptionsDefaulter().process(options);

        compiler = new Compiler();
        compiler.options = options;
        //模型转换
        compiler.options = new WebpackOptionsApply().process(options, compiler);
        //初始化插件
        new NodeEnvironmentPlugin().apply(compiler);
        compiler.applyPlugins("environment");
        compiler.applyPlugins("after-environment");
    } else {
        throw new Error("Invalid argument: options");
    }
    //。。。
    return compiler;
}
```

产出编译器\(compiler\),故单纯的webpack\(config\)需要配合注入Server or compiler.run进行使用

## 如何进行编译?

`bin/webpack`-&gt;webpack 脚本命令

```js
//optimist 不解释
//...
var webpack = require("../lib/webpack.js");

Error.stackTraceLimit = 30;
var lastHash = null;
//获取编译后对象
var compiler = webpack(options);

function compilerCallback(err, stats) {
    //...
}
if(options.watch) {
    var primaryOptions = !Array.isArray(options) ? options : options[0];
    var watchOptions = primaryOptions.watchOptions || primaryOptions.watch || {};
    if(watchOptions.stdin) {
        process.stdin.on('end', function() {
            process.exit(0); // eslint-disable-line
        });
        process.stdin.resume();
    }
    //监听
    compiler.watch(watchOptions, compilerCallback);
} else
    compiler.run(compilerCallback);//编译

}

processOptions(options);
```

webpack 命令,根据watch决定调用run or watch...

即通过compiler.run进行编译

## 如何自定义plugin?

[http://webpack.github.io/docs/how-to-write-a-plugin.html](http://webpack.github.io/docs/how-to-write-a-plugin.html)

## Compiler and Compilation? {#compiler-and-compilation}

* Compiler

嗯,就是`webpack(config)`输出的那个对象

* Compilation

```js
Compiler.prototype.run = function(callback) {
	//...
	this.compile(function(err, compilation) {})
	//...
};			

Compiler.prototype.createCompilation = function() {
	return new Compilation(this);
};

Compiler.prototype.newCompilation = function(params) {
	var compilation = this.createCompilation();
	//...
	return compilation;
};

Compiler.prototype.compile = function(callback) {
	//..
	var compilation = this.newCompilation(params);
	//..
};
```

run会调用compile,compile时会创建新的Compilation

watch会调用Watch.\_go,其也回调用compile这个函数

即compiler是全局参数,compilation是运行时参数

