# go basic

## 1.内置数据类型标符

```go
内置数据类型
	数值
		整型 12
			byte int int8 int16 int32 int64
			uint unint8 uint16 uint32 uint64 uintptr
	  浮点数 2
			float32 float64 浮点数自动类型推断为 float64 不能使用 == 或者 ！= 操作两个浮点数  -> math
	  复数 2
			complex64 complex128  
			内置三个函数 
			var v=complex(2.1,3) 构造一个复数
			a := real(v) 返回实部
			b := imag(v) 返回虚部
			
	字符和字符串
		string rune
		字符串是常量，可以通过类似数组索引访问字节单元,不能修改
			var a = "hello"
  	  b := a[0]
	  字符串转换为切片[]byte(s) 要慎用,尤其数据量大的时候,每转换一次需要复制内容
			c := [] byte(a)
		字符串尾部不包含 NULL 字符
		字符串底层是一个二元的数据结构，一个是指向字节数组的起点，一个是长度
		基于字符串创建的切片和原字符串指向相同的底层字符数组，一样不能修改，对字符串的切片操作返回的子串仍然是 string
    字符串可以转换为字节数组，也可以转换为 Unicode 的字符数组
      a := "hello"
      b := []byte(a)
      c := []rune(a)
		字符串的拼接 + len(a) 获取长度
      for i,v :=range a{
        fmt.Println(i,v)
      }
    字符类型
			rune Unicode 编码
			byte uint8 别名
				
	接口型
		error
	布尔型
		bool
```

``go 是强类型静态编译型语言，定义变量和常量需要显式指出数据类型，也支持自动类型推导``

``在指定新类型或者函数时，必须显式带上类型标识符``

## 2.内置函数

```
make
new 
len
cap
append
copy
delete
panic
recover
close
complex
real
image
Print
Println
```

## 3.常量值识符

```
true false
iota        连续枚举类型声明
nil				  指针/引用类型变量默认值
```

## 4.空白标识符

```
_ 
匿名变量占位 ，用来忽略返回值和强制编译器类型检查
```

## 5.变量

``显式声明变量  var varName varType [varValue],声明后立马分配空间``

``短类型声明 varName := varValue ``

**编译器使用栈逃逸技术能够自动为变量分配空间，可能在栈上也可能在堆上**

## 6.常量

```
常量分为布尔型、字符串常量和数值型常量，常量存储在程序的只读段内
预声明标识符 iota ,初始值为 0
一组多个常量同时声明时其值逐行递增加，可以看作是自增的枚举变量
分开的 const 语句，iota 每次从 0 开始
```

```go
const (
	c0=iota
  c1=iota
  c2=iota
)
const (
	c0=iota
  c1
  c2
)
```

## 7.复合数据类型

- 指针     *[pointerType]

```
声明 *T 
支持多级指针 **T
变量名前加 & 获取变量的地址
```

```go
赋值语句中 *T 出现在 = 左边表示指针声明，*T 出现在 = 右边表示取指针指向的值
var a =1
p := &a
*p 和 a 的值都是 1	
```

```go
结构体指针访问结构体字段仍然使用 ". 操作符"
type User struct{
	name string
  age int
}
aladdin := User{
  name : "aladdin",
  age : 23,
}
u := &aladdin
fmt.Println(u.name)
```

```go
不支持指针运算 non-numeric type *T 错误
函数中允许返回局部变量的地址
编译器使用 ”栈逃逸“ 机制将这种局部变量的空间分配在堆上
func sum(a,b int) *int{
		sum := a+b
		return &sum
}
```



- 数组     [n] elementType

```go
var arr [2]int 两个元素默认类型零值 0
arr := [...]float64{7.0,8.5}
a := [3]int{1,2,3}//指定长度和初始化字面量
a := [...]int{1,2,3}//不指定长度，初始化列表数量确定长度
a := [3]int{1:1,2:3}//指定总长度，通过索引值进行初始化，没有指定默认类型零值
a := [...]int{1:1，2；3}
```

```
数组创建完长度就固定，不能在追加
数组是值类型，数组赋值或者作为函数参数都是值拷贝
数组长度是数组的组成部分，[10]int 和 [20]int 表示不同的类型
```



- 切片     [] elementType

- map    map[keyType] valueType

- chan   chan valueType

- struct  

  ```go
  struct{
   fieldName fieldType
  }
  ```

- Interface 

  ```go
  interface{
  		method1(inputParams)(returnParams)
  }
  ```

  

