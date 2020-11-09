---
title: "Ubuntu下配置Nginx https"
subtitle: ""
date: 2019-11-09T11:40:25+08:00
lastmod: 2019-11-09T11:40:25+08:00
draft: true
author: "Liusoon"
authorLink: "liusoonfang.space"
description: "井字型少年"

tags: [Nginx,https,ubuntu]
categories: [学个语言]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc: true
  
math:
  enable: false
lightgallery: true
license: ""
---

<!--more-->
### 什么是https

&emsp;HTTPS(全称：Hypertext Transfer Protocol over Secure Socket Layer)，是以安全为目标的HTTP通道，简单讲是HTTP的安全版。即HTTP下加入SSL层，HTTPS的安全基础是SSL。它是一个URI scheme(抽象标识符体系)，句法类同http:体系。用于安全的HTTP数据传输。https:URL表明它使用了HTTP，但HTTPS存在不同于HTTP的默认端口及一个加密/身份验证层(在HTTP与TCP之间)。这个系统的最初研发由网景公司进行，提供了身份验证与加密通讯方法，现在它被广泛用于万维网上安全敏感的通讯，例如交易支付方面。
### 确认Nginx支持OpenSSL模块

&emsp;运行下面命令进行判断：

```shell
nginx -V
nginx: nginx version: nginx/1.0.0
nginx: built by gcc 4.4.3 (Ubuntu 4.4.3-4ubuntu5) 
nginx: TLS SNI support enabled
nginx: configure arguments: --user=www --group=www --prefix=/usr/local/webserver/nginx --with-http_stub_status_module --with-http_ssl_module
```

&emsp;如果没有发现–with-http_ssl_module这个编译参数，说明不支持。Nginx默认是不支持SSL的，需要加入–with-http_ssl_module参数重新编译。

### 生成证书

&emsp;SSL证书是数字证书的一种，类似于驾驶证、护照和营业执照的电子副本。SSL证书通过在客户端浏览器和Web服务器之间建立一条SSL安全通道(Secure socket layer(SSL)安全协议是由Netscape Communication公司设计开发。该安全协议主要用来提供对用户和服务器的认证；对传送的数据进行加密和隐藏；确保数据在传送中不被改变，即数据的完整性，现已成为该领域中全球化的标准。由于SSL技术已建立到所有主要的浏览器和WEB服务器程序中，因此，仅需安装服务器证书就可以激活该功能了)。即通过它可以激活SSL协议，实现数据信息在客户端和服务器之间的加密传输，可以防止数据信息的泄露。保证了双方传递信息的安全性，而且用户可以通过服务器证书验证他所访问的网站是否是真实可靠。

#### 自签证书

&emsp;具体生成方法参见：[Ubuntu下配置Apache HTTPS]中的生成方法。

#### CA机构签发证书

&emsp;CA机构签发证书可以分为付费和免费，付费的不用多说阿里云腾讯云花钱一键搞定。申请免费的StartSSL CA证书可以通过StartSSL(公司名：StartCom也是一家CA机构，它的根证书很久之前就被一些具有开源背景的浏览器支持(Firefox浏览器、谷歌Chrome浏览器、苹果Safari浏览器等)。

&emsp;申请地址：[http://www.startssl.com](http://www.startssl.com/)

&emsp;具体生成的方法不多赘述，可以参照一下两篇文章：

> -[http://blog.s135.com/startssl/](http://blog.s135.com/startssl/)

> -[http://www.deepvps.com/apply-startssl-ssl-certificate.html](http://www.deepvps.com/apply-startssl-ssl-certificate.html)

### 配置Nginx  

&emsp;修改nginx.conf中相应的server段，加入如下内容：

```shell
listen 443;
server_name www.mike.me;
#启用SSL模块
ssl on;
#证书文件放置路径
ssl_certificate /etc/ssl/private/yiji.cer;
#私钥文件放置路径
ssl_certificate_key /etc/ssl/private/yiji.key;
```

{{< admonition >}}
      **SSL数字证书路径和域名根据实际情况修改** 
      {{< /admonition >}}

### 重启Nginx并验证

```shell
#测试Nginx主配置文件是否正确
nginx -t
#平滑重启Nginx
nginx -s reload
```

#### 使用HTTPS协议访问网站

&emsp;访问https://ip或域名，浏览器会弹出安装服务器证明书的窗口,说明服务器已经支持SSL了。

