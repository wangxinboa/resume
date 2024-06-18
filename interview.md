# 面试题积累

## http

* [强缓存和协商缓存](https://juejin.cn/post/6844903838768431118)
	>* 强缓存：浏览器不会像服务器发送任何请求，直接从本地缓存中读取文件并返回 Status Code: 200 OK;
	>* 协商缓存: 向服务器发送请求，服务器会根据这个请求的request header的一些参数来判断是否命中协商缓存，如果命中，则返回304状态码并带上新的response header通知浏览器从缓存中读取资源；

* [websocket 简介](https://blog.csdn.net/weixin_42981560/article/details/135559337)

* [HTTP2.0的特性](https://segmentfault.com/a/1190000019614275)
	>* 二进制传输; 多路复用; Haeder压缩; 服务端push

* [HTTP 请求方法](https://blog.csdn.net/demo_yo/article/details/123596028)
	>1. GET 请求指定的页面信息，并返回实体主体。
	>2. HEAD 类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头
	>3. POST 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。
	>4. PUT 从客户端向服务器传送的数据取代指定的文档的内容。
	>5. DELETE 请求服务器删除指定的页面。
	>6. CONNECT HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。
	>7. OPTIONS 允许客户端查看服务器的性能。
	>8. TRACE 回显服务器收到的请求，主要用于测试或诊断。

* [post和get的区别](https://cloud.tencent.com/developer/article/1498283)


## js api

* [数组去重](https://segmentfault.com/a/1190000016418021)

	>* Map 记录
	>* sort 排序

* promise

* ansys 捕获异常

* 垃圾回收机制
	>* 标记清除; 引用计数
	>* [1](https://segmentfault.com/a/1190000043893470)

## css

* [css 绘制三角形](https://blog.csdn.net/weixin_44015669/article/details/115663156):
	>1. border 绘制三角形;
	>2. linear-gradient 绘制三角形;
	>3. conic-gradient 绘制三角形
	>4. transform: rotate 配合 overflow: hidden 绘制三角形
	>5. clip-path 绘制三角形
	>6. Unicode 表示码绘制三角形;


## 浏览器

* 前端常见浏览器兼容性问题解决方案
	>* [1](https://www.cnblogs.com/moneyup/p/13721322.html)

* [IE9 以下浏览器不能使用 opacity](https://blog.csdn.net/fesfsefgs/article/details/101159339)


## react

* [react: class 组件生命周期](https://www.runoob.com/react/react-component-life-cycle.html)

* redux 使用

## typescript

* [type 和 interface 的区别](https://blog.csdn.net/TIAN20121221/article/details/120085998)

* [typescript & 和 | ](https://blog.csdn.net/qq_52395343/article/details/134905832)


## less

* less特性
	>* [1](https://blog.csdn.net/chen__cheng/article/details/117066052)
