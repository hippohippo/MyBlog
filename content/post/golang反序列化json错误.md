---
date : 2017-08-01
title : "Golang Json 反序列化 UTF-8 字符串遇到的错误"
slug : "golang-json-error"
categories : ["golang"]
tags : ["json", "序列化",]
---


## 过程

1. 从 http POST 数据中读取字节数据
2. 将字节数据传入 json.Unmarshal 函数直接反序列化
3. 遇到错误 `invalid character 'ï' looking for beginning of value`

其中 http POST 数据是正常的Json，使用 `fmt.Println` 能够正常输出，并没有错误中提到的神奇字符

## 原因

在UTF-8字符转中，有可能会有一个BOM（字节顺序标记）（[参见维基百科](https://en.wikipedia.org/wiki/Byte_order_mark#UTF-8)）

如果你直接输出 POST 的字节数据，会在头部发现 `239 187 191` 这三个字节，这个就是UTF-8的字节顺序标记。

但是要注意，这个字节顺序标记并不是必须的，有的 UTF-8 数据就是不带这个 BOM 的

## 解决

既然字节数据头部会有几个非数据字节，那么去掉就好了。

```
...

body = bytes.TrimPrefix(body, []byte("\xef\xbb\xbf")) // Or []byte{239, 187, 191}

...
```
参见 Stackoverflow 中的问题：[Got error "invalid character 'ï' looking for beginning of value” from json.Unmarshal](https://stackoverflow.com/questions/31398044/got-error-invalid-character-%C3%AF-looking-for-beginning-of-value-from-json-unmar)

## 最后

果然还是面向 [Stackoverflow](https://stackoverflow.com) 编程
