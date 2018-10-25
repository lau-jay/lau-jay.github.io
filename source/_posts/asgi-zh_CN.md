---
title: ASGI规范中文版
date: 2018-10-17 07:00:39
categories: python
tags: python3
---
# 异步服务网关接口(Asynchronous Server Gateway Interface) 规范
*Version*:2.0(2017-11-28)

## 摘要

本文档描述了基于网络协议服务器（尤其是web服务器）和 Python 应用之间的标准接口，希望能够处理多种通用协议(包括 HTTP、HTTP2 和 WebSocket)。

这个基本规范目的在于定义这些服务器交互并运行应用的API集合；每个支持的协议（例如HTTP) 都有一个子规范，概述如何将协议编码和解码报文。

## 依据

WSGI 规范自诞生以来应用广泛，让 Python 框架和 web 服务器的选择上有很大的自由。然而，因为设计的与 HTTP 风格的请求响应模型的耦合太强，加上越来越多不遵循这种模式的协议逐渐成为 web 编程的标准之一，比如说，WebSocket。
ASGI 尝试在保证应用接口简单的前提下，提供一个允许数据能够在任意时候、被任意应用进程发送和接受的抽象。

它同样采用将协议转换为Python兼容，异步友好的消息集的原则，并将其概括为两部分：一个关于构建服务器的标准通信接口，以及一组用于每个协议的标准通信协议。

它主要的目标是提供一个编写HTTP/2 和WebSocket，以及一般的HTTP应用代码的方式。然而，

这种设计一部分是确保有简单的途径来兼容现有的WSGI服务器与应用代码，为绝大多数依赖WSGI的Python web 项目提供一个继续运行的简单方式，这是很重要的一点。具体的细节在ASGI-HTTP规范里描述。

## 总览

ASGI由两个不同的组件组成

* 一个*协议服务* ，其用来终止sockets与转换他们成为一个连接于每个连接的事件信息
* 一个*应用代码* ，它位于协议服务内，每个连接都会实例化一次，并在事件发生时进行处理

与WSGI类似的是，应用运行于服务容器中，服务容器以标准格式向应用发送请求。然而与WSGI不同的是，应用是实例化的对象，他们是处理事件，而非简单可调用的，并且他们必须是兼容`asyncio`的协程（在主线程上，如果需要运行同步的代码，可以自由的使用线程或其他进程）

与WSGI不同的是，一个ASGI有两个独立的连接部分：

* *连接域* ，代表着一个协议连接到一个用户，并持续到连接被关闭
* 事件，发生在连接时发送给应用

应用在一个连接域内被实例化，然后在事件循环中被触发执行处理事件，最后发送数据给客户端。

每个应用实例都映射到一个独立的“socket”或者连接，如果要进行清理操作，预计会在该连接的预设生存期再加一段时间。 有些协议可能不使用传统的套接字; ASGI针对这些协议将定义范对应的（实例）生命域以及何时关闭。

## 规范细节
### 连接域
用户到ASGI应用的每个连接都将产生一个应用实例。连接的持续域与建立连接时获得得信息，统称为*连接域*。

例如： 在HTTP连接域下只会持续一个请求，但是它已经包含大多数的请求信息(除了HTTP request body外，因为它通过流来传递事件)。

但是，在WebSocket协议里，只要socket连着，连接就会不断持续着。这个域包含的信息像是Websocket的路径，但是传入的信息等细节则是以事件的形式出现的。

有些协议可能会提供一个连接，但只有非常有限的信息，因为他们封装了握手之类的东西。每个协议定义都必须包含关于连接持续多长的信息，与能从中获得哪种类型的信息。

应用程序在被初始化与给定连接域时不能与客户端进行通信；他们必须等待到事件循环根据协议规范运行，可能必须等到初次打开的信息。

## 事件

ASGI将协议分解为一系列应用程序必须响应的*事件*，这有序的两个事件很简单-`http.request`与`http.disconnect`。针对像WebSocket，可能是`websocket.connect，websocket.send，websocket.receive，websocket.disconnect`。

每个事件都是一个`dict`并带着一个顶层`type`key，包含信息类型的一个unicode 字符串。使用者可以自由的建立他们自己的消息类型，并在应用实例间发送他们以处理高级事件-举个例子，一个聊天程序可能会发送一个`mychat.message`类型的聊天信息。预估应用程序可以处理多种事件类型混合的事件集，其中一些来自客户端连接，另外一些则来自应用的其它部分。

因为这些信息可能来自其它网络，他们需要被序列化，因此信息被要求只能包含以下几种类型：

* Byte strins
* Unicode strings
* Integers (within the signed 64 bit range)
* Floating point numbers (within the IEEE 754 double precision range, no `Nan` or infinities)
* Lists (tuples should be encoded as lists)
* Dicts (keys must be unicode strings)
* Booleans
* `None`

## 应用

ASGI应用被定义成可调用的:

`application(scope)应用（域）`

* `域`: 连接域，一个至少包含一个`type`key所指定传入协议的字典 

每当一个新的连接进入协议服务器，这个第一个可调用的对象在有新连接时会被调用并建立一个新的*实例* （这个实例时一个新的对象，第一次被调用时候返回）

这个调用时一个同步的，且不同包含阻塞调用（建议只作为存储的域）。假如你需要执行阻塞的工作，则必须在下一个可以调用的开始时执行它，然后你的应用才会进入await等待事件执行完。

