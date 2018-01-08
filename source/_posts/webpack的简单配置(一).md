---
title: webpack的简单配置(一)
---
##### 开始
首先生成一个项目 npm init
安装webpack以及webpack-dev-server
```
npm install webpack webpack-dev-server --save-dev
```
(由于使用了之前的老静态页面做练习,只是简单的打包了css,js以及一些静态文件)
需要安装的依赖有:css-loader、style-loader、extract-text-webpack-plugin(该插件可以使散落的css文件打包成一个css,在首页使用link引入)、file-loader、url-loader、webpack-merge、html-webpack-plugin(用于生成html模板)

然后在package.json文件的script中配置开启以及打包的内容
```
"dev": "webpack-dev-server  --open  --config  webpack.config.js"
"build": "webpack --progress --hide-modules --config build.js"
```
在这里需要创建两个文件:一个是webpack.config.js以及build.js

在webpack.config.js文件中：
```
const path = require('path');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

const config = {
    entry: {
        main: './main'
    },
    output: {
        path: path.join(__dirname, './distdoc'),
        publicPath: '/distdoc/',
        filename: 'main.js'
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ExtractTextPlugin.extract({
                	publicPath: './',
                    use: 'css-loader?minimize',
                    fallback: 'style-loader'
                })
            },
            {
		test: /\.(gif|jpg|png|woff|svg|eot|ttf)\??.*$/,
		loader:'url-loader?limit=1024&name=distdoc/[hash].[ext]'
	     }
        ]
    },
    plugins: [
        new ExtractTextPlugin('main.css')
    ]
};

module.exports = config;
```
在这里需要在配置css中加上publicPath: './' 让css中的资源地址为相对路径，css-loader后面加上minimize使打包后的css代码压缩，入口文件为当下的main.js，需要配置main.js，出口文件则配置为distdoc文件夹下面的main.js(自动生成)

在main.js中 把我们需要打包编译的文件加上
```
import './css/reset.css'
import './css/index.css'
import func from './js/func.js'
import JSONdate from './js/JSONdate.js'
func();
JSONdate();
```
后面的js文件 我们使用export default function ...()的方法将其导出

最后在需要打包的build.js文件中
```
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const ExtractTextPlugin = require('extract-text-webpack-plugin')
const merge = require('webpack-merge')
const webpackBaseConfig = require('./webpack.config')

webpackBaseConfig.plugins = []

module.exports = merge(webpackBaseConfig, {
	output: {
		publicPath: '/distdoc/',
		filename: '[name].js'
	},
	plugins: [
		new ExtractTextPlugin({
			filename: '[name].css',
			allChunks: true
		}),
		new webpack.DefinePlugin({
			'process.env': {
				NODE_ENV: '"production"'
			}
		}),
		new webpack.optimize.UglifyJsPlugin({ 
			compress: {
				warnings: false
			}
		}),
		new HtmlWebpackPlugin({
			filename: '../index_pro.html',
            template: './index.html',
            inject: false
		})
	]

})
```
我们将其打包的文件为index_pro.html，模板为index.html，配置webpack.optimize.UglifyJsPlugin让打包后的js代码压缩
(简书笔记搬运 写于2017.09.03 01:48)