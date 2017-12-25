+++
date = "2017-05-24"
draft = false
title = "pip 安装时 UnicodeDecodeError 错误的解决方法"
slug = "pip-install-error"
categories = ["python"]
tags = ["pip", "错误",]
+++

## 错误描述

我在安装`pymongo`和`wrapt`时都遇到了同一种错误：

```
File "c:\program files\python36\lib\site-packages\pip\compat\__init__.py", line 73, in console_to_str
    return s.decode(sys.__stdout__.encoding)
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xb6 in position 34: invalid start byte
```

我的环境配置为：

- WindowsServer2012
- Python3.6.1
- pip 9.0.1
- PowerShell

## 解决

在查阅了Stackoverflow以及国内各大论坛后，发现次错误是由于PowerShell(cmd也相同)的编码格式默认为GBK(代码页936)。我的系统语言是中文，所以是GBK，其他系统语言可能默认设置不同。

使用`chcp`命令，将编码修改为UTF-8就能解决问题。

PowerShell命令(cmd中相同)：

```
chcp 65001
```
命令含义是：将代码页转换为65001(UTF-8)

此时在PowerShell顶部显示`Active code page: 65001`，这表明正在使用UTF-8编码。

再用pip安装之前失败的内容即可。

```
Active code page: 65001
PS C:\Users\Administrator> pip instal pymongo
Collecting pymongo
  Using cached pymongo-3.4.0.tar.gz
Installing collected packages: pymong
  Running setup.py install for pymong
Successfully installed pymongo-3.4.0
```
