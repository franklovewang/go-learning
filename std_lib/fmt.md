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

示例：
```
package main

import "fmt"

func main() {
	fmt.Printf("%b\n", 123.123)
	fmt.Printf("%e\n", 123.123)
	fmt.Printf("%E\n", 123.123)
	fmt.Printf("%f\n", 123.123)
	fmt.Printf("%.2f\n", 123.123)
	fmt.Printf("%F\n", 123.123)
	fmt.Printf("%g\n", 123.123)
	fmt.Printf("%G\n", 123.123)
}
```
执行结果如下：
```
8664010889386525p-46
1.231230e+02
1.231230E+02
123.123000
123.12
123.123000
123.123
123.123
```

####  1.2.5 字符串与[]byte

|占位符|说明|
|---|---|
|%s|直接输出字符串或者[]byte|
|%q|该值对应的双引号括起来的go语法字符串字面量，必要时会采用安全的转义表示|
|%x|每个字节用两字符十六进制数表示（使用a-f）|
|%X|每个字节用两字符十六进制数表示（使用A-F）|

示例：
```
package main

import "fmt"

func main() {
	s := "我是字符串"
	b := []byte{65, 66, 67}
	fmt.Printf("%s\n", s)
	fmt.Printf("%s\n", b)
	fmt.Printf("%q\n", b)
	fmt.Printf("%x\n", s)
	fmt.Printf("%X\n", s)
}
```
执行结果如下：
```
我是字符串
ABC
"ABC"
e68891e698afe5ad97e7aca6e4b8b2
E68891E698AFE5AD97E7ACA6E4B8B2
```

####  1.2.6 指针

|占位符|说明|
|---|---|
|%p|表示为十六进制，并加上前导的0x|

示例：
```
package main

import "fmt"

func main() {
	s := "我是字符串"
	fmt.Printf("%p\n", &s)
}
```
执行结果如下：
```
0xc000052050
```

####  1.2.7 宽度标识符

`宽度`通过一个紧跟在百分号后面的十进制数指定，如果未指定宽度，则表示值时除必需之外不作填充。
`精度`通过（可选的）宽度后跟点号后跟的十进制数指定。如果未指定精度，则会使用默认的精度；如果点号后没有跟数字，表示精度为0.

|占位符|说明|
|---|---|
|%f|默认宽度，默认精度|
|%10f|宽度9，默认精度|
|%.2f|默认宽度，精度2|
|%10.2f|宽度10，精度2|
|%10.f|宽度10，精度0|

示例：
```
package main

import "fmt"

func main() {
	n := 123.45
	// 默认宽度是10，默认精度是6
	fmt.Printf("%f\n", n)
	// 宽度不足，空格补齐
	fmt.Printf("%11f\n", n)
	fmt.Printf("%10s\n", "我是字符串")
	fmt.Printf("%10.4f\n", n)
}
```
执行结果如下：
```
123.450000
 123.450000
     我是字符串
  123.4500
```

####  1.2.8 其他flag

|占位符|说明|
|---|---|
|+|总是输出数值的正负号；对%q(%+q)会生成全部是ASCII字符的输出（通过转义）|
|空格|对数值，正数前面加空格而负数前面加负号；对字符串采用%x或%X时（%x或%X）会给各打印的字节之间加空格|
|-|在输出右边填充空白而不是默认的左边（即从默认的右对齐切换为左对齐）|
|#|八进制数前加0（%#o），十六进制数前加0x（%#x）或0x（%#X），指针去掉前面的0x（%#p）对%q（%3q），对%U（%#U）会输出空格和单引号括起来的go字面值|
|0|使用0而不是空格填充，对于数值类型会把填充的0放在正负号后面|

示例1：
```
package main

import "fmt"

func main() {
	s := "我是字符串"
	fmt.Printf("% d\n", 10)
	fmt.Printf("% d\n", -10)
	fmt.Printf("%s\n", s)
	fmt.Printf("%10s\n", s)
	fmt.Printf("%-10s\n", s)
	fmt.Printf("%10.2f\n", 10.14)
	fmt.Printf("%-10.2f\n", 10.14)
	fmt.Printf("%010s\n", s)
}
```
执行结果如下：
```
 10
-10
我是字符串
     我是字符串
我是字符串     
     10.14
10.14     
00000我是字符串
```

### 1.3 Fprint

将内容输出一个io.Writer接口类型的变量w中

```
func Fprint(w io.Writer, a ...any) (n int, err error) {
	p := newPrinter()
	p.doPrint(a)
	n, err = w.Write(p.buf)
	p.free()
	return
}

func Fprintf(w io.Writer, format string, a ...any) (n int, err error) {
	p := newPrinter()
	p.doPrintf(format, a)
	n, err = w.Write(p.buf)
	p.free()
	return
}

func Fprintln(w io.Writer, a ...any) (n int, err error) {
	p := newPrinter()
	p.doPrintln(a)
	n, err = w.Write(p.buf)
	p.free()
	return
}
```
n是写入的字节数量，err是返回的错误。一般用在写文件中。

示例1：向控制台输出
```
package main

import (
	"fmt"
	"os"
)

func main() {
	fmt.Fprint(os.Stdout, "向标准输出 打印字符串\n")
	fmt.Fprintln(os.Stdout, "向标准输出 打印字符串")
	fmt.Fprintf(os.Stdout, "你的名字叫：%s\n", "frank")
}
```
执行结果如下：
```
向标准输出 打印字符串
向标准输出 打印字符串
你的名字叫：frank
```

示例2：向文件输出
```
package main

import (
	"fmt"
	"os"
)

func main() {
	file, err := os.OpenFile("./test.txt", os.O_APPEND|os.O_WRONLY|os.O_CREATE, 0644)
	if err != nil {
		panic(err)
	}
	fmt.Fprintf(file, "向文件写入：\n我的名字叫：%s\n", "frank")
	file.close()
}
```

示例三：查看权限标识
```
package main

import (
	"fmt"
	"os"
)

func main() {
	s := os.FileMode(0644).String()
	fmt.Printf("s: %v\n", s)
}
```
执行结果如下：
```
s: -rw-r--r--
```

示例4：向浏览器输出
```
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.ListenAndServe(":8080", &MyHandler{})
}

type MyHandler struct {
}

func (*MyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "向浏览器输出")
}
```



### 1.4 Sprint

### 1.5 Errorf










