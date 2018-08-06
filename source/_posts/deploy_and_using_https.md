---
title: CentOS升级HTTPS
date: 2017-12-28 21:38:29
categories: Nginx
tags: 
    - https
    - nginx
    - web
---
## 升级https
最近需要将域名升级为https, 以满足安全性和一些第三方接口的要求。
目前的技术栈为前端VUE, 后端为Tornado。Tornado负责接口的实现。
## 具体步骤

   `curl https://get.acme.sh | sh` 安装acme.sh

   `source ~/.bashrc `
   `acme.sh --issue -d www.your-app.com -w /home/work/www/your-app/current/public` 这里的public需要通过域名外网可访问

   ```
   acme.sh --installcert -d www.your-app.com \
               --keypath       /home/work/www/ssl/www.your-app.com.key  \
               --fullchainpath /home/work/www/ssl/www.your-app.com.key.pem \
               --reloadcmd     "sudo service nginx force-reload"
   ```
   这里是ubuntu上的用法,而我用的服务器是CentOS系统的,所以最后一句需要改为
   ```
   --reloadcmd "sudo systemctl reload nginx"
   ```
   最后一句为了免密执行需要再设置下:
   `sudo visudo`
   `work ALL=(ALL) NOPASSWD: /bin/systemctl reload nginx` # work 是工作目录
   生成 dhparam.pem 文件:
   `openssl dhparam -out /home/work/www/ssl/dhparam.pem 2048`

## 遇到的问题
首先需要解决的是证书, 关于这点acme.sh解决了这个问题, 详细内容可以看参考。
当生成证书, 写好nginx配置的时候, 测试配置的时候直接报错`unknown directive "ssl"`。
很明显ssl不支持, 查看配置确实是最初编译的时候没加入http_ssl_module,
随后重新编译加上`--with-http_ssl_module`, 于是就解决了这个问题。

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

## 最后检查并重启nginx
这个具体的命令依据不同版本的系统而不同, 更多内容看参考文章。

[使用 acme.sh 给 Nginx 安装 Let’ s Encrypt 提供的免费 SSL 证书](https://ruby-china.org/topics/31983)
