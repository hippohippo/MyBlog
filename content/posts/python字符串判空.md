---
date : 2018-01-15
title : "Python中的字符串判空"
categories : ["python"]
tags : ["string"]
---

## Python中的字符串判空

### 操作

```
somestring = ''
if somestring:
    print('不是空的')
else:
    print('字符串是空的')
```
在python中可以支持这样的字符串判断操作，可以直接判断字符串是否是空的。大约相当于
```
somestring = ''
if len(somestring) > 0:
    print('不是空的')
else:
    print('字符串是空的')
```

事实上上述两种方法都能达到效果，但是按照规范来说，应该使用第一种方法，具体规范如下：

> For sequences, (strings, lists, tuples), use the fact that empty sequences are false.    
> Yes: if not seq:  
>     if seq:  
> No: if len(seq):  
>     if not len(seq):


摘自：[PEP 8 -- Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/)


### 比较

在C#里，字符串判空是这样
```
var somestring = "";
if(System.String.IsNullOrEmpty(somestring))
{
    System.Console.WriteLine("字符串是空的");
}
else
{
    System.Console.WriteLine("字符串不是空的");
}
```

明显，Python代码更简洁，但是C#代码更具可读性。

实话说，写C#要是没了VS或者代码补全，写起来真是费劲……
