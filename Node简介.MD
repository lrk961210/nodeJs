## 事件驱动编程风格
当感兴趣事件发生时，有系统调用的函数来取代应用返回值的变成风格，被称为事件驱动编程或一步编程
即 回调函数

## 闭包
使用函数外层的变量
由于全局变量会污染环境，可以使用如下方法创建一个作用域
```
	(function(){
		// 这里形成了一个局部作用域
	}( ))
```

回调函数其实是一种闭包
