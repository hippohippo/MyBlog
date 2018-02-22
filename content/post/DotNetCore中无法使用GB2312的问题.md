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
会抛出异常：

> Unhandled Exception: System.ArgumentException: 'GB2312' is not a supported encoding name. For information on defining a custom encoding, see the documentation for the Encoding.RegisterProvider method.

或者

> Unhandled Exception: System.ArgumentException: 'GBK' is not a supported encoding name. For information on defining a custom encoding, see the documentation for the Encoding.RegisterProvider method.

## 解决

### 原因

使用如下代码检查支持的编码：

```
System.Text.Encoding.GetEncodings();
```

发现获得的编码中没有GB2312或者GBK。

### 解决办法

**第一步**

向项目中添加如下包：

> System.Text.Encoding.CodePages

根据 [System.Text.Encoding.CodePages nuget主页](https://www.nuget.org/packages/System.Text.Encoding.CodePages/) 的描述，这个包能为程序提供 Windows-1252, Shift-JIS, and GB2312 三种编码。

> Provides support for code-page based encodings, including Windows-1252, Shift-JIS, and GB2312.

所以导入这个包我们将能使用 GB2312 编码。

在 `.csproj` 文件中应添加如下代码：

```
  <ItemGroup>
    <PackageReference Include="System.Text.Encoding.CodePages" Version="4.4.0" />
  </ItemGroup>
```

或者在项目目录执行如下命令：

```
dotnet add package System.Text.Encoding.CodePages --version 4.4.0
```

当然，其中的版本号请自行修改为最新。此时（2018.02.22）最新版是4.4.0 。

别忘了执行 `dotnet restore` 。

**第二步**

根据错误提示，我们需要对引用的编码使用 `Encoding.RegisterProvider` 函数进行注册。

在使用 `System.Text.Encoding.GetEncoding ("GB2312")` 之前，在代码中执行：

```
System.Text.Encoding.RegisterProvider (System.Text.CodePagesEncodingProvider.Instance);
```

注册完之后，获取 GB2312 编码对象就不会报错了，并且可以正常使用其中的函数。

### 其他问题

至此我们解决了关于 GB2312 编码的问题。但是程序中仍然无法使用 GBK 编码。针对 GBK 编码数据的解析问题仍存在。
