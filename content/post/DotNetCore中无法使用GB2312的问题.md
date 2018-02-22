---
date : 2018-02-22
title : "DotNet Core 中使用GB2312编码报错的问题"
slug : "dotnetcore-use-gb2312"
categories : ["C#"]
tags : ["dotnetcore", "gb2312", "error",]
---

## 错误描述

### 环境
- dotnet 2.1.4

### 现象
当代码中使用
```
System.Text.Encoding.GetEncoding("GB2312")
//或者
System.Text.Encoding.GetEncoding("GBK")
```
时会抛出异常：
> Unhandled Exception: System.ArgumentException: 'GB2312' is not a supported encoding name. For information on defining a custom encoding, see the documentation for the Encoding.RegisterProvider method.

或者

> Unhandled Exception: System.ArgumentException: 'GBK' is not a supported encoding name. For information on defining a custom encoding, see the documentation for the Encoding.RegisterProvider method.

