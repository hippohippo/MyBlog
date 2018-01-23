+++
date = "2017-05-10"
title = "在 golang 中使用 Json"
slug = "golang-json"
categories = ["golang"]
tags = ["json"]
+++

## 序列化

序列化对象将使用 [encoding/json](https://golang.org/pkg/encoding/json/) 中的 [Marshal](https://golang.org/pkg/encoding/json/#Marshal) 函数。

函数原型为：`func Marshal(v interface{}) ([]byte, error)`

以下是官网给出的例子：

```
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {
	type ColorGroup struct {
		ID     int
		Name   string
		Colors []string
	}
	group := ColorGroup{
		ID:     1,
		Name:   "Reds",
		Colors: []string{"Crimson", "Red", "Ruby", "Maroon"},
	}
	b, err := json.Marshal(group)
	if err != nil {
		fmt.Println("error:", err)
	}
	os.Stdout.Write(b)
}
```

需要注意的是：json.Marshal返回了[]byte类型，通常情况下，需要将其转换为string类型使用。

## 反序列化

反序列化对象将使用 encoding/json 中的 Unmarshal 函数。

函数原型为：`func Unmarshal(data []byte, v interface{}) error`

以下是官网给出的例子：

```
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	var jsonBlob = []byte(`[
		{"Name": "Platypus", "Order": "Monotremata"},
		{"Name": "Quoll",    "Order": "Dasyuromorphia"}
	]`)
	type Animal struct {
		Name  string
		Order string
	}
	var animals []Animal
	err := json.Unmarshal(jsonBlob, &animals)
	if err != nil {
		fmt.Println("error:", err)
	}
	fmt.Printf("%+v", animals)
}
```

在反序列化时，有可能不知道Json数据的具体类型，那应该怎么办呢？

其实，观察json.Unmarshal的函数原型可以看到，其第二个参数是一个interface。

事实上，你可以传入一个interface对象的指针，反序列化函数依然会填充这个对象。并且这个对象可以被转换成为`map[string]interface{}`这样的类型或者`[]interface{}`这样的类型，这取决于Json的内容。

于是，我们便有了在不知道Json数据具体类型的情况下，解析Json内容的方法。

代码如下：

```
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	var jsonBlob = []byte(`[
		{"Name": "Platypus", "Order": "Monotremata"},
		{"Name": "Quoll",    "Order": "Dasyuromorphia"}
	]`)

	var jsonObject interface{}
	err := json.Unmarshal(jsonBlob, &jsonObject)
	if err != nil {
		fmt.Println("error:", err)
	}
	animals := jsonObject.([]interface{})
	for i := 0; i < len(animals); i++ {
		animalObject := animals[i]
		animalMap := animalObject.(map[string]interface{})
		fmt.Println(animalMap["Name"])
		fmt.Println(animalMap["Order"])
	}
}
```

如上，我们可以将反序列化出的对象，递归地进行类型转换，转换为`[]interface{}`或者`map[string]interface{}`，这样就能一层层地解析Json的内容了。

当然，以上只是举例，证明可以通过一些手段解析未知结构的Json。为了正确解析未知结构的Json内容，你可能需要为解析过程添加一些判断，如解析出的对象的类型需要二选一，又如解析出的map类型中键的存在性等等。