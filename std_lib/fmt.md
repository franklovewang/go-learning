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





