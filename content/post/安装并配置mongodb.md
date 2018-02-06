---
date : 2017-06-02
title : "安装 MongoDB"
slug : "install-mongodb"
categories : ["mongodb"]
tags : ["安装"]
---

## 下载
你可以从[MongoDB Download Center](https://www.mongodb.com/download-center?jmp=nav#community)页面中选择合适的版本进行下载。

我下载的是 "RHEL 7 Linux 64-bit x64" 版本，因为我的运行环境是CentOS。

## 安装
事实上Linux版本的MongoDB并不需要安装，解压即可用。但是我们还是要进行一些传统意义上的安装操作，比如：将文件夹移动到合适的目录中、建立相关的软连接、修改配置文件等等。

### 配置文件
有时候在启动时设置启动参数实在太繁琐，使用配置文件能极大地减轻启动时的繁琐操作。
从配置文件启动命令：

```
sudo mongod -f mongodb.conf
```

关于配置文件的详细内容请参考:[Configuration File Options](https://docs.mongodb.com/manual/reference/configuration-options/#configuration-file)

我挑几个我常用的举个例子

- 数据路径(dbpath)。参考：[storage Options](https://docs.mongodb.com/manual/reference/configuration-options/#storage-options)

指定一个保存数据库数据的目录

```
storage:
  dbPath: /data/mongodb/
```

- 日志。参考：[systemLog Options](https://docs.mongodb.com/manual/reference/configuration-options/#systemlog-options)

指定日志文件，以及添加日志的方式

```
systemLog:
   destination: file
   path: /log/mongodb/mongodb.log
   logAppend: true
```

- 网络。参考：[net Options](https://docs.mongodb.com/manual/reference/configuration-options/#net-options)

在服务器网络环境比较复杂的情况下，指定监听的ip地址及端口

```
net:
   port: 20000
   bindIp: 0.0.0.0
```

- 认证。参考：[security Options](https://docs.mongodb.com/manual/reference/configuration-options/#security-options)

开启权限认证(为了安全，最好开启。设置部分，在后面讲)

```
security:
   authorization: enabled
```

- 后台启动。参考：[processManagement Options](https://docs.mongodb.com/manual/reference/configuration-options/#processmanagement-options)

设置数据库为后台启动

```
processManagement:
   fork: true
```

## 启动

### 首次启动设置管理员

写好配置文件后，命令启动：

```
sudo mongod -f mongodb.conf
```

mongod 这个程序在解压后的文件夹中的`bin`文件夹中。

但是请记住如果你要开启权限认证，那第一次启动，要将配置文件中`authorization`设置为`disabled`。因为要在关闭权限认证的情况下，为MongoDB添加管理员。

在没有开启权限认证的情况下，使用

```
mongo localhost
```

连接数据库。当然`mongo`这个连接工具也在`bin`文件夹里。

然后执行

```
use admin
```

切换到`admin`数据库，这是MongoDB默认就有的一个数据库。

切换完成后，执行

```
db.createUser(user:'用户名',pwd:'密码',roles:[{role:'root',db:'admin'}])
```

其中用户名和密码，自行替换，root表示的是最高权限（啥都能干），db表示这个用户所属的数据库。关于role，你可以在[Built-In Roles](https://docs.mongodb.com/manual/reference/built-in-roles/#built-in-roles)找到更多信息。

一般我个人只用root和readWrite这两种权限，一个表示啥都能干，一个表示只能读写。

创建用户成功后，会有提示。

### 带权限认证启动

有了管理员，就可以开启权限认证了。`exit`退出MongoDB的Shell，然后kill掉MongoDB的进程，将配置文件中的`authorization`重新设置为`enabled`。再次

```
sudo mongod -f mongodb.conf
```

就行了。

### 用户认证

此时，通过`mongo localhost`登入数据库，是基本不能执行任何操作的，比如：`show dbs`。会提示权限不足。

此时可以先切换到用户所在的数据库，这是一个必要的操作，用户只有在其创建的数据库上才能正常认证。

```
use admin
```

然后进行认证

```
db.auth('用户名','密码')
```

如果认证成功，会回显一个数字"1"，否则是"0" 。

此时就能进行这个用户在创建时设定的权限内的操作了。

## 最后
数据库已经能够正常运行了。剩下的创建数据库，创建其他用户什么的，都可以用管理员用户认证后完成，或者使用一些图形化管理工具。

关于安装的部分，大约就上面这些内容吧~