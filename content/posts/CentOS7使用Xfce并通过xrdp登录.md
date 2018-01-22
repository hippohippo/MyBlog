+++
date = "2018-01-22"
title = "CentOS 使用 Xfce 桌面并通过 xrdp 登录"
slug = "golang-json-error"
categories = ["CentOS"]
tags = ["Xfce", "xrdp",]
+++

### 基础环境
- CentOS 7.1 最小化安装

### 安装步骤

#### 安装桌面支持
首先安装桌面支持
```
yum groupinstall "Server with GUI"
```
此时，系统默认使用Gnome桌面。

#### 安装xrdp和vnc
```
yum install xrdp tigervnc
```
然后启动xrdp并设为开机启动
```
systemctl start xrdp
systemctl enable xrdp
```

#### 配置防火墙
```
firewall-cmd --add-port=3389/tcp --permanent
firewall-cmd --reload
```
或者干掉防火墙
此时，已经可以通过远程桌面连接了，但是使用的是默认的Gnome桌面。

#### 安装Xfce
```
yum groupinstall Xfce
```

#### 配置Xfce为默认桌面
**以下内容参考[XRDP and xfce](https://www.centos.org/forums/viewtopic.php?t=51046)**

打开用户目录下的.Xclients文件，原本没有的话创建一个
```
vim ~/.Xclients
```
然后把下面内容写入文件：
```
#!/bin/bash
XFCE="$(which xfce4-session 2>/dev/null)"
exec "$XFCE"
```
增加执行权限
```
chmod +x ~/.Xclients
```
并重启xrdp
```
systemctl restart xrdp
```