---
title: node基础
tags: node
categories: node
date: 2019-08-01
---

<div align='center' ><font size='70'>node基础</font></div>

#### 一、模块 ####

##### 定义：
每一个文件就是一个模块，每一个模块就是一个函数，用户编写的代码都自动封装在一个函数中，函数拥有五个参数。
```javascript
function (exports, require, module, __filename, __dirname) { 

}
```
* exports：暴露对象，可以将模块的数据暴露给引用的地方
	`exports.属性名=值`
	`exports.方法名=值`
	exports是model.exports的引用，不能改指向，只能添加属性和方法，若需要暴露函数，需使用model.exports。
* require：引入模块的函数，用于在一个模块中引入另一个模块，并且将子模块暴露的数据复制给变量。
	在require中自定义模块必须加`./`,因为在node中默认路径是`node_modules`。
* module：模块对象，包含了当前模块的所有信息。
		module.exports 真正的暴露对象。
		```javascript
				module.exports.属性=值；
				module.exports.方法=函数；
				module.exports=对象或函数；
		```
		module.id  模块id，模块名称
		module.parent  模块的父级
		module.filename  模块的路径和文件名
		module.children  子模块的列表
		module.paths  模块查找路径，如果当前目录下找不到`node_modules`，就去上一级目录查找，直到根目录。
* __filename: 当前模块的文件名。
* __dirname：当前模块所在的路径。

##### 分类：
1.自定义模块
	自己编写的模块：
	`require('./文件名.js')`
2.第三方模块（需要npm）
	`require('模块名')`
	npm安装后模块在`node_modules`中。
3.系统模块
	node.js自带的功能模块，直接引用。
	例如：fs、http、url、path等
	`require('模块名')`
	
#### 二、作用域 ####
>由于node.js在执行里会将用户编写的所有代码都封装在函数中，所有用户定义的变量或函数都是要将数据共享给其他模块使用：

1.暴露对象
	`module.exports`
2.全局对象
	`global.属性或方法=值或函数`
	在使用全局变量时，global可省略不写。
	

​	


