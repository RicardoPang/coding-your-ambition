---
title: HTTP协议
date: 2021-01-11 21:27:17
tags: HTTP
---

**概念：** HTTP（Hyper Text Transfer Protocol）超文本传输协议是用于从服务器传输超文本到客户端的传输协议，基于 TCP/IP 通信协议，遵循客户端-服务器端模型。

**特点：**

- 简单可扩展：报文可读，headers 让协议扩展非常容易。
- 无状态：同一个连接，两个执行成功的请求之间没有关系。但是可以通过 Cookies 创建有状态的会话。
- 暖连接比冷连接更高效：HTTP/2 通过在一个连接中复用消息保持暖连接。
- 支持 B/S 及 C/S 模式。

**重点掌握：** _头_、_请求方法_、_返回码_、_Cookie_、_缓存_、_会话_、_链接管理_ 。

## HTTP 头

_HTTP_ 消息头允许客户端和服务器端通过**request**和**response**传递附加信息。书写格式：名称 : 值 （如 Accept: application/json）。

- 一般头：适用于请求和响应消息，但与最终消息主体传输的数据无关。
- 请求头：包含关于获取资源和客户端本身信息。
- 响应头：包含有关响应的补充信息。
- 实体头：包含有关事例主题信息。

**端到端消息头：** 这类消息头必须被传输到最终的消息接收者（服务器或客户端）。必须转发未经修改的消息头、缓存。

**逐跳消息头**：这类消息头针对单次传输，不能通过代理或缓存转发。

**消息头及其用途:**

- Accept: 用户代理期望的 MIME 类型列表。
- Accept-Encoding: 用户代理支持的压缩方法。
- Accept-Language：用户代理期望的页面语言。
- Connection：网络连接状态，如果是 keep-alive，则连接是持久的。
- Authorization：包含用服务器验证用户代理的凭证。
- Content-Type：指示服务器文档的 MIME 类型，帮助用户代理处理接收到的数据。
- Cache-Control：指示请求和响应的缓存方式。

## HTTP 的请求方法

**概念：** 表明对给定资源执行的操作。包括 **GET** **POST** **HEAD** **OPTIONS** **PUT** **DELETE** **TRACE** **CONNECT** 。

- **GET** 用于获取数据。

```
// 完整请求URL
Request URL: http://10.0.0.136:8090/api/honorws/v2/honor/customer/search/customers?pageSize=20&sortCode=byDateDesc&companyType=PERSONAL&currentPage=0
// 请求类型
Request Method: GET
// 状态码
Status Code: 200 OK
// 请求的目的地.User-Agent
Remote Address: 10.0.0.136:8090
// HOST将指出请求的目的地.User-Agent,服务器端和客户端脚本都能访问它,它是浏览器类型检测逻辑的重要基础.该信息由你的浏览器来定义,并且在每个请求中自动发送等等
Host: 10.0.0.136:8090
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36
```

   <!-- more -->

- **HEAD** 与 GET 相同，但没有响应体。

- **POST** 方法用于将实体提交到指定的资源，通常导致状态或服务器上的副作用的更改。(副作用是指请求多次可能具有额外效果)。

```
// 请求行，post请求
Request URL: http://10.0.0.136:8090/api/honorws/v2/honor/customer/register
Request Method: POST
// 请求头部
Accept: application/json
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Authorization: Bearer 76607c40-6349-4ddb-935b-374a89375345
Connection: keep-alive
Content-Length: 790
Content-Type: application/json
Host: 10.0.0.136:8090
Origin: http://10.0.0.136:8090
Referer: http://10.0.0.136:8090/
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36
// 请求数据，单独放在body中
```

- **PUT** 从客户端向服务器传送的数据取代指定的文档的内容。（没有副作用：连续调用一次或几次具有相同的效果）。
- **DELETE** 方法删除指定的资源。
- **CONNECT** 方法建立一个到由目标资源标识的服务器的隧道，能够将连接改为管道方式的代理服务器。
- **OPTIONS** 方法用于描述目标资源的通信选项，允许客户端查看服务器性能。
- **TRACE** 方法沿着到目标资源的路径执行一个消息环回测试，回显服务器收到的请求，用于测试或诊断。
- **PATCH** 方法用于对资源应用部分修改。

## HTTP 的返回码

**概念：** HTTP 响应状态代码指示请求是否已成功完成。状态码有三位数字组成，第一个数字定义了相应的类别，共分五类：信息响应，成功响应，重定向，客户端错误和服务器错误。

- 1xx：指示信息--表示请求已接收，继续处理
- 2xx：成功--表示请求已被成功接收、理解、接受
- 3xx：重定向--要完成请求必须进行更进一步的操作
- 4xx：客户端错误--请求有语法错误或请求无法实现
- 5xx：服务器端错误--服务器未能实现合法的请求

**常见状态码：**

```html
200 OK //客户端请求成功 400 Bad Request
//客户端请求有语法错误，不能被服务器所理解 401 Unauthorized
//请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用 403 Forbidden
//服务器收到请求，但是拒绝提供服务 404 Not Found
//请求资源不存在，eg：输入了错误的URL 500 Internal Server Error
//服务器发生不可预期的错误 503 Server Unavailable
//服务器当前不能处理客户端的请求，一段时间后可能恢复正常
```

## HTTP Cookie

**概念：** 服务器发送到客户端并保存到本地的一小块数据，下次随请求一同发送服务器。

**作用：**

