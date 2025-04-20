# 面试积累


## http 强缓存和协商缓存
### 简单记录
1. 强缓存：浏览器不会像服务器发送任何请求，直接从本地缓存中读取文件并返回 Status Code: 200 OK;
2. 协商缓存: 向服务器发送请求，服务器会根据这个请求的request header的一些参数来判断是否命中协商缓存，如果命中，则返回304状态码并带上新的response header通知浏览器从缓存中读取资源
### 网上博客
1. [强制缓存和协商缓存](https://juejin.cn/post/6844903838768431118)


## websocket
### 网上博客
1. [websocket 简介](https://blog.csdn.net/weixin_42981560/article/details/135559337)


## HTTP2.0的特性
### 简单记录
	>* 二进制传输; 多路复用; Haeder压缩; 服务端push
### 网上博客
1. [HTTP2.0的特性](https://segmentfault.com/a/1190000019614275)


## http 请求方法
### 简单记录
1. GET 请求指定的页面信息，并返回实体主体。下载文本、图片、音视频等时获取服务器资源。
2. POST 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。提交用户注册信息。
3. PUT 从客户端向服务器传送的数据取代指定的文档的内容。
4. DELETE 请求服务器删除指定的页面。
5. HEAD 类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头
6. CONNECT HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。
7. OPTIONS 允许客户端查看服务器的性能。
8. TRACE 回显服务器收到的请求，主要用于测试或诊断。
### 网上博客
1. [博客链接](https://blog.csdn.net/demo_yo/article/details/123596028)
2. [post和get的区别](https://cloud.tencent.com/developer/article/1498283)


## 数组去重
### 简单记录
1. Map 记录
2. 利用 for 嵌套 for，然后 splice 去重
3. 新建一个空的结果数组，for 循环原数组，利用 indexOf 去重
### 网上博客
* [数组去重](https://segmentfault.com/a/1190000016418021)


## promise


## ansys 捕获异常


## 垃圾回收机制
### 简单记录
1. 标记清除; 引用计数
### 网上博客
1. [1](https://segmentfault.com/a/1190000043893470)


## css 绘制三角形
### 简单记录
1. border 绘制三角形;
2. linear-gradient 绘制三角形;
3. conic-gradient 绘制三角形
4. transform: rotate 配合 overflow: hidden 绘制三角形
5. clip-path 绘制三角形
6. Unicode 表示码绘制三角形;
### 网上博客
1. [css 绘制三角形](https://blog.csdn.net/weixin_44015669/article/details/115663156):


## 前端常见浏览器兼容性问题解决方案
### 网上博客
1. [1](https://www.cnblogs.com/moneyup/p/13721322.html)


## IE9 以下浏览器不能使用 opacity
### 网上博客
1. [IE9 以下浏览器不能使用 opacity](https://blog.csdn.net/fesfsefgs/article/details/101159339)


## react: class 组件生命周期
### 网上博客
1. [react: class 组件生命周期](https://www.runoob.com/react/react-component-life-cycle.html)


## redux 使用


## type 和 interface
### 网上博客
1. [type vs interface](https://zhuanlan.zhihu.com/p/636809203)
2. [type 与 interface 的区别，你真的懂了吗？](https://juejin.cn/post/7072945053936648200)


## typescript & 和 | 
### 网上博客
1. [typescript & 和 | ](https://blog.csdn.net/qq_52395343/article/details/134905832)


## less特性
1. [1](https://blog.csdn.net/chen__cheng/article/details/117066052)


## 个人观点
1. 光是做好自己的情况，就需要常态化的加班的话，反馈的是对项目的掌控力极弱，用辛苦加班来弥补能力的不足，试图把自己的无能给隐藏起来，从自而表现出能够胜任岗位的样子。