---
title: 阿里云CentOS内网机器利用iptables共享公网IP上网
date: 2018-04-17 11:32:17
categories: linux
tags: centos
---
公司有个业务是2B的以及日活不大，所以两台服务器搞定,一个6M EIP。两台机器都是CentOS7系统
EIP为 xxx.xxx.xxx.xxx绑在 内网ip为 172.18.30.175的服务器上，内网机器的内网ip为 172.18.30.176。
现在有个问题，更新软件的时候可以将EIP解绑然后重绑在内网机器上。但是每次都这样搞太麻烦了。于是想到了iptables桥接上网。

搜了下，来源于网络。不知道出处。所以不署名了。

### 在带外网的机器上设置iptables规则：
   `iptables -t nat -A POSTROUTING -s 172.18.30.176 -j SNAT --to 172.18.30.175`
    // 如果想让整个内网的机器全部上网，只需要把 -s 172.18.30.176 换成
    `-s 172.18.30.0/255.255.255.0` 即可

### 在带外网机器上打开转发
   首先查看是否已经打开
   `sysctl -a |grep 'net.ipv4.ip_forward'`
如果值为1，则说明已经打开，否则需要修改配置文件 /etc/sysctl.conf
打开该配置文件，找到该参数，使其变为
`net.ipv4.ip_forward = 1`
然后运行 `sysctl -p`

### 在内网机器上，设置其网关为 172.18.30.175
`vim  /etc/sysconfig/network-scripts/ifcfg-eth0`
添加
`GATEWAY=172.18.30.175`
重启网络服务即可。 `service network restart`

### 测试内网机器是否可以上网。
