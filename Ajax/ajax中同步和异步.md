---
title: ajax中同步和异步 
tags: ajax
categories: JavaScript
date: 2019-04-16
---

AJAX中根据async的值不同分为同步（async = false）和异步（async = true）两种执行方式；在W3C的教程中推荐使用异步执行；

```javascript

 $.ajax({  
 	type: "post",  
	url: "path",  
	cache:false,  
	async:false,  
	dataType: ($.browser.msie) ? "text" : "xml",  
	success: function(xmlobj){  
	function1(){}; 
}); 
function2(){}; 
```

默认情况下async是true

# 一、什么是同步请求：(false)

同步请求即是当前发出请求后，浏览器什么都不能做，必须得等到请求完成返回数据之后，才会执行后续的代码，相当于是排队，前一个人办理完自己的事务，下一个人才能接着办。也就是说，当JS代码加载到当前AJAX的时候会把页面里所有的代码停止加载，页面处于一个假死状态，当这个AJAX执行完毕后才会继续运行其他代码页面解除假死状态(即当ajax返回数据后，才执行后面的function2)。

#  二、什么是异步请求：(true)

异步请求就当发出请求的同时，浏览器可以继续做任何事，Ajax发送请求并不会影响页面的加载与用户的操作，相当于是在两条线上，各走各的，互不影响。一般默认值为true，异步。异步请求可以完全不影响用户的体验效果，无论请求的时间长或者短，用户都在专心的操作页面的其他内容，并不会有等待的感觉。

# 三、同步和异步区别

异步：在异步模式下，当我们使用AJAX发送完请求后，可能还有代码需要执行。这个时候可能由于种种原因导致服务器还没有响应我们的请求，但是因为我们采用了异步执行方式，所有包含AJAX请求代码的函数中的剩余代码将继续执行。如果我们是将请求结果交由另外一个JS函数去处理的，那么，这个时候就好比两条线程同时执行一样。同步：在同步模式下，当我们使用AJAX发送完请求后，后续还有代码需要执行，我们同样将服务器响应交由另一个JS函数去处理，但是这时的代码执行情况是：在服务器没有响应或者处理响应结果的JS函数还没有处理完成return时，包含请求代码的函数的剩余代码是不能够执行的。就好比单线程一样，请求发出后就进入阻塞状态，知道接触阻塞余下的代码才会继续执行。有人说：既然异步这么好，那全部都用异步好了，同步存在还有什么意义？

# 四.同步适用情况

我们可以想一下，同步是一步一步来操作，等待请求返回的数据，再执行下一步，那么一定会有一些情况，只有这一步执行完，拿到数据，通过获取到这一步的数据来执行下一步的操作。这是异步没有办法实现的，因此同步的存在一定有他存在的道理。

# 五.如何选择同步还是异步模式

要回答这一问题，我们可以通过下面可能出现的问题来回答：**我们在发送AJAX请求后，还需要继续处理服务器的响应结果，如果这时我们使用异步请求模式同时未将结果的处理交由另一个JS函数进行处理。这时就有可能发生这种情况：异步请求的响应还没有到达，函数已经执行完了return语句了，这时将导致return的结果为空字符串。

# 六.参考

* 原文：[Ajax中什么时候用同步，什么时候用异步？](https://blog.csdn.net/xiegongmiao/article/details/78217386)