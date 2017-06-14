---
layout:     post

title:      "webpack入门"

subtitle:   "环境配置与打包基础"

date:       2017-06-12 15:38

author:     "Wyq"

catalog:    true

tags:
   学习 
   webpack
---

## 环境搭建

按照[windows下配置npm](http://www.wangyuquan.cn/2017/05/18/window-git/)上的配置安装好npm命令后，直接运行`npm install webpack -g` 即可进行全局安装。如需在项目目录下安装webpack，则需要在项目根目录下运行`npm install webpack --save-dev`命令(需要在新建完项目名称以及生成package.json文件后运行)。


## 项目构建及JS打包
1. 新建项目webpacktest，在项目*根路径*下新建build和src两个文件夹。src为开发目录，build为打包目录。
2. 在项目根路径下运行命令`npm init`生成package.json。按照默认配置一路回车即可。
3. 在src目录下新建四个文件
	
	index.js|打包的入口文件
	
	common.js|为普通的js模块文件
	
	style.css|样式模块文件
	
	index.html|项目的首页

4. 写入代码
各个文件的代码如下：
	* index.html
	```
		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="UTF-8">
		    <title>webpack</title>
		</head>
		<body>
		    <div id="div1"> 
		    </div>
		    <script type="text/javascript" src="./bundle.js"></script>></script>
		</body>
		</html>
	``` 
	注：**bundle.js为打包后的文件，里面包括所有模块（js/css/图片/字体等）的配置及打包文件。**
	* common.js
	```
	    module.exports.doCommon=function(){
	        console.log("this is common.js !")
	    }
	```
	* index.js
	```
	    var commonFn=require("./common.js");
	    commonFn.doCommon();
	    console.log("this is index.js !");
	```
	* style.css
	```
	    /*样式模块文件*/
	    #div1{
	        width:200px;
	        height:200px;
	        margin:100px auto;
	        background: yellowgreen;
	        border-radius: 50%;
	        box-shadow: 10px 10px 5px #888888;
	    }
	```
5. 运行打包命令,完成对js的打包
在src目录下运行`webpack index.js bundle.js`
在浏览器中查看index.html，打开控制台，可以揽到控制台打印的如下语句：
```
    this is common.js !
    this is index.js !
```
## css打包
1. 安装style-loader与css-loader  
项目根路径下运行如下命令：  
`npm install style-loader css-loader --save-dev`
2. 在根目录添加webpack.config.js配置文件，代码如下：
```
    var path=require("path")
    module.exports={
        entry:{
            //入口文件路径，要求为相对路径
            index:["./src/index.js"]
        },
        output:{
            filename:"bundle.js",
            //paht要求为绝对路径，需要引入paht模块调用resolve方法
            path:path.resolve(__dirname,"src/") 
        },
        module:{
            loaders:[
                {
                test: /\.css$/,
                loaders: ['style-loader', 'css-loader']
                }
            ]
        }
    }
```    
3. 修改index.js文件代码为：
```
    require("./style.css");
    var common=require("./common.js");
    common.commonFn();
    console.log("this is index.js !")
```    
4. 在项目根路径下运行 `webpack`命令，打开index.html，查看效果。

## html打包（代码打包至build文件夹）
1. 项目根路径下运行  
`npm install html-webpack-plugin --save-dev`
2. 重新配置webpack.config.js
```
    var path=require("path");
    //插件需要引入
    var htmlWebpackPlugin=require("html-webpack-plugin");
    module.exports={
        entry:{
            //入口文件路径，要求为相对路径
            index:["./src/index.js"]
        },
        output:{
            filename:"bundle.js",
            //paht要求为绝对路径，需要引入paht模块调用resolve方法
            //现在要把输出路径改为构建路径
            path:path.resolve(__dirname,"./build/") 
        },
        module:{
            loaders:[
                {
                test: /\.css$/,
                loaders: ['style-loader', 'css-loader']
                }
            ]
        },
        plugins:[new htmlWebpackPlugin({
                title:"webpacktest",
                template:"./src/index.html",
                filename:"index.html"
            })] 
    }
``` 
3. 项目根路径下运行webpack，会发现文件被打包至build文件夹，打开bulid下的index.html,查看效果。
注：*在此步如果运行webpack时报错  
**Cannot find module webpack/lib/node/NodeTemplatePlugin** ,需要配置NODE_PATH环境变量。命令行输入 `npm config get piefix` 按照显示的node路径配置NODE_PATH环境变量即可。如我的路径为 `D:\Program Files\node\node_modules`*

## 打包图片文件

在css中引入图片的大小大于8k时，若没有安装file-loader会报错，原因是大于8k的图片会被webpack当做文件处理，而小于8k的图片会被转为base64编码引入行间以减少http请求；若未下载html-withimg-loader则在html中引入的img标签不会被打包。配置步骤如下：

1. 项目根路径运行   
`npm install url-loader file-loader html-withimg-loader --save-dev`
2. webpack.config.js的module-loaders部分新增如下配置：
```
    //用于css中背景图片的加载器
    {
        test: /\.(png|jpg)$/,
        //limit:图片大小小于8K，会以base64编码图片以减少http请求
        //name：输出子目录构建
        loader: 'url-loader?limit=8192&name=./images/[name].[ext]'
    },
    //用于html中img标签的图片加载器
    {
        test: /\.html$/,
        loader: 'html-withimg-loader'
    }
```    
3. html或者css中引入图片，项目根路径下运行webpack命令，打开index.html即可查看效果。



参考：[http://www.cnblogs.com/pomelott/p/6974167.html](http://www.cnblogs.com/pomelott/p/6974167.html)