# 面试官：说说HTTP1.0/1.1/2.0的区别?

## 一、HTTP1.0
HTTP1.0是HTTP协议的第一个版本，也是第一个在通信中指定版本号的HTTP协议版本。

在HTTP1.0中，浏览器与服务器之间只保持短暂的连接，每次请求都需要与服务器建立一个TCP连接。服务器在完成请求处理后立即断开TCP连接，不保持与客户端的长连接，也不跟踪每个客户端的状态或记录过去的请求。

简单来说，每次与服务器交互都需要新开一个连接，如下图所示：

![image](https://github.com/linwu-hi/code-interview/assets/137023716/09fab051-e380-4b03-b117-b7b732e5eefd)

例如，在解析HTML文件时，如果文件中存在资源文件（如CSS、JS等），就需要创建单独的连接来获取这些资源，最终导致一个HTML文件的访问涉及多次请求和响应，每次请求都需要创建连接、建立连接和关闭连接，这种形式明显造成了性能上的缺陷。

如果需要建立长连接，需要设置一个非标准的Connection字段`Connection: keep-alive`。

## 二、HTTP1.1
HTTP1.1在HTTP1.0的基础上进行了改进和优化。

在HTTP1.1中，默认支持长连接（`Connection: keep-alive`），即在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟。

通过建立一次连接，多次请求可以由这个连接完成，如下图所示：

![image](https://github.com/linwu-hi/code-interview/assets/137023716/ac6eb492-3f9e-4207-89b6-e6c71b402bba)

这样，在加载HTML文件的时候，文件中的多个请求和响应可以在一个连接中传输，提高了性能。

此外，HTTP1.1还引入了更多的请求头和响应头来完善功能，例如：

- 引入了更多的缓存控制策略，如If-Unmodified-Since、If-Match、If-None-Match等缓存头来控制缓存策略。
- 引入了range头，允许请求资源的某个部分。
- 引入了host头，实现在一台WEB服务器上可以在同一个IP地址和端口号上使用不同的主机名来创建多个虚拟WEB站点。

同时，HTTP1.1还新增了其他的请求方法：PUT、DELETE、OPTIONS等。

## 三、HTTP2.0
HTTP2.0在相比之前版本，性能上有很大的提升，主要体现在以下几个方面：

### 1. 多路复用
HTTP2.0复用TCP连接，在一个连接上可以同时发送多个请求或回应，而且不需要按照顺序一一对应，避免了"队头堵塞"的问题。

通过复用连接，在同一个TCP连接里面，客户端可以同时发送多个请求，服务器也可以同时返回多个响应，这样大大减少了连接的建立和关闭次数，提高了并发性能，如下图所示：

![image](https://github.com/linwu-hi/code-interview/assets/137023716/3c8268e4-e201-4095-bdb7-69248ba34f1a)

### 2. 二进制分帧
HTTP2.0采用二进制格式传输数据，而非HTTP1.x的文本格式，解析起来更高效。

将请求和响应数据分割为更小的帧，并且它们采用二进制编码，这样数据传输更加紧凑高效。

HTTP2.0中，同域名下所有通信都在单个连接上完成，该连接可以承载任意数量的双向数据流。每个数据流都以消息的形式发送，而消息又由一个或多个帧组成。多个帧之间可以乱序发送，根据帧首部的流标识可以重新组装，这也是多路复用同时发送数据的实现条件。

### 3. 首部压缩
HTTP2.0使用首部表来跟踪和存储之前发送的键值对，对于相同的数据，不再通过每次请求和响应发送。

首部表在HTTP2.0的连接存续期内始终存在，由客户端和服务器共同渐进地更新。这样就实现了对HTTP1.x中冗余头部信息的压缩，减少了数据传输量，降低了开销。

例如，下图中的两个请求，请求一发送了所有的头部字段，第二个请求则只需要发送差异数据，这样可以减少冗余数据，降低开销。

![image](https://github.com/linwu-hi/code-interview/assets/137023716/3f8b53ec-11bc-43bc-9460-8763365e3cda)

### 4. 服务器推送
HTTP2.0引入了服务器推送，允许服务器将一些客户端需要的资源预先推送到客户端。

服务器可以顺便把一些客户端需要的资源一起推送到客户端，如在响应一个页面请求时，可以随同页面的其他资源一起推送。这样免得客户端再次创建连接发送请求到服务器端获取资源，提高了性能。

服务器推送适合用于加载静态资源，如图片、CSS、JS等，如下图所示：

![image](https://github.com/linwu-hi/code-interview/assets/137023716/7dc00724-2108-4b71-9ccf-cf838e8f06d1)

## 四、总结
HTTP1.0、HTTP1.1和HTTP2.0在性能和功能方面都有很大的区别。

HTTP1.0通过短连接来实现请求和响应，HTTP1.1引

入了长连接和更多的请求头和响应头来优化。

而HTTP2.0则进一步提升了性能，通过多路复用、二进制分帧、首部压缩和服务器推送等特性来优化数据传输和性能表现。

随着HTTP2.0的逐渐普及，网络通信将更加高效快速，为用户提供更好的上网体验。

## 参考文献
- https://zh.wikipedia.org/wiki/超文本传输协议#HTTP/1.0
- https://www.jianshu.com/p/52d86558ca57
- https://segmentfault.com/a/1190000016496448
- https://zhuanlan.zhihu.com/p/26559480