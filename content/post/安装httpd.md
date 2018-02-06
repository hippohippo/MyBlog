---
date : 2017-07-12
title : "安装ApacheWeb服务器 —— httpd"
slug : "install-httpd"
categories : ["Apache"]
tags : ["httpd"]
---

## 环境

CentOS 7.x

(嗯……好像就只有这些了)

## 安装

本着能通过 `yum` 绝不编译的心理，我使用如下命令安装：

```
yum install httpd -y
```

嗯……就好了……

## 启动

正常启动(全默认配置)：

```
systemctl start httpd
```

docker 容器中启动：

```
httpd
```
(就是只写服务名称就行……)

### 解决问题

你有可能会遇到这样的**错误**：

```
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.18.0.2. Set the 'ServerName' directive globally to suppress this message
```

服务器表示他不知道应该如何确定服务的完全限定域名，要你设置 `ServerName` 来阻止此消息。

也就是说，去配置文件里把 `ServerName` 设置一下就行。

打开 "/etc/httpd/conf/httpd.conf" 看看

```
vim /etc/httpd/conf/httpd.conf
```

**请注意**你要是用root以外的用户进行修改操作，则需要在命令前加 `sudo` ，毕竟 `etc` 下的文件不是你想改就能改的~

你会在配置文件中找到如下一段内容：

```
# ServerName gives the name and port that the server uses to identify itself.
# This can often be determined automatically, but we recommend you specify
# it explicitly to prevent problems during startup.
#
# If your host doesn't have a registered DNS name, enter its IP address here.
#
#ServerName www.example.com:80
```

上面的说明已经很清楚了。

接下来把 `ServerName` 那一行解除封印(取消注释，把行首的 "#" 删掉)。

将后面的值修改成 [域名:端口] 或者 [IP:端口] 的样子

如果只希望本地访问，那么：

```
ServerName 127.0.0.1:80
```

如果希望不区分域名和IP，那么以下配置表示接受任意连接：

```
ServerName 0.0.0.0:80
```

此时重启 httpd ，你就能在浏览器访问服务器了。

## 最后

httpd 默认会显示apache的测试页面。你只需在 `/var/www/html` 路径下放上你的网页(需要有index.html文件作为默认页面)就行了。当然，这个路径是可以从配置文件配置的，我觉得用默认的就行，除非一些特殊情况(比如，分区容量限制等)。

这样程度的Web服务器只能提供纯静态网站的内容，如果需要支持动态内容(如：PHP什么的)，那么还需要安装相应的软件和服务。此处不再过多介绍。
