---
title: Nginx升级https
date: 2017-12-28 21:38:29
categories: Nginx
tags: note
---
## 升级https
最近需要将域名升级为https, 以满足安全性和一些第三方接口的要求。
目前的技术栈为前端VUE, 后端为Tornado。Tornado负责接口的实现。
## 遇到的问题
首先需要解决的是证书, 关于这点acme.sh解决了这个问题, 详细内容可以看参考。
当生成证书, 写好nginx配置的时候, 测试配置的时候直接报错`unknown directive "ssl"`。
很明显ssl不支持, 查看配置确实是最初编译的时候没加入http_ssl_module,
随后重新编译加上`--with-http_ssl_module`, 于是就解决了这个问题。
剩下的其实就很顺利, 参考文章说的很详细的。

## 配置Nginx
```
server {
    listen       443 ssl;
    server_name  subdomain.dommain.com;

    ssl_certificate      /home/work/path/subdomain.domain.com.key.pem;
    ssl_certificate_key  /home/work/path/subdomain.domain.com.key;
    ssl_dhparam /home/work/path/dhparam.pem;

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers  on;
    ...

}
```

## http 强制跳转https
```
server {
    listen       80;
	server_name  subdomain.domain.com;
    rewrite /(.*) https://$http_host/$1 redirect;
}
```

[使用 acme.sh 给 Nginx 安装 Let’ s Encrypt 提供的免费 SSL 证书](https://ruby-china.org/topics/31983)