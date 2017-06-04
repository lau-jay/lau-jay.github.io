---
title: pem
date: 2017-03-06 23:30:39
categories: node
tags: secret
---
在现今https是一个主流，https支持本地测试的时候并不需要去找免费的或者
付费的证书，我们可以做个自签发的证书。对于开发和测试来说，已经够用了。

先生成私钥:

    openssl genrsa 1024 > key.pem

然后创建证书:

    openssl req -x509 -new -key key.pem > key-cert.pem


使用:

    var https = require('https');
    var fs = require('fs');
    var options = {
        key: fs.readFileSync('./key.pem'),
        cert: fs.readFileSync('./key-cert.pem'),
        };
    https.createServer(options.function(req, res){
        res.writeHead(200);
        res.end('hello world\n');
        }).listen(3000);
