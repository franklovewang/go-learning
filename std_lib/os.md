# os标准库

> os包提供了操作系统函数，但和操作系统无关。
> 
> os包的接口规定在所有操作系统中都是一致的。
> 
> 设计为Unix风格的。

## 1 权限说明
os标准库有大量的文件操作，在创建文件等操作中，需要指定perm.

在go语言中perm是一个uint32类型

在go语言中一般使用0777这样的形式进行表示

- 0代表八进制
- 777是-rwxrwxrwx的形式计算得出

第一位：

-：代表这是一个普通文件（regular）

d: 目录文件（directory）

l: 链接文件（link）

b: 块设备文件（block）

c: 字符设备文件（character）

s: 套接字文件（socket）

p: 管道文件（pipe）

> go中的FileMode定义

```
type FileMode uint32
```
FileMode代表文件的模式和权限位。这些字位在所有操作系统都有相同的含义，因此文件的信息可以在不同的操作系统之间安全的移植。不是所有的位都能用于所有的系统，唯一共有的是用于表示目录的ModeDir位。

```
const (
  // 单字符是被String方法用于格式化的属性缩写
  ModeDir        // d: 目录
  ModeAppend     // a: 只能写入，且只能写入到末尾
  ModeExclusive  // l: 用于执行
  ModeTemporary  // T: 临时文件（非备份文件）
  ModeSymlink    // L: 符号文件（不是快捷方式文件）
  ModeDevice     // D: 设备
  MOdeNamedPipe  // p: 命名管道（FIFO）
  ModeSocket     // S: Unix域socket
  MOdeSetuid     // u: 表示文件具有其创建者用户id权限
  ModeSetgid     // g: 表示文件具有其创建组id的权限
  ModeCharDevice // c: 字符设备，需已设置ModeDevice
  // 覆盖所有类型位（用于通过&获取类型位），对普通文件，所有这些位都不应该被设置
  ModeType = ModeDir | ModeSymlink | ModeNamedPipe | ModeSocket | ModeDevice
  ModePerm FileMode = 0777 // 覆盖所有Unix权限位（用于通过&获取类型位）
)
```
这些被定义的位是FileMode最重要的位。另外9个不重要的位为标准Unix rwxrwxrwx权限（任何人都可以读、写、运行）。这些（重要）位的值应被视为公共API的一部分，可能会用于线路协议或硬盘标识：它们不能被修改，但可以添加新的位。

## 2 os.Create

```
func Create(name string) (file *File, err error)
```
Create采用模式0666（任何人都可以读写，不可执行）创建一个名为name的文件，如果文件已存在会截断它（为空文件）。如果成功，返回的文件对象可用于I/O；对应的文件描述符具有O_RDWR模式。如果出错，错误底层类型是\*PathError

```
package main

import (
	"os"
)

func main() {
	// 创建文件
	f, err := os.Create("test.txt")
	if err != nil {
		panic(err)
	}
	defer f.Close()
}
```

## 3 os.Mkdir

创建单个目录
```
func Mkdir(name string, perm FileMode) error {

}
```

示例：
```
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Mkdir("./std/ms", os.ModePerm)
	if err != nil {
		fmt.Printf("err: %v\n", err)
	}
}
```

## 4 os.MkdirAll

创建多级目录

```
func MkdirAll(path string, perm FileMode) error {}
```

示例：
```
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.MkdirAll("./std/ms/a/b", os.ModePerm)
	if err != nil {
		fmt.Printf("err: %v\n", err)
	}
}
```

## 5 os.Remove

删除一个空的目录或一个文件
```
func Remove(name string) error {}
```

示例：
```
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Remove("./std/ms/a/b")
	if err != nil {
		fmt.Printf("err: %v\n", err)
	}
}
```

## 6 os.RemoveAll

强制删除多级目录以及目录中的文件

```
func RemoveAll(path string) error {}
```

示例：
```
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.RemoveAll("./std/ms/")
	if err != nil {
		fmt.Printf("err: %v\n", err)
	}
}
```

## 7 os.Getwd

获取工作目录

```
func Getwd() (dir string, err error) {}
```

```
package main

import (
	"fmt"
	"os"
)

func main() {
	dir, err := os.Getwd()
	if err != nil {
		fmt.Printf("err: %v\n", err)
	} else {
		fmt.Printf("dir: %v\n", dir)
	}
}	
```
执行结果如下：
```
dir: d:\code\go\gin_study
```

## 8 os.Chdir

修改工作目录

```
// 改变工作目录到f，其中f必须为一个目录，否则便会报错
func Chdir(dir string) error {}
```

示例：
```
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Chdir("d:/code/go/gin_study/std")
	if err != nil {
		fmt.Printf("err: %v\n", err)
	}
	fmt.Println(os.Getwd())
}
```
执行结果如下：
```
d:\code\go\gin_study\std <nil>
```

## 9 os.TempDir

获得临时目录

```
func TempDir() string {}
```
示例：
```
package main

import (
	"fmt"
	"os"
)

func main() {
	s := os.TempDir()
	fmt.Printf("s: %v\n", s)
}
```
执行结果如下：
```
s: C:\Users\CHENMI~1\AppData\Local\Temp
```

## 10 os.Rename

重命名文件
```
func Rename(oldpath, newpath string) error {
	return rename(oldpath, newpath)
}
```
示例：
```
package main

import (
	"fmt"
	"os"
)

func main() {
	f, err := os.Create("./std/old_boy.txt")
	if err != nil {
		fmt.Printf("err: %v\n", err)
	}
	f.Close()
	err2 := os.Rename("./std/old_boy.txt", "./std/new_bod.txt")
	if err2 != nil {
		fmt.Printf("err2: %v\n", err2)
	}
}
```

## 11 os.Chmod

修改文件权限

```
func Chmod(name string, mode FileMode) error {
	return chmod(name, mode)
}
```

示例：
```

```











