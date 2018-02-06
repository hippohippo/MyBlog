---
date : 2018-01-23
title : "sudo 找不到命令 go"
slug : "sudo-cannot-find-commond-go"
categories : ["Linux"]
tags : ["golang", "env"]
---

## 错误描述

### 环境

- CentOS7.1 x64
- golang 1.9.3

golang目录：`/home/moonlightwatch/go/`

环境变量配置：
```
# /etc/profile
export GOROOT=/home/moonlightwatch/go
export GOPATH=/home/moonlightwatch/codes/golang/godefault:/home/moonlightwatch/codes/golang/workspace
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

### 现象

1. 使用非root用户，执行 `go version` 获得正确输出。
2. 使用root用户，执行 `go verison` 获得正确输出。
3. 使用非root用户，执行 `sudo go version` 获得输出 **`sudo: go: command not found`**

日了狗了有木有？

## 解决

### 原因

根据 [Some programs not found when used with sudo](https://askubuntu.com/questions/118263/some-programs-not-found-when-used-with-sudo) 中的回答。

我们使用 `sudo` 时，使用的配置文件是 `/etc/sudoers`。

### 解决

配置 `/etc/sudoers` 文件中的 `Defaults secure_path` 这一项。将 `$GOROOT/bin` 目录加入进去。（请使用绝对目录，如：/home/moonlightwatch/go/bin/）

`Defaults secure_path` 这一行，在文件中是这样子的：
```
Defaults    secure_path = /home/moonlightwatch/go/bin:/sbin:/bin:/usr/sbin:/usr/bin
```

### 其他问题

当我们使用了自己 `GOPATH` 中的包时，`sudo go run xxxx` 并不会在我们环境变量指定的目录去查找包。

我们需要设置 `/etc/sudoers` 文件中的 `Defaults    env_keep` 项。向其中加入我们的 `GOPATH` 环境变量。

`Defaults    env_keep` 这几行，在文件中是这样的：
```
Defaults    env_keep =  "COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS"
Defaults    env_keep += "MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE"
Defaults    env_keep += "LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES"
Defaults    env_keep += "LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE"
Defaults    env_keep += "LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY"
Defaults    env_keep += "GOPATH"
```
加上最后的那一行就行。

