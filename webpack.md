WebpackDevServer,用于调用基于express搭建的开发环境,那么webpack类

```js
var webpack = require('webpack');
var WebpackDevServer = require('webpack-dev-server');
var Server = new WebpackDevServer(webpack(config));
Server.listen(config.devServer.port, localhost, function (err, result) {})
```

## bin/webpack

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

## webpack

```js
webpack(config)
```

config为文档中的配置参数

```js
function webpack(options, callback) {
	var compiler;
	if(Array.isArray(options)) {
		//迭代
		compiler = new MultiCompiler(options.map(function(options) {
			return webpack(options);
		}));
	} else if(typeof options === "object") {
		//初始化参数
		new WebpackOptionsDefaulter().process(options);

		compiler = new Compiler();
		compiler.options = options;
		compiler.options = new WebpackOptionsApply().process(options, compiler);
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

产出编译器,故单纯的webpack\(config\)需要配合注入Server or compiler.run进行使用



