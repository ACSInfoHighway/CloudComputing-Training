# Chapter 2

## Shell Commands

### Man cd

cd cannot directly manned because it is a shell built-in command, but with

```bash
man builtins
```

We can get cd manual entry:

```
       cd [-L|[-P [-e]] [-@]] [dir]
              Change the current directory to dir.  if dir is not supplied, the value of the HOME shell variable is the default.  Any additional arguments following dir are ignored.  The variable CDPATH defines the search path for the  directory
              containing dir: each directory name in CDPATH is searched for dir.  Alternative directory names in CDPATH are separated by a colon (:).  A null directory name in CDPATH is the same as the current directory, i.e., ``.''.  If dir be‐
              gins with a slash (/), then CDPATH is not used.  The -P option causes cd to use the physical directory structure by resolving symbolic links while traversing dir and before processing instances of .. in dir (see also the -P  option
              to  the  set  builtin  command); the -L option forces symbolic links to be followed by resolving the link after processing instances of .. in dir.  If .. appears in dir, it is processed by removing the immediately previous pathname
              component from dir, back to a slash or the beginning of dir.  If the -e option is supplied with -P, and the current working directory cannot be successfully determined after a successful directory change, cd will return  an  unsuc‐
              cessful  status.  On systems that support it, the -@ option presents the extended attributes associated with a file as a directory.  An argument of - is converted to $OLDPWD before the directory change is attempted.  If a non-empty
              directory name from CDPATH is used, or if - is the first argument, and the directory change is successful, the absolute pathname of the new working directory is written to the standard output.  The return value is true if  the  di‐
              rectory was successfully changed; false otherwise.
```

究其根本原因，`当前目录`对于每个进程都是独立的属性，如果cd作为被shell spawn的进程，则必然无法改变shell本身的路径

### WC

`wc`(word count)

```
NAME
       wc - print newline, word, and byte counts for each file

SYNOPSIS
       wc [OPTION]... [FILE]...

DESCRIPTION
       Print newline, word, and byte counts for each FILE, and a total line if more than one FILE is specified.  A word is a non-zero-length sequence of characters delimited by white space.

       With no FILE, or when FILE is -, read standard input.

       The options below may be used to select which counts are printed, always in the following order: newline, word, character, byte, maximum line length.

       -c, --bytes
              print the byte counts

       -m, --chars
              print the character counts

       -l, --lines
              print the newline counts

       --files0-from=F
              read input from the files specified by NUL-terminated names in file F; If F is - then read names from standard input

       -L, --max-line-length
              print the maximum display width

       -w, --words
              print the word counts
```

## Git

1. `git cherry-pick`

该操作可以使任意分支上的任意多commits应用到当前分支

当代码冲突时，有以下措施：

- `continue`:用户合并冲突后继续
- `abort`:退回到应用前
- `quit`:合并冲突后不再继续

2. `git rebase`

使用较少但是比较重要

由rebase本意得到的根本作用：变基

当master分支被更新，`git rebase`将从原始commit分支出的feature分支commit先被取消，然后应用到master分支更新后的commit上，feature分支可看作由后commit引发得到的

若变基应用于同一分支，则可以使多个commit合并

3. 对于已经同步到远程的commits，不可能使用本地rebase来合并远程commit或修改顺序

## Go

go env中`GOARCH`表示目标处理器架构，`GOBIN`表示编译器和链接器的安装位置，`GOOS`表示目标操作系统，`GOPATH`表示当前工作目录，`GOROOT`表示Go开发包的安装目录。

### 数据类型

`int`为64位，可指定数据宽度
`byte` = `uint8`，宽度最小
`rune` = `int32`
`uintptr`
无`double`，为`float64`
`complex64`实数虚数
`complex128`实数虚数

### 语言定义

`{`不能位于单独的行

#### 符号定义

##### 变量定义

- `var id+ [\[size\]*] type [= initVal+]`
- `var id+ = [\[size\] type]initVal+` 自行判断type
- `id+ := [\[size\] type]initVal+`

**数组**

```
id := [size]* type {
    
}
```

数组初始化还可以类似map的方式：`{index: value}`，可以与顺序赋值混合使用：

```
var id = [...]type {vals...}
```

数组的size在声明时可以为0，任意一维为0，则整个数组内存占用为0，于是可以有以下操作：

```go
var times [5][0]int
for range times {
       fmt.Println("hello")
}
```

