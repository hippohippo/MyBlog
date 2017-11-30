+++
date = "2017-07-15"
draft = false
title = "golang 的时间格式化操作"
slug = "golang-time-format"
categories = ["golang"]
tags = ["时间", "格式化",]
+++

## 怎么做

### 简而言之

```
time.Now().Format("2006-01-02 15:04:05")
```

你将会获得如同 `yyyy-MM-dd hh-mm-ss` 这样的输出。

### 还可以

在 [format.go](https://golang.org/src/time/format.go) 的源码中我们可以找到一些预定的格式，源码摘抄如下：

```
const (
  	ANSIC       = "Mon Jan _2 15:04:05 2006"
  	UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
  	RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
  	RFC822      = "02 Jan 06 15:04 MST"
  	RFC822Z     = "02 Jan 06 15:04 -0700" // RFC822 with numeric zone
  	RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
  	RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
  	RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // RFC1123 with numeric zone
  	RFC3339     = "2006-01-02T15:04:05Z07:00"
  	RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
  	Kitchen     = "3:04PM"
  	// Handy time stamps.
  	Stamp      = "Jan _2 15:04:05"
  	StampMilli = "Jan _2 15:04:05.000"
  	StampMicro = "Jan _2 15:04:05.000000"
  	StampNano  = "Jan _2 15:04:05.000000000"
)
```

我们可以直接使用 `time.Time` 对象的 `format` 函数，将以上预定的格式当作参数，来得到格式化后的时间字符串。

除了预定义的格式，我们也可以按照上述格式的规则自定义格式化字符串，就如同上一节 “简而言之” 中描述的那样。

## 为什么

golang的时间格式化并不像C#那样使用 `yyyy` 表示年份、`MM` 表示月份等等。

而是使用 `2006-01-02 15:04:05` 这样具有固定数字的格式化字符串。

你要问我为什么使用这样看起来奇怪的格式化字符串？抱歉，我真的猜不透。

下面的代码摘自golang源代码 [关于时间格式化的部分](https://golang.org/src/time/format.go)

```  
// String returns the time formatted using the format string
//	"2006-01-02 15:04:05.999999999 -0700 MST"
func (t Time) String() string {
  	return t.Format("2006-01-02 15:04:05.999999999 -0700 MST")
}
```

可以看到，golang中并没有使用英文单词首字母表示的格式化字符串，而是使用了一组固定的数据。

在 [format.go](https://golang.org/src/time/format.go) 中第140行左右的地方，可以找到下面这个函数。

此函数用来判断格式化字符串中的格式化内容。在其源代码中，我们可以清晰地看到， golang 使用了硬编码来对格式化字符串进行判断。

```
// nextStdChunk finds the first occurrence of a std string in
// layout and returns the text before, the std string, and the text after.
func nextStdChunk(layout string) (prefix string, std int, suffix string) {
...
        case '2': // 2006, 2
  			if len(layout) >= i+4 && layout[i:i+4] == "2006" {
  				return layout[0:i], stdLongYear, layout[i+4:]
  			}
  			return layout[0:i], stdDay, layout[i+1:]
  
  		case '_': // _2, _2006
  			if len(layout) >= i+2 && layout[i+1] == '2' {
  				//_2006 is really a literal _, followed by stdLongYear
  				if len(layout) >= i+5 && layout[i+1:i+5] == "2006" {
  					return layout[0 : i+1], stdLongYear, layout[i+5:]
  				}
  				return layout[0:i], stdUnderDay, layout[i+2:]
  			}

...
}
```
(由于该函数行数比较多，所以只摘抄部分，如果有兴趣研究，请移步 [format.go 源代码](https://golang.org/src/time/format.go) )

## 最后

我暂时不知道 golang 为何使用这样的格式化字符串，但是既然这样设计了，我们就应该如此使用。
