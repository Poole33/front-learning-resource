## HTTP/1.1 长连接模式
> 其实应该叫做`TCP长连接`，因为`HTTP`本身只有请求和响应，发送靠`TCP`。不同域名，会创建不同的`TCP`连接

**有了长连接，对于同一个连接的`HTTP请求`是如何发送的呢？**  
  
`浏览器`允许`并发`发送`HTTP`请求（使用Pipelining技术，`HTTP/1.1`本身不支持`并发`），chrome可以同时最多并发6个，后面的请求要等前面等请求完成，才会继续发送（在`TCP`上发送`HTTP`请求会有先后次序，不是并发）

**HTTP/1.1的长连接实现**  
+ `Connection` 请求头
  + `keep-alive` 开启长连接（默认值）
  + `close` 关闭长连接
+ `Connection` 响应头
  + `keep-alive` 开启长连接
  + `close` 关闭长连接

设置了`keep-alive`的连接也会断开，服务端有一个`保活机制`来确保连接的情况。连接有一个保持时间，这个时间由服务端进行设置。因为有`请求头`和`响应头`，所以`HTTP/1.1`要实现`长连接`需要服务端的配合。

## 数据协商
### 请求头
+ `Accept` 客户端想要什么类型等返回数据。有默认值
+ `Accept-Encoding` 客户端支持什么压缩格式。默认值：gzip、deflate、br
+ `Accept-Language` 客户端想要什么样语言，默认根据系统和浏览器的语言设置
+ `user-Agent` 浏览器的相关信息，PC、mobile。有默认值

### 响应头
+ `Content-Type` 告诉客户端，服务端返回的文件类型
+ `Content-Encoding` 告诉客户端，服务端对文件使用的压缩格式
+ `Content-Language` 告诉客户端，服务端返回对语言类型
+ `X-Content-Type-Options` 在没有Content-Type的情况下，告诉浏览器不要预测我返回的数据类型（不经常用）



## 重定向Redirect
> 浏览器访问的地址被重定向其他地址


**location 响应头**  
> 服务端告诉客户端目前访问的地址被重定向到那个地址。

**例如：**  
+ 访问 `http://parent.com`
+ HTTP请求的`状态码`为`302`
+ 响应头`location:` `http://child.com`
+ 浏览器跳转到`http://child.com`


**`location`响应头需要状态码配合**  
+ `301` 永久重定向
  第一次访问原地址，然后跳转到新地址，之后就直接访问新地址。浏览器直接在发送`HTTP请求`的时候就去请求新地址（要慎用，设置了之后，想反悔就不受服务端的控制。需要用户清除本地缓存）
+ `302` 临时重定向
  先访问原地址，再跳转新地址


## 内容安全策略CSP
> [MDN介绍](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP)

**Content-Security-Policy 响应头**  
> `CSP`的主要目标是减少和报告`XSS 攻击`，通过设置`Content-Security-Policy 响应头`，来指定站点可以执行的脚本文件的来源

也可以通过`meta标签`的形式来设置限制，不能使用`report-uri`，推荐`CSP`在`HTTP`的`header`中使用
```html
<meta http-equiv="Content-Security-Policy" content="....">
```