他必须返回另一个，awaitable的调用：

```python
协程 application_instance(receive, send)
```

* `receive`，一个awaitable调用的函数，当可用的时候他会` yield` 一个新的事件字典事件

* `send`,  一个awaitable调用的单个事件字典作为一个位置参数，一但发送完成就会返回

这个设计也许更容易被认为是一个可能的实现，像是一个类:

```Python
class Application:
    def __init__(self, scope):
        self.scope = scope
    async def __call__(self, receive, send):
        ...
```

应用程序接口一般情况下传入两个可调用对象，看起来会像是工厂函数(类)或者是基于分配器来提供更好的可扩展性。

无论scope或者你接收或发送的报文格式都是基于应用协议的。scope必须是一个dict，键scope["type"]始终存在，并可用于确定使用的协议类型。

协议特定的子规范包含了这些scope和报文的格式。他们相当于WSGI规范定义的字典中的`environ`键。

## 协议规范

这里描述各种协议的标准化域和报文格式。

一个在所有的域和报文中都存在的公共键是`type`, 用于表明正在接收哪种类型的域或者报文。

在域中，`type`键必须是一个unicode字符串，像是"http"或者是"websocket",如相关协议规范中的定义所示。

在 报文中，类型应命名为`protocol.message_type`，其中协议与域类型匹配，`message_type`由协议规范定义。消息类型值的示例包括`http.request`和`websocket.send`

当前协议规范:

* [HTTP and WebSocket](https://github.com/django/asgiref/blob/master/specs/www.rst)

##  中间件

有可能会有ASGI中间件，代码同时具有服务器和应用两种角色， 又接受一个域和发送/接收的awaitables，可能修改这个awaitables，然后调用更底层的应用。

当一个中间件修改域的时候，应该要在域对象的副本进行改变，并将副本传入底层应用之前进行修改，否则更改可能影响上层。需要注意的是，您不应该假设您传给应用的域的副本是其最终使用的域的副本，因为底层可能是其他中间件；因此，不要保留对它的引用，并尝试将它改变为传递的初始ASGI构造器可调用的的域。

值得注意的是，ASGI应用的一部分域同步运行，它的目的在于与Python类构造函数兼容。如果你需要将对象放入阻塞/异步工作的域中，则可以让他们是awaitable的，或着在协程的入口创建可以填写的对象（记住，对象必须是可以修改的；您无法保留对域的引用并尝试稍后添加键）。

## 错误处理

如果服务器收到一个无效的事件字典-比如，一个未知类型，缺少应该有的键，或者用错了Python对象的类型（比如对于HTTP headers Unicode字符串），它应该抛出一个awaitable的异常 `send`给应用。

如果应用收到无效的事件字典，`receive`应该引发异常。

在这两种情况下，事件字典中的其他键的存在都不应该引发异常。这是为了允许随着事件的推移对协议规范进行不间断的升级。

服务器可以自由的展示错误，这些错误会从他们正在运行的应用实例中抛出，但是他们希望在控制台打印log，发送到系统日志或者其他操作-但是如果发生这种情况，他们必须终止应用程序实例以及其关连的连接。

## 扩展

有时候基于ASGI协议的服务器可能希望在核心ASGI协议规范之外提供特定服务器的扩展，或者在规范的更改被发布之前进行试运行。

对于这种情况，我们定义了一个通用模式`extensions`-对协议规范的名称添加是可选的，但如果由服务器提供并由应用程序解析，则可用于获取更多功能。

这是通过字典中的一个 `extensions`字段，其本身值就是一个字典。扩展名由一个在服务器和应用程序之间达成一致的unicode字符串名称。

如果服务器支持扩展名，它应该在`extensions`值对应的字典内加入一个扩展名，扩展名对应的值也是一个字典。

服务器可以提供任何额外的域信息，这些信息属于该字典值中扩展的那部分，或者如果扩展只是表明服务器通过`send` 可调用函数接受附加事件，则它可能是一个空字典。

As an example, imagine a HTTP protocol server wishes to provide an extension that allows a new event to be sent back to the server that tries to flush the network send buffer all the way through the OS level. It provides an empty entry in the extensions dict to signal that it can handle the event:

例如，假設 HTTP 協議服務器希望提供一個擴展，允許將新事件發送回嘗試刷新網絡發送緩衝區的服務器， 直到整個操作系統級別。它在擴展詞典中提供一個空的條目來表明它可以處理該事件:

一个例子是，假设HTTP协议服务器希望提供一个扩展，允许将新事件发送回尝试通过操作系统刷新网络发送缓存区的服务器。它在扩展字典中提供一个空的字段来表明它可以处理该事件:

```python
scope = {
    "type": "http",
    "method": "GET",
    ...
    "extensions": {
        "fullflush": {},
    },
}
```

如果应用获取到这个空字段，它就知道它可以发送自定义事件（通过类型为`http.fullflush`的`send`调用）。

### 字符串与Unicode

在本文档和所有子规范中，字节字符串指的是 bytes Python3中的类型。Unicode字符串指的`str`是Python3中的类型。

这个文档永远不会指定字符串-所有string都是两种确切类型之一。

所有的字典key（包括域和事件）都是unicode字符串。

## Version History

- 2.0 (2017-11-28): Initial non-channel-layer based ASGI spec

## Copyright

This document has been placed in the public domain.