## JAVA 转GO初学者笔记
```golang
/**
	包生生命 main 的时候 该包下面的main方法才能执行
*/
package main

/**
	导入某个 package
*/
import (
	"fmt"
)

func main() {
	fmt.Println("Hello,世界")
	test()
}

/**
   	$GOPATH 默认指向 $HOME/go 编译后二进制存放路劲
	$GOROOT Go环境的安装目录
*/

```

### 保留关键字 基本与Java字重合 除开 fallthrough

`break,default,func,interface,select,case,defer,go,map,struct,chan,else,goto,package,switch,const,fallthrough,if,range,type,continue,for,import,return,var`
### 预定义标识符
`append,bool,byte,cap,close,complex,complex64,complex128,uint16,copy,false,float32,float64,imag,int,int8,int16,uint32,int32,int64,iota,len,make,new,nil
,panic,uint64,print,println,real,recover,string,true,uint,uint8,uintptr`

### 数据类型
1.  bool 
2.  number
3.  string
4.  pointer array struct channel function interface map slice切片(?)

### 变量声明
```golang
var a, b uint8 //声明多个同类型变量uint8，默认值为0
var c string   //声明单个字符串变量 默认值""
var d bool     //声明布尔变量 默认值false
//nil 为派生类型的默认值
var a1 *int             //定义一个int指针
var a2 []int            //定义一个int数组变量
var a3 map[string]int   //定义一个map key：string value:int
var a4 chan int         //定义一个管道类型
var a5 func(string) int //定一个函数
var a6 error            //定义一个error 接口变量
//自动推断类型
var a7 = true //定义 bool 类型变量
//a7 = 1; 编译报错
/**
a8:=false 声明并复制。需要写在func(){ 这里面 }
*/
//a8:=false 直接写编译报错

//声明多个变量，通常用于声明全局变量。因式分解关键字？
var (
	a9  int
	a10 = true
)

```

### 声明常量，把语法规则的var 变成const 及为声明常量

`const b1 = 1`
iota 枚举自增计数器？[具体使用方法参见](https://studygolang.com/articles/2192)
`const name = iota`


### 算数运算符
`+ - * / % ++ -- >> <<`
### 关系运算符
`== > < != >= <=`
### 逻辑运算符
`&& || !`
### 位运算符
`& (与) | (或) ^ (异或)`
### 赋值运算符
`= += -= *= /= %= <<= >>= &= ^= |=`
### 地址运算符
`&返回存储地址 *访问指针`

```golang
func test() {
	test1 := 1
	t1p := &test1
	t1l := *t1p
	fmt.Println(t1p, t1l)
}```

>tip:运算符优先级：使用Java的习惯，一目>二目 算数>逻辑

### 条件语句

```golang
    if true{}
	if true {} else {}
	if true {} else if false {} else {}
	//按照条件执行某一个case 分支。没有就执行default。
	a := 1
	switch a {
	case 0:
		{
			break
		}
	default:
		{
		}
	}
	/**shi'jia
	类似Java中的for循环语句。随机选择某一个满足条件的case执行。如果都没有则执行default。如果没有default则阻塞到满足条件可执行为止
	所以case 表达式必须是 接收、发送或分配接收。可以理解为非阻塞的io操作。
	*/
	c1 := make(chan int)//声明管道
	c2 := make(chan string)//声明管道
	for {
		select {
		case c11 := <-c1: //接受赋值
			{
				if c11 == 1 {
					goto end
				}
			}
		case <-c2: //接受
			println("no")
		case c2 <- "h1": //发送

		default: //默认可匹配表达式
			println("no")
		}
	}
end:
```
