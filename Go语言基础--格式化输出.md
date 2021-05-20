# Go语言基础--格式化输出

## Print、Println 、Printf` `、Sprintf 、Fprintf的区别：

Print:   输出到控制台(不接受任何格式化，它等价于对每一个操作数都应用 %v)
Println: 输出到控制台并换行
Printf : 只可以打印出格式化的字符串。只可以直接输出字符串类型的变量
Sprintf：格式化并返回一个字符串而不带任何输出。
Fprintf：来格式化并输出到 io.Writers 而不是 os.Stdout。

## Printf常用占位符：

1. v        值的默认格式。
2. %+v   添加字段名(如结构体)
3. %#v   相应值的Go语法表示 
4. %T     相应值的类型的Go语法表示 
5. %%    字面上的百分号，并非值的占位符
6. %p      指针地址

### 常用占位符示例：

```go
student := Student{Name: "golang", Age: 10, CreateTime: time.Now(), PassExamine: true}
fmt.Printf("%%v只显示值:%v\n", student)
// {golang 10 true 2020-06-28 14:46:53.0620998 +0800 CST m=+0.011000301}
fmt.Printf("%%+v显示字段名和值:%+v\n", student)
//{Name:golang Age:10 PassExamine:true CreateTime:2020-06-28 14:46:53.0620998 +0800 CST m=+0.011000301}
fmt.Printf("%%#v字段名、值和类型区分:%#v\n", student)
//example.Student{Name:"golang", Age:10, PassExamine:true, CreateTime:time.Time{wall:0xbfb62d1743b39158, ext:11000301, loc:(*time.Location)(0xce75a0)}}
fmt.Printf("%%T值的类型:%T\n", student)
//example.Student
fmt.Printf("%%p指针地址:%p\n", &student)
//0xc00010cb40
```
### 数字类型的一些占位符示例：

```go
val := 88
fmt.Printf("%%b 二进制:\t%b\n", val)
fmt.Printf("%%c Unicode字符:\t%c\n", val)
fmt.Printf("%%d 十进制:   \t%d\n", val)
fmt.Printf("%%o 八进制:\t%o\n", val)
fmt.Printf("%%q ASCII: \t%q\n", val)
fmt.Printf("%%x 小写十六进制:\t%x\n", val)
fmt.Printf("%%X 大写十六进制:\t%X\n", val)
fmt.Printf("%%U Unicode格式:\t%U\n", val)

%b 二进制:             1011000
%c Unicode字符: 		 X
%d 十进制:             88
%o 八进制:             130
%q ASCII:             'X'
%x 小写十六进制:        58
%X 大写十六进制:        58
%U Unicode格式:        U+0058
```

### 实例化结构体的方式

```go
//值类型
u1 := Student{}fmt.Println("Student{},", u1)
var u2 Studentfmt.Println("var u2 Student,", u2)
//指针类型
u3 := new(Student)fmt.Println("new(Student),", u3)
u4 := &Student{}fmt.Println("&Student{},", u4)
var u5 *Studentfmt.Println("var u5 *Student,", u5) //空指针

Student{}, { 0 false 0001-01-01 00:00:00 +0000 UTC}
var u2 Student, { 0 false 0001-01-01 00:00:00 +0000 UTC}
new(Student), &{ 0 false 0001-01-01 00:00:00 +0000 UTC}
&Student{}, &{ 0 false 0001-01-01 00:00:00 +0000 UTC}
var u5 *Student, <nil>
```