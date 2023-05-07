# fmt标准库

主要分为向外输出内容和获取输入内容两大部分。

## 1.输出

### 1.1 fmt.Print

print有三个相关的函数：
```
func Print(a ...any) (n int, err error) {
  return Fprint(os.Stdout, a...)
}

func Println(a ...any) (n int, err error) {
  return Fprintln(os.Stdout, a...)
}

func Printf(format string, a ...any) (n int, err error) {
  return Fprintf(os.Stdout, format, a...)
}
```
可以看到，最终调用的都是Fprintf，os.Stdout代表标准输出，即控制台输出。

### 1.2 格式化占位符

####  1.2.1 通用占位符

|占位符|说明|
|---|---|
|%v|值的默认格式表示|
|%+v|类似%v，但输出结构体时会添加字段名|
|%#v|值的Go语法表示|
|%T|打印值的类型|
|%%|百分号|

示例1：
```
package main

import "fmt"

type User struct {
	Id int64
}

func main() {
	user := &User{Id: 1}
	fmt.Printf("%v\n", user)
	fmt.Printf("%+v\n", user)
	fmt.Printf("%#v\n", user)
	fmt.Printf("%T\n", user)
}
```
执行结果如下：
```
&{1}
&{Id:1}
&main.User{Id:1}
*main.User
```
示例2：
```
package main

import "fmt"

func main() {
	fmt.Printf("%v\n", "123")
	fmt.Printf("%+v\n", "123")
	fmt.Printf("%#v\n", "123")
	fmt.Printf("%T\n", "123")
}
```
执行结果如下：
```
123
123
"123"
string
```
####  1.2.2 布尔型

|占位符|说明|
|---|---|
|%t|true或false|

示例1：
```
package main

import "fmt"

func main() {
	fmt.Printf("%t\n", true)
	fmt.Printf("%t\n", false)
}
```
执行结果如下：
```
true
false
```
####  1.2.3 整型

|占位符|说明|
|---|---|
|%b|表示为二进制|
|%c|该值对应的unicode码值|
|%d|表示为十进制|
|%o|表示为八进制|
|%x|表示为十六进制，使用a-f|
|%X|表示十六进制，使用A-F|
|%U|表示Unicode格式：U+1234，等价于“U+%04X”|
|%q|该值对应的单引号括起来的go语法字符字面值，必要时采用安全的转义表示|

示例1：
```
package main

import "fmt"

func main() {
	fmt.Printf("%b\n", 123)
	fmt.Printf("%c\n", 127)
	fmt.Printf("%d\n", 123)
	fmt.Printf("%o\n", 123)
	fmt.Printf("%x\n", 123)
	fmt.Printf("%X\n", 123)
	fmt.Printf("%U\n", 33)
	fmt.Printf("%q\n", 40)
}
```
执行结果如下：
```
1111011
DEL
123
173
7b
7B
U+0021
'('
```

####  1.2.4 浮点数与复数

|占位符|说明|
|---|---|
|%b|无小数部分、二进制指数的科学计数法，如-123456p-78|
|%e|科学计数法，如-123455e+78|
|%E|科学计数法，如-123455E+78|
|%f|有小数部分但无指数部分，如123.456|
|%F|等价于%f|
|%g|根据实际情况采用%e或%f格式（以获得更简洁、准确的输出）|
|%G|根据实际情况采用%E或%F格式（以获得更简洁、准确的输出）|











