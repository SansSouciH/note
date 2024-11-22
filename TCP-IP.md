# HTTP

## Request

> 在Servlet服务器Tomcat中使用**HttpServletRequest**类来操控请求中的所有参数

| 名称            | 作用                                                     |
| --------------- | -------------------------------------------------------- |
| Host            | 主机名                                                   |
| User-Agent      | 浏览器版本                                               |
| Accept          | 浏览器能接收的资源类型：text/*, image/*或*/*表示接收所有 |
| Accept-Language | 浏览器偏好语言，服务器据此返回不同语言的网页             |
| Accept-Encoding | 浏览器支持的压缩类型，例gzip，deflate等                  |
| Content-Type    | 请求主体的数据类型                                       |
| Content-Length  | 请求主体的大小（单位：字节）                             |

**Get请求**

```http
*请求行*
GET /brand/findAll?name=Xiaomi&status=1 HTTP/1.1
*请求头*
Accept: text/html,application/xhtml+xml,application/xml;
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Content-Length: 161
Host: localhost:8080
User-Agent: xxxxxxxxxxxxxxxxxChrome/...
```

**Post请求**

```http
*请求行*
Post /brand HTTP/1.1
*请求头*
Accept: application/json, text/plain, */*
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Content-Length: 161
Content-Type: application/json;charset=UTF-8
Cookie: xxxxxxxxxxxxxxxxxxxxxxxxx;JSESSIONID=XXXXXXXXXXXX
Host: localhost:8080
User-Agent: xxxxxxxxxxxxxxxxxChrome/...
*请求体*
{"status":1,"brand":"Xiaomi","description":"描述"}
```

## Respones

> 在Servlet服务器Tomcat中使用**HttpServletRespones**类来操控请求中的所有参数

* 1xx：响应中-临时状态码
* 2xx：成功
* 3xx：重定向
* 4xx：客户端错误
* 5xx：服务端错误

| 名称             | 作用                                               |
| ---------------- | -------------------------------------------------- |
| Content-Type     | 响应内容的类型，text/html, application/json        |
| Content-Length   | 响应内容的长度（字节）                             |
| Content-Encoding | 响应传输数据使用的压缩算法，例gzip                 |
| Cache-Control    | 指示客户端如何缓存，例max-age=300表示最多缓存300秒 |
| Set-Cookie       | 告诉浏览器为当前页面所在的域设置cookie             |

