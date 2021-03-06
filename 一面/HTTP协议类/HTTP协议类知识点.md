# HTTP 协议的主要特点。

简单快速，灵活，无连接，无状态。

无连接：只连接一次就断开。

无状态：客户端和服务端状态是分开的。

# HTTP 报文的组成部分。

请求报文：请求行，请求头，空行，请求体。

响应报文：状态行，响应头，空行，响应体。

空行代表标识下面要传输请求体或相应体了。

# HTTP 方法。

get:获取资源

post:传输资源

put:更新资源

delete:删除资源

head:获得报文首部

options:预检请求

patch:对资源进行部分修改，属于对put请求的补充

# GET 与 POST 的区别。

1. get 在浏览器回退时是无害的，而 post 会再次请求。
2. get 产生的 URL 地址可以被收藏，而 post 产生的地址不能被收藏。
3. get 请求会被浏览器主动缓存，而 post 不会，除非手动设置。
4. get 请求传送的参数有长度限制（大概 2kb），而 post 没有。
5. get 比 post 更不安全。
6. get 请求参数会被完整的保留在浏览器的历史记录里，而 post 不会。
7. 重点： GET 产生一个 TCP 数据包，POST 产生两个 TCP 数据包。
8. GET 请求服务器直接响应 200，POST 先发送 header，服务器响应 100，然后再发送 data，服务器响应 200。

> 注：GET 和 POST 本质上没有区别，底层都是 TCP/IP 的链接，只是传输数据规则不同。

# HTTP 状态码说明。

1xx：指示信息

2xx：成功

3xx：重定向

4xx：客户端错误

5xx：服务器错误

举例：200 客户端请求成功，301 所请求的页面已经转移到新的 URL， 304 客户端有缓冲的文档并发出了一个条件性的请求，服务端告诉客户端，原来缓冲的文档还可以继续使用，403 请求页面的访问被禁止，404 请求资源不存在，503 请求未完成。

# 什么是持久化连接

HTTP1.1 版本是支持持久化连接的，非 keep-alive 模式时，每个请求/应答服务器和客户端都要新建一个连接，完成之后立即断开连接。 当使用 keep-alive 模式时，客户端到服务器的连接持久有效。

# 什么是管线化。

在 HTTP1.1 版本持久连接的情况下，请求和响应都是打包完成的，客户端一次发送多个请求，服务器一次响应多个请求。 只有 get 和 head 请求支持管线化。

# HTTP、HTTPS、HTTP2对比。

* HTTP协议运行在TCP之上，HTTPS协议运行在SSL/TLS之上，SSL/TLS之上协议运行在TCP之上
* HTTP协议默认端口80，HTTPS协议默认端口443
* HTTP2采用新的二进制进行传输，HTTP1.X采用文本传输
* HTTP1.x每一个请求都会建立一个链接，HTTP2实现了多路复用多个请求共享一个TCP链接
* HTTP2对header进行了缓存和压缩
* HTTP2可以进行服务端主动推送

# CORS机制

* 需要浏览器和服务器同时支持

* 浏览器将CORS请求分为两类：

  * 简单请求：请求方法为GET、POST、HEAD，并且请求头不能超出以下几种：Accept、Accept-Language、Content-Language、Last-Event-ID和Content-Type只能为：application/x-www-form-urlencoded、multipart/form-data、text/plain

  * 非简单请求：请求方法为PUT或DELETE，或者请求头Content-Type为application/json

* 对于简单请求：

  * 浏览器直接发出CORS请求，会在请求头中增加Origin字段，用来标识请求来自哪个源

  * 如果指定的Origin，不在服务器的许可范围内，服务器会返回一个正常的HTTP响应（状态码有可能是200），但是响应头里没有Access-Control-Allow-Origin字段，浏览器发现缺少Access-Control-Allow-Origin，就会抛出一个错误（可以被XMLHttpRequest的onerror捕获到）

  * 如果指定的Origin，在服务器的许可范围内，服务器的响应头中会有增加三个字段：Access-Control-Allow-Origin、Access-Control-Allow-Credentials和Access-Control-Expose-Headers

* 对于非简单请求：
  * 浏览器会在正式通信之前先发出Options预检请求，预检请求的请求头会增加Origin、Access-Control-Request-Method和Access-Control-Request-Headers这三个字段
  * 如果预检请求失败，会返回一个正常的HTTP请求，但是没有任何CORS相关字段，浏览器发现没有CORS相关字段，就会抛出一个错误（可以被XMLHttpRequest的onerror捕获到）
  * 预检请求成功，就会跟简单请求一样来通信

* Options请求优化
  * 将非简单请求变为简单请求
  * 设置Access-Control-Max-Age字段，浏览器会根据这个请求头字段来判断OPTIONS预检请求的响应结果要缓存多久（对于相同请求来说，在指定时间内，只会触发一次预检请求）