为range次数做法；

```go
    c1 := make(chan [0]int)
    go func() {
        fmt.Println("c1")
        c1 <- [0]int{}
    }()
    <-c1

```

为管道同步操作

结构体数组key为结构体域

可以用`fmt.Printf`函数提供的`%T`或`%#v`谓词语法来打印数组的类型和详细信息

数组长度`len(arr)`

**字符串**

源码：UTF-8编码，`rune`序列，但也可以通过byte访问

字符串相当于

```
data := "abc"
var data = [...]byte{
       [charString...] // 'a'
}
```

可使用切片，`len`

**切片**

声明指针在type前加*，空指针为`nil`

无size时为切片定义，其他定义方式：

- `var id []type` 不定义长度
- `var id []type = make([]type, len)` 定义长度
- `var id []type = make([]type, len, capacity)` 定义长度，容量

初始化右值：

- `[]type {num...}`
- `arr[start:end]`左闭右开，arr也有可能是切片，缺省值时为`0`与`len(arr)`

- `len(slice)`获取长度
- `cap(slice)`获取容量

数组或切片作函数参数时可省略长度信息，空切片为`nil`

`append(slicel, num...)`尾部添加元素
`copy(slice1, slice2)`拷贝切片内容

map作为type时格式：`map[type]type`，声明也可使用

`id := make(map[type]type)`

加入元素为：

`id[key] = value`

删除元素：

`delete(id, key)`

可以进行类型转换，格式：`typeName(expr)`

对于基础类型不支持隐式类型转换

##### 常量定义

常量定义将变量定义`var`改为`const`

`iota`:被编译器修改的常量，在每次const出现时归0，内部使用时按行从0累加（即使该行未使用）：

```go
const (
    a = iota
    b = iota
    c = iota
)

// => simplified

const (
    a = iota
    b
    c
)
```

##### 结构体定义

```
type structName struct {
    varName type
}
```

type作用类似C的`typedef`

##### 接口定义

```
type interfaceName interface {
       funcioneName(paramDef) (?[returnTypes...])?
}
```

定义相同类型函数后使用：

```
var id = interfaceName
id = new(implementFunctionName)

var (
       id interfaceName = implementFunctionName // 支持显示或隐式类型转换
)
```

#### channel

共享内存(array)

声明：

```go
ch := make(chan int, bufferSize)
```

关闭通道：`close(ch)`

遍历通道使用range，在通道未能产生新的数时阻塞，通道被关闭时结束遍历操作

#### 分支

`switch`用于一个变量的多分枝判断选择，每次只能有一个条件成立

`select`不指定变量，`case`为条件语句，每次可能会有多个语句成立，`select`将会随机挑选执行，也可能没有语句成立，则会阻塞直到某分支能够执行

#### 循环

格式：

- `for init; condition; post { }`
- `for condition { }`
- `for { }`
- `for key := range array { }`
- `for key, value := range array { }`
- `for _, value := range array { }`

#### 函数

`func funcName(varName type? [, varName type]) (?[returnType...]`)?

函数定义是参数相同类型的可以放在一起，在最后加`type`，支持多个返回值，多个返回值加`()`

go显然支持了C的函数指针，因此支持回调函数

##### 函数定义

在函数内也可进行嵌套的函数定义，复制给某个变量

##### 函数参数

函数可以作为另一个函数的参数，参数名后类型为函数形式

##### 函数闭包

函数返回值定义为匿名函数，返回后可直接使用，且栈帧位置一致，多次调用操作同一函数空间

```go
package main

import "fmt"

func getSequence() func() int {
   i:=0
   return func() int {
      i+=1
     return i  
   }
}

func main(){
   /* nextNumber 为一个函数，函数 i 为 0 */
   nextNumber := getSequence()  

   /* 调用 nextNumber 函数，i 变量自增 1 并返回 */
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   
   /* 创建新的函数 nextNumber1，并查看结果 */
   nextNumber1 := getSequence()  
   fmt.Println(nextNumber1())
   fmt.Println(nextNumber1())
}

/* output
1
2
3
1
2
 */
```

##### 函数作为结构体方法

结构体可以定义有关方法：

```go
func (varName structName) funcName ...
```

若对结构体域有更改，需要使用指针：

```go
func (varName *structName) funcName ...
```

### range

应用于`array, slice, map, channel`

### go

并发，开启新线程执行函数