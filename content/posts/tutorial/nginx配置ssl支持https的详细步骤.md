---
title: "nginx配置ssl支持https的详细步骤"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "nginx配置ssl支持https的详细步骤"
weight:
slug: ""
draft: false # 是否为草稿
comments: true # 本页面是否显示评论
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

nginx配置ssl支持https的详细步骤 {#content_views}
---------------------------------------------------------

> **文章概要**：在建站的时候我们通常要让网站通过https进行访问，不然使用http过程中，所有信息都是未加密的，并且用户访问的时候浏览器会屏蔽我们的网站。本文介绍如何通过nginx配置ssl以支持通过https协议访问网站。
> **本文内容来自：[谷流仓AI - ai.guliucang.com](https://ai.guliucang.com)**

### 一、修改nginx配置文件

1. 打开nginx.conf配置文件，修改需要添加支持ssl的server块，比如

```prism language-nginx
http {
	server {
        listen       443 ssl; # https默认为443端口，当然也可以用任何端口。后面ssl用于告诉Nginx在指定的端口上启用SSL/TLS加密
        server_name  example.com; # 你网站的域名
        
        # 下面输入证书和私钥的地址（详细解释看下文的注释1）
        ssl_certificate      certs/my_cert.crt; # 证书（查看注释2）
        ssl_certificate_key  certs/my_cert.key; # 证书对应的私钥文件（查看注释3）
        
        ssl_session_cache    shared:SSL:1m; # 可选配置，设置了 SSL 会话缓存的类型和大小。（具体查看注释5）
        ssl_session_timeout  5m; # 可选配置，设置了 SSL 会话缓存的超时时间为 5 分钟。

        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4; # 可选配置， 指定了 SSL/TLS 握手过程中允许使用的加密算法的优先级顺序。（具体查看注释7）
        ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3; # 可选配置，指定了允许使用的 SSL/TLS 协议版本。（具体查看注释8）
        ssl_prefer_server_ciphers  on; # 可选配置（具体查看注释9）

	}

}

```

2. 修改保存了上面的配置文件之后，使用`nginx -t`验证一下配置文件是否正确配置。
3. 提示syntax ok之后，就可以用`nginx -s reload`重载配置，使配置立即生效。

###二、验证

通过https访问上面配置好的网站，可以在浏览器地址栏左侧的锁图标点开看看是否显示证书是安全的了。

### 三、（可选）HTTP重定向到HTTPS

如果希望将所有HTTP请求重定向到HTTPS,可以添加以下配置:

```prism language-nginx
server {
    listen 80;
    server_name example.com;
    return 301 https://$server_name$request_uri;
}
```

保存修改之后，`nginx -s reload`重载配置文件即可。

### 相关注释

1. **ssl证书**：

   * 有免费的有也收费的，没钱的用免费的就行了， 比如Let's Encrypt。
   * 如何下载证书？
     * 可以去阿里云的`数字证书`页面获取，可以申请20个免费的DigitCert证书
     * Let's Encrypt的可以安装certbot自动化下载证书还可以自动续期
   * Let's Encrypt如何通过certbot下载证书？
     1. centos中通过yum安装certbot：`yum install certbot -y`

     2. 签发下载泛域名证书`*.example.com`(这样所有子域名也可以用): `certbot certonly -d "*.example.com" -d example.com --manual --preferred-challenges dns-01 --server https://acme-v02.api.letsencrypt.org/directory`, 其中`--preferred-challenges dns-01`表示通过dns的方式验证自己对网站的所有权。比如我的域名解析服务是阿里云提供的，按照certbot的要求，去阿里云的云解析界面添加一个指定的TXT类域名解析就行了。

     3. 签发成功后给出如下提示

        ```java
        		 Congratulations! Your certificate and chain have been saved at:
           /etc/letsencrypt/live/example.com/fullchain.pem
           Your key file has been saved at:
           /etc/letsencrypt/live/example.com/privkey.pem
           Your certificate will expire on 2024-06-16. To obtain a new or
           tweaked version of this certificate in the future, simply run
           certbot again. To non-interactively renew *all* of your
           certificates, run "certbot renew"
        ```

     4. 之后快要过期之前通过`certbot renew`即可签发新的证书。

   * 证书有效期？
     * 免费的证书目前一般只有3个月有效期，到期之前需要重新签发新的证书
   * 证书还没过期，直接新申请一个证书，旧的证书会立即失效吗？
     * 不会。旧证书的过期时间是固定的,不会因为申请了新证书而提前失效。除非你直接把旧的证书从服务器上删掉了。
     * 可以在在过期前提前申请好一个新的证书，新的证书不会影响旧的证书。然后过期前把新的证书放到服务器替换掉旧的证书，并`nginx -s reload`重载配置就行了。
2. **关于ssl_certificate**

   * 证书文件包含服务器的公钥、服务器信息以及证书颁发机构（CA）的数字签名。
   * 证书文件用于向客户端证明服务器的身份，并提供服务器的公钥。
   * 可以是`.pem`格式的文件，也可以是`.crt`格式的文件（详情查看注释4）
3. **关于ssl_certificate_key**

   * 私钥文件包含与证书文件中公钥相对应的私钥。
   * 私钥用于对数据进行解密和签名操作，必须严格保密。
   * 可以是`.pem`格式的文件，也可以是`.key`格式的文件
4. **pem文件和crt文件有啥区别，都能用吗？**

   * SSL证书的PEM和CRT格式都是用于存储SSL/TLS证书的文件格式，它们之间有一些区别，但在大多数情况下可以互换使用。
   * PEM（Privacy Enhanced Mail）格式是一种基于Base64编码的文件格式，以 `-----BEGIN CERTIFICATE-----` 开头，以 `-----END CERTIFICATE-----` 结尾。PEM文件可以包含证书、私钥或者公钥。PEM格式的文件可以用于存储SSL/TLS证书、私钥和公钥。
   * CRT（Certificate）格式与PEM格式类似，也是基于Base64编码，但通常只包含证书本身，不包含私钥。CRT格式的文件通常只用于存储SSL/TLS证书。
   * 如果你有一个包含证书和私钥的PEM文件，可以直接在Nginx配置中使用该文件。以Let's Encrypt签发的证书为例，ssl_certificate填`/path/to/fullchain.pem`和ssl_certificate_key填对应私钥地址`/path/to/privkey.pem`
   * 如果你有单独的CRT证书文件和私钥文件，需要在Nginx配置中分别指定证书文件和私钥文件的路径。
5. **ssl_session_cache**

   * 这个配置设置了 SSL 会话缓存的类型和大小。
   * `shared` 表示在所有工作进程之间共享缓存。
   * `SSL` 是缓存的名称。
   * `1m` 表示缓存的最大大小为 1 兆字节。
   * 启用 SSL 会话缓存可以提高性能，因为它允许客户端在后续连接中重用之前协商的会话参数，避免了重新进行完整的 SSL/TLS 握手过程。
6. **ssl_session_timeout**

   * 这个配置设置了 SSL 会话缓存的超时时间为 5 分钟。
7. **ssl_ciphers**

   * 这个配置指定了 SSL/TLS 握手过程中允许使用的加密算法的优先级顺序。
   * 它使用了一个加密算法的列表，按照优先级从高到低排列。
   * 建议使用安全性较高的加密算法，并禁用一些不安全的算法（如 NULL、aNULL、MD5、ADH、RC4 等）。
8. **ssl_protocols**

   * 指定了允许使用的 SSL/TLS 协议版本。
   * 建议只启用安全的协议版本，如 TLSv1.1、TLSv1.2 和 TLSv1.3，禁用不安全的 SSL v2 和 SSL v3。
9. **ssl_prefer_server_ciphers**

   * 这个配置指定了在 SSL/TLS 握手过程中，优先使用服务器端指定的加密算法，而不是客户端提供的加密算法。
   * 这样可以确保使用更安全的加密算法。
10. **同一个域名不同端口，可以用同一个SSL证书吗？**

    * 可以。SSL证书是基于域名颁发的，而不是基于端口号。
    * 例如，假设你有一个域名为example.com的网站，并且你在nginx.conf中配置了多个服务器块，分别监听不同的端口：

```prism language-shell
server {
    listen 443 ssl;
    server_name example.com;
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    ...
}

server {
    listen 8443 ssl;
    server_name example.com;
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    ...
}

```

在上面的示例中，两个服务器块都使用相同的SSL证书（certificate.crt）和私钥（private.key），尽管它们监听不同的端口（443和8443）。

当客户端通过不同的端口访问你的网站时，例如https://example.com或https://example.com:8443，Nginx将使用相同的SSL证书来建立安全连接，因为证书是基于域名example.com颁发的。

