---
layout: post
title: "requests"
date: 2016-08-24 20:48:15 +0800
comments: true
categories: python
---
## Question
我一直以为按照meta去搞编码就ok了，然而事实上是然并卵。。例子之一是199IT的网页，看到meta charset="utf-8"
在使用requests获取网页的时候，发现搞下来的中文文本都是乱码(response.text)。于是一脸懵逼的开始转码，开始试都不行直到试到了ISO－8859-1
于是我就奇怪了，怎么搞的，然后我就开始搜索和研究。
## Search&Test
第一步自然是怀疑requests了，因为我试过用wget获取发现毫无问题。
于是就去看requests文档，发现了这么一段：

`When you receive a response, Requests makes a guess at the encoding to use for decoding the
response when you access the Response.text attribute. Requests will first check for an  encoding in 
the HTTP header, and if none is present, will use chardet to attempt to guess the encoding.
The only time Requests will not do this is if no explicit charset is present in the HTTP
headersand the Content-Type header contains text. In this situation, RFC 2616 specifies that
the default charset must be ISO-8859-1. Requests follows the specification in this case. 
If you require a different encoding, you can manually set the Response.encoding property,
or use the rawResponse.content.`

然后我打印requests的response.encoding..结果是：

    ISO－8859-1

## solved
找到问题了。。直接

    response.encoding="utf-8"

 搞定
