#### version

1.16.5

#### bin/webpack-dev-server.js

cli系列,使用如下方式触发

```
webpack-dev-server   --host 127.0.0.1 --inline --hot --config config/webpack.dev.babel.js
```

##### optimist

如何有逼格的描写node参数

##### processOptions

options,会尽力...中进行一系列的初始化,其中下面的devClient为使用new webpack和webpack-dev-server脚本最大的区别,也是部分文档所说的需要追加注入../client的原因

```js
//.....
if(options.inline) {
	var devClient = [require.resolve("../client/") + "?" + protocol + "://" + (options.public || (options.host + ":" + options.port))];

	if(options.hot)
		devClient.push("webpack/hot/dev-server");
	[].concat(wpOpt).forEach(function(wpOpt) {
		if(typeof wpOpt.entry === "object" && !Array.isArray(wpOpt.entry)) {
			Object.keys(wpOpt.entry).forEach(function(key) {
				wpOpt.entry[key] = devClient.concat(wpOpt.entry[key]);
			});
		} else {
			wpOpt.entry = devClient.concat(wpOpt.entry);
		}
	});
}
new Server(webpack(wpOpt), options).listen(options.port, options.host, function(err) {
	var uri = protocol + "://" + options.host + ":" + options.port + "/";
	if(!options.inline)
		uri += "webpack-dev-server/";

	if(err) throw err;
	console.log(" " + uri);
	console.log("webpack result is served from " + options.publicPath);
	if(typeof options.contentBase === "object")
		console.log("requests are proxied to " + options.contentBase.target);
	else
		console.log("content is served from " + options.contentBase);
	if(options.historyApiFallback)
		console.log("404s will fallback to %s", options.historyApiFallback.index || "/index.html");
	if(options.open)
		open(uri);
});
```

至此,只用webpack与hot模块搭建开发模式需要以下两部分,不难理解吧

```js
var devClient = ["webpack-dev-server\\client\\index.js?http://"+host+":"+(devServer.port||3001), "webpack/hot/dev-server"]

Object.keys(config.entry).forEach(function(key) {
    config.entry[key] = devClient.concat(config.entry[key]);
});

//引用
new webpack.HotModuleReplacementPlugin()
```

#### lib/Server

基于express,在开发环境下使用的服务,如

```js
app.get("/webpack-dev-server", function(req, res) {
		res.setHeader("Content-Type", "text/html");
		res.write('<!DOCTYPE html><html><head><meta charset="utf-8"/></head><body>');
		var path = this.middleware.getFilenameFromUrl(options.publicPath || "/");
		var fs = this.middleware.fileSystem;
		//...
})
```

http://127.0.0.1:3001/webpack-dev-server就是最好的导航

![](/assets/webpack-dev-server-1.png)

##### 代理

```js
options.proxy.forEach(function(proxyConfig) {
	var bypass = typeof proxyConfig.bypass === 'function';
	var context = proxyConfig.context || proxyConfig.path;
	var proxyMiddleware;
	// It is possible to use the `bypass` method without a `target`.
	// However, the proxy middleware has no use in this case, and will fail to instantiate.
	if(proxyConfig.target) {
		proxyMiddleware = httpProxyMiddleware(context, proxyConfig);
	}

	app.use(function(req, res, next) {
		var bypassUrl = bypass && proxyConfig.bypass(req, res, proxyConfig) || false;

		if(bypassUrl) {
			req.url = bypassUrl;
			next();
		} else if(proxyMiddleware) {
			return proxyMiddleware(req, res, next);
		}
	});
});

```

使用httpProxyMiddleware



其他略

