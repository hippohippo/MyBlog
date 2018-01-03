+++
date = "2017-07-11"
title = "创建能够使用 ssh 登录的容器"
slug = "docker-ssh"
categories = ["docker"]
tags = ["ssh", "容器",]
+++

## 环境描述

宿主无所谓，容器我用的是CentOS，事实上用什么都差不多，毕竟ssh服务都是一样的。

## 创建容器

我们要保证将宿主某端口映射到容器22端口，以便将容器的ssh服务暴露在外，本文中我选择8022端口。

命令如下：

```
docker run -itd --name=sshcon -p 8022:22 centos /bin/bash
```

具体我就不解释了，[官方文档](https://docs.docker.com/engine/reference/run/)说的很清楚。

简述如下：

创建一个名为：sshcon的容器并映射容器的22端口至宿主的8022端口。

我选择执行bash的原因只是习惯而已，只要不结束PID为1的bash进程，那么容器就不会停止。

**请注意！** 这样的做法是不推荐的。根据docker官方的说法，应该尽量避免一个容器执行多个任务的情况。然而最终这个容器中会有一个bash进程和一个sshd进程。[Each container should have only one concern](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#each-container-should-have-only-one-concern)

## 进入容器

进入容器方法很多，我用以下命令：

```
docker exec -it sshcon bash
```

在sshcon容器中执行启动bash并连接到这个进程。如此我们便能够获得容器的shell。

此时我们会在容器中看到两个bash进程。 

```
[root@d2c41b67a917 /]# ps -aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.1  0.1  11764  1684 ?        Ss+  13:22   0:00 /bin/bash
root        13  0.5  0.1  11764  1884 ?        Ss   13:22   0:00 bash
root        25  0.0  0.1  47436  1680 ?        R+   13:22   0:00 ps -aux
[root@d2c41b67a917 /]#
```

一个是PID为1的bash进程，那个是我们启动容器的进程，此进程的结束将导致容器停止运行。  
另一个bash进程就是我们当前会话的进程，这个bash进程的结束将导致我们此次会话终止。  

## 安装sshd

首先，我们安装sshd (嗯……如果你喜欢也可以先更新一下容器内的其他软件)： 

```
yum install openssh-server -y
```

安装完成后，在容器中使用如下命令启动sshd：

```
/usr/sbin/sshd -D
```

如果执行命令后，提示无法加载主机密钥，类似如下提示：

```
error: Could not load host key: /etc/ssh/ssh_host_rsa_key
error: Could not load host key: /etc/ssh/ssh_host_dsa_key
error: Could not load host key: /etc/ssh/ssh_host_ecdsa_key
```

那么，你需要手动生成这些密钥，如下：

```
ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key 
```

执行完后，用命令重启sshd服务：

```
/usr/sbin/sshd -D
```

不过，貌似不重启也能用。

## 登录

时至此时，sshd已经安装完成。接下来就看你喜欢怎样登录了

- 可以选择新建用户，使用新用户通过ssh登录容器。
- 或者直接为root设置密码，用root登录。
- 又或者你将客户机的公钥取来放在ssh的公钥文件中。

以上方法都行，都能使你通过ssh连接宿主机的8022端口，来登录容器。

登入容器后，就可以撒开玩了~

## 最后

这样子只是为了方便和好玩，但是并不推荐。根据docker官方的说法，应该尽量避免一个容器执行多个任务的情况。[Each container should have only one concern](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#each-container-should-have-only-one-concern)



