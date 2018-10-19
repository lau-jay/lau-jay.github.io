---
title: ASGI规范中文版
date: 2018-10-17 07:00:39
categories: python
tags: python3
---
# ASGI(Asynchronous Server Gateway Interface) Specification
*Version*:2.0(2017-11-28)

## 摘要

本文档描述了基于网络协议服务器（尤其是web服务器）和 Python 应用之间的标准接口，希望能够处理多种通用协议(包括 HTTP、HTTP2 和 WebSocket)。

这个基本规范目的在于定义这些服务器交互并运行应用的API集合；每个支持的协议（例如HTTP) 都有一个子规范，概述如何将协议编码和解码成信息。

## 依据

WSGI 规范自诞生以来应用广泛，让 Python 框架和 web 服务的选择上有很大的自由。然而，因为设计的与 HTTP 风格的请求响应模型的耦合太强，加上越来越多不遵循这种模式的协议逐渐成为 web 编程的标准之一，比如说，WebSocket。
ASGI 尝试在保证应用接口简单的前提下，提供一个允许数据能够在任意时候、被任意应用进程发送和接受的抽象。

它同样采用将协议转换为Python兼容，异步友好的消息集的原则，并将其概括为两部分：一个关于构建服务器的标准通信接口，以及一组用于每个协议的标准通信协议。

它主要的目标是提供一个编程HTTP/2 和WebSocket，以及一般的HTTP应用代码的方式。然而，

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

每个应用实例都映射到一个独立的“socket”或者连接，如果要进行清理操作，预计会在该连接的生命域再加一段时间。 有些协议可能不使用传统的套接字; ASGI针对这些协议将定义范对应的（实例）生命域以及何时关闭。

## 规范细节
### 连接域
用户到ASGI应用的每个连接都将产生一个应用实例。连接的持续域与建立连接时获得得信息，统称为*连接域*。

例如： 在HTTP连接域下只会持续一个请求，但是它已经包含大多数的请求信息(除了HTTP request body外，因为它通过流来传递事件)。

但是，在WebSocket协议里，只要socket连着，连接就会不断持续着。这个域包含的信息像是Websocket的路径，但是传入的信息等细节则是以事件的形式出现的。

有些协议可能会提供一个连接，但只有非常有限的信息，因为他们封装了握手之类的东西。每个协议定义都必须包含关于连接持续多长的信息，与能从中获得哪种类型的信息。

应用程序在被初始化与给定连接域时不能与客户端进行通信；他们必须等待到事件循环根据协议规范运行，可能必须等到初次打开的信息。

## 事件

ASGI将协议分解为一系列应用程序必须响应的*事件*，这个与有序的两个事件一样简单-`http.request`与`http.disconnect`。针对像WebSocket，可能是`websocket.connect`,`websocket.receive`,`websocket.receive`,`websocket.disconnect`。

每个事件都是一个`dict`并带着一个顶层`type`key，包含信息类型的一个unicode 字符串。使用者可以自由的建立他们自己的消息类型，并在应用实例间发送他们以处理高级事件-举个例子，一个聊天程序可能会发送一个`mychat.message`类型的聊天信息。预估饮用程序可以处理多种事件类型混合的事件集，其中一些来自客户端连接，另外一些则来自应用的其他部分。

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