- 会化状态管理（如用户登录状态、购物车、游戏分数或其他需要记录的信息）。
- 个性化设置（如用户自定义设置）。
- 流浪器行为跟踪（如跟踪分析用户行为）。

**创建：** 服务器在响应头使用 **Set-Cookie** 保存客户端发送来的信息。

**会话期 Cookie：** 随浏览器有效或删除。

**持久性 Cookie：** 可指定过期时间(Expires)或有效期(Max-Age)。

**Secure 和 HttpOnly 标记：** 前者表示只支持 HTTPS，但不确保安全；后者表示只能发送到服务器，JavaScript 的 API 不能访问。

**作用域：** Domain 和 Path 定义 Cookie 作用域，默认当前文档的主机。

**安全：** 切记不能通过 HTTP Cookie 存储、传输敏感信息。

- 会话劫持和 XSS：窃取 Cookie 用户或授权会话，利用社会工程学攻击和利用应用程序漏洞进行 XSS 攻击。
- 跨站请求伪造（CSRF）：点击不安全聊天室或论坛图片，实际却是给银行服务器发送体现请求。

> - 预防：

    1. 对用户输入进行过滤阻止XSS；
    2. 敏感操作认证；
    3. 敏感信息Cookie设置短生命周期
    4. ......

## HTTP 缓存

**概念：** 保存资源副本并在下次请求时直接使用该副本的技术。分为私有和共享缓存。有利于提升性能。

**缓存控制：** Cache-Control

- no-store：禁止缓存，每次请求都会下载完整响应内容。
- no-cache：强制确认缓存。检查缓存是否过期，未过期则使用缓存。
- 私有缓存和公共缓存：**public** 可以被任何中间人缓存。**private** 专用于单个用户。
- max-age：能被缓存的最大时间，指距离从请求发起的时间秒数。
- must-revalidate：缓存验证确认。使用资源时必须先确认状态，过期不用。
- 新鲜度：如果检测到有缓存，请求头附带 **If-None-Match** 到服务器，如果返回 **304 Not Modified** 表示新鲜
- Vary：确认请求新的资源还是使用缓存文件。只有当前请求和缓存请求头跟缓存响应头里的 Vary 匹配才使用缓存。

## HTTP 会话

典型 **Client-Server（客户端 - 服务器）** 会话：

- 客户端建立 TCP 连接
- 客户端发送请求并等待应答
- 服务器处理请求并送回应答，包括状态码和对应数据。

**注意：** 从 HTTP/1.1 开始，连接完成第三阶段后不再关闭，等待客户端再次发起新请求。

## HTTP/1.x 连接管理

**概念：** 打开和保持连接影响应用性能。有短连接、长连接和 HTTP 流水线模型。

**模型：**

- 短连接：每次请求都要与 TCP 握手
- 长连接：完成握手第三阶段后，保持连接（时间长度为 keep-alive 设置的最小时间），但空闲状态还会消耗 性能。
- 流水线：在一条长连接上发送请求，不需要等待应答返回。

**域名分片：** 为每个域名建立多个连接以实现并发请求。不需要用这个过时技术，HTTP/2 完美解决。

## HTTP 访问控制(CORS)

**概念：** 跨域资源共享(**CORS**) 是使用 HTTP 头告诉客户端 让应用访问不同源服务器上指定资源的机制。

**什么时候需要 CORS？** 除了非简单请求，都需要首先使用 **OPTION** 发送预检请求，服务器允许后才发起实际请求

**简单请求：**

- 使用 **GET** 、**HEAD** 、**POST**
- 使用安全首部字符集合 **Accept**、**Accept-Language**、**Content-Language**、**Content-Type**、**DPR**、**Downlink**、**Save-Data**、**Viewport-Width**、**Width**
- Content-Type 仅限于 **text-plain**、**multipart-form-data**、**application/x-www-from-urlencoded**

**预检请求：**

- 非简单请求
- 请求对象注册人一多个事件监听器
- 请求中使用 **ReadableStream** 对象
- 预检大多不支持重定向，若报错可以先发简单请求获取真实地址然后发送真正请求或服务器更改。

**附带身份凭证请求：**

- 利用 Cookie 和认证信息发送身份凭证
- 服务器不得设置 **Access-Control-Allow-Origin** 为"\*"，只能设为具体 URI。

**响应首部字段：**

| 字段名                                                       | 作用                                                                    |
| ------------------------------------------------------------ | ----------------------------------------------------------------------- |
| Access-Control-Allow-Origin: <origin> \| \*                  | 指定允许访问的 URI                                                      |
| Access-Control-Expose-Headers                                | 设置除可以默认访问响应头外的其他响应头                                  |
| Access-Control-Max-Age                                       | 请求结果缓存时间                                                        |
| Access-Control-Allow-Credentials                             | 当浏览器的 credentials 设置为 true 时是否允许浏览器读取 response 的内容 |
| Access-Control-Allow-Methods: <method>[, <method>]\*         | 实际请求允许使用的 HTTP 方法                                            |
| Access-Control-Allow-Headers: <field-name>[, <field-name>]\* | 实际请求允许携带的首部字段                                              |

**请求首部字段：**

| 字段名                                                         | 作用                             |
| -------------------------------------------------------------- | -------------------------------- |
| Origin                                                         | 请求源站                         |
| Access-Control-Request-Method: <method>                        | 将实际使用的 HTTP 方法告诉服务器 |
| Access-Control-Request-Headers: <field-name>[, <field-name>]\* | 实际请求携带的首部字段告诉服务器 |
