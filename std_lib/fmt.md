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

示例
```
func TestPrint(t *testing.T) {
	fmt.Print("我是控制台输出，不带换行\n")
	fmt.Println("我是控制台输出，带换行")
	fmt.Printf("我是格式化输出，%s 占位符\n", "张三")
}
```

### 1.2 格式化占位符

####  1.2.1 通用占位符

|占位符|说明|
|--|--|
|%v|值的默认格式表示|

