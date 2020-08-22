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

**显式声明变量  var varName varType [varValue],声明后立马分配空间**

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

### 指针     

```
*[pointerType]
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



### 数组     

```go
[n] elementType
var arr [2]int 两个元素默认类型零值 0
arr := [...]float64{7.0,8.5}
a := [3]int{1,2,3}//指定长度和初始化字面量
a := [...]int{1,2,3}//不指定长度，初始化列表数量确定长度
a := [3]int{1:1,2:3}//指定总长度，通过索引值进行初始化，没有指定默认类型零值
a := [...]int{1:1，2；3}
```

```
数组创建完长度就固定，不能在追加，不同长度的数组不相同
数组是值类型，数组赋值或者作为函数参数都是值拷贝
数组长度是数组的组成部分，[10]int 和 [20]int 表示不同的类型
二维数组只有第一层可以使用 ... 推导长度
[n]*T表示指针数组，*[n]T表示数组指针
```

### 切片  

```go
[] elementType
变长数组，数据结构中有指向数组的指针，是一种引用类型
type slice struct{
	array unsafe.Pointer
	len int
	cap int
}
切片维护了三个元素 1.指向底层是数组的指针，切片的元素数量和底层数组的容量
在切片上限是容量不是长度
判断是否为空 len ==0
copy 只会复制长度最小的
```

```
扩容是二倍扩容
append 添加 并返回切片 二倍扩容 声明后可以添加
	扩容针对不同的数据类型处理不一样
		首先判断，如果新申请容量（cap）大于2倍的旧容量（old.cap），最终容量（newcap）就是新申请的容量（cap）。
		否则判断，如果旧切片的长度小于1024，则最终容量(newcap)就是旧容量(old.cap)的两倍，即（newcap=doublecap），
		否则判断，如果旧切片长度大于等于1024，则最终容量（newcap）从旧容量（old.cap）开始循环增加原来的1/4，
		即（newcap=old.cap,for {newcap += newcap/4}）直到最终容量（newcap）大于等于新申请的容量(cap)，即（newcap >= cap）
		如果最终容量（cap）计算值溢出，则最终容量（cap）就是新申请容量（cap）。
	删除索引为index的元素，操作方法是a = append(a[:index], a[index+1:]...)
```

```
切片的创建
1.由数组创建
array[b:e] b 开始索引，可以不指定默认是0，e结束索引，默认是len（array），取不到
[>=low:<height]
var array := [...]int{0,1,2,3,4,5,6}
s1 := array[0,4]
s2 := array[:4]
s3 := array[2:]
2.内置函数 make make 创建的切片各元素初始化为切片元素类型的零值
a := make([]int，10) //cap 10
b := make([]int,10,15) // len 10 cap 15
直接声明切片类型变量是没有意义的
```

```
内置函数
len() 切片长度
cap() 切片底层数组容量
append() 对切片追加
copy() 复制切片
```

### map    

```
map[keyType] valueType
map[K]T k 可以是任意可以进行比较的类型 T 是值类型,map也是一种引用类型
```

```
创建
1.字面量创建
map := map[string]int{"a":1,"b":2}
2.内置 make
make(map[K]T)
make(map[K]T,len)
赋值,访问
map[1]="tom"
判断是否存在 v,ok:=map[key]
fmt.Println(map[1])
range 遍历不保证每次迭代元素的顺序
删除
delete(mapName,key)
内置的map 不是并发安全的，并发安全的可以使用标准包 sync 中的map
不要直接修改 map value 内某个元素的值，如果要修改，必须整体赋值（内部是结构体）
```

### chan  

```
chan valueType
```



### struct  

```go
struct{
 fieldName fieldType
}
多个不同类型元素组合而成，类型可以任意
存储空间是连续的，字段按照声明时的顺序存放，【字段对齐】
【define】
type TypeName struct{
  fieldName fieldType
}
```

### Interface 

```go
interface{
		method1(inputParams)(returnParams)
}
```

## 8.控制结构

``控制和跳转``

```
无条件的跳转 函数调用、goto
有条件的跳转 分支、循环
```

**go源代码的顺序并不一定是最终可执行的指令顺序，涉及语言的运行时和包的加载**

- if

```
if 后面语句不需要小括号扩起来
{ 必须在行尾 或者 if 、if else 放在一行
if 后面可以带初始化语句，分号分割，声明的变量作用域是整个if语句块
【没有条件运算符】
```

- switch

```
switch 后面可以带一个初始化语句，条件表达式可以是任意支持相等的比较运算符类型变量
fallthrough 语句可以强制执行下一条 case 子句 不再进行判断
case 后面可以多个 用 , 分割
```

- for

```
for [init];[condition];[post]{

}
```

- 标签和跳转

```
label 标签搭配 goto、break、continue
goto 只能跳转到同级作用域或者上层作用域内，不能跳到哪步作用域内
goto 只能在函数内跳转
goto 不能跳过内部变量声明语句
```

## 9.函数

```
go 函数是一种类型，函数类型的变量可以像其他类型变量一样使用，可以作为函数的参数或者返回值
函数支持多值返回，支持闭包，支持可变参数
```

```
func 函数名 参数列表 返回列表{
	函数体
}
函数首字母大小写决定了函数在其他包的可见性 【大写其他包可见】
【函数返回值如果是单个且不是非命名的参数,可以省略（）】
	{ 必须位于函数返回值同行的行尾
}
【函数支持命名的返回值】
【sum 相当于函数内的局部变量，被初始化为零】
func add（a,b int）(sum int){
	sun = a + b
	...
	return //return sum 简写
	sum := a + b // sum := a + b  相当于新声明了一个 sum 变量命名返回变量 sum 覆盖
	return sum. 
}
【不支持默认值参数、不支持函数重载、不支持命名函数的嵌套定义】
【局部变量和全局变量重名 优先访问局部变量】
```

#### 多值返回

```
定义多值返回的参数列表要用 （）
一般将错误类型作为最后一个返回值
```

#### 参数传递

```
go 函数实参数到形参的传递永远是值拷贝、地址
```

#### 不定参数

```
go 支持不定数目的参数 param ...type
必须类型相同，必须是函数最后一个参数
不定参数名在函数体内相当于切片，对切片的操作同样适合对不定参数的操作
【切片可以作为参数传递给不定参数,切片名要加上 ... 】
func main(){
	slice := []int{1,2,3}
	sum(slice...)
}
func sum(arr ...int) (sum int){}
【形参为不定参数的函数和形参为切片的函数类型不相同】
```

#### 函数签名

```
函数类型又叫做函数签名
一个函数的类型就是函数定义首行去掉函数名、参数名、{ ，可以使用 fmt.Printf 的 %T 打印函数的类型
【两个函数类型相同的条件: 拥有相同的形参数列表和返回值列表{列表元素的次序、个数、类型都相同},形参名可以不同】
【函数类型和 map、slice、chan 一样,实际函数类型变量和函数名都可以当作指针变量，指针指向函数代码开始位置，函数类型变量是一种引用类型，未初始化的函数类型的变量默认是 nil】
【有名的函数名可以看作函数类型的常量，可以直接使用函数名调用函数，也可以直接赋值给函数类型变量,后续通过变量来调用函数】
```

```go
func sum(a,b int) int{
	return a + b
}
func main(){
	sum(3,4)//直接调用
	f := sum 有名函数可以直接赋值给变量
	f(3,4) 
}
```

#### 匿名函数

```
匿名函数可以看作函数字面量，【直接使用函数类型变量的地方都可以由匿名函数代替】
【匿名函数可以直接复制给函数变量，可以当作实际参数，也可以作为返回值，还可以直接调用】
```

```go
//直接复制函数变量
var sum = func(a , b int) int {
  return a + b
}
//函数式接口 函数作为参数
func doinput(f func(int,int) int,a,b int) int{
  return f(a,b)
}
//函数作为返回值
func wrap(op string) (func(int,int) int,error){
  switch op{
    case "add":
	    return func(a,b int)int{
  	    return a+b,nil
  	  }
    case "sub":
    return func(a,b int) int{
      	return sub,nil
    }
	  default:
    err := errors.New("not shut in function wrap")
    	return nil,err
  }
}
//直接被调用
defer func(){
  if err := revocer();err != nil{
    fmt.Println(err)
  }
}()
//作为参数
doinput(func(x,y int)int{
  	return x+y
},1,2)
//返回值
opFunc := wrap("add")
re := opFunc(2,3)
```

#### defer

```
defer 可以注册多个延迟调用，【调用以先进后出 FILO 顺序在函数返回前被执行】
有点类似 java 的 finally ，常用语保证一些资源最终能够得到回收和释放
【defer 后面必须是函数或者方法的调用,不能是语句】
【defer 函数的实参数是在注册时通过值拷贝进去】
【defer 必须先注册后才能执行，如果defer 位于return之后,因为没有注册不会执行，调用os.Exit(int)退出进程时，defer 不再执行即使已经注册】
【一般 defer 语句放在错误检查语句之后,defer 会推迟资源的释放，尽量不要放在循环语句里面】
```

```go
func f()int{
  a := 0
  defer func (i int){
    println("defer i =",i)
  }(a)
  a++
  return a
}

//结果
defer i=0
```

#### 闭包

```
闭包是由函数和相关引用环境组合而成的实体
一般通过在匿名函数中引用外部函数的局部变量或者全局变量构成
闭包= 函数 + 引用环境
【闭包对闭包外的环境引入是直接引用，编辑器检查到闭包，会将闭包引用的外部变量分配到堆上】
如果函数返回的闭包引用了该函数的局部变量(参数或函数内部变量)
1.多次调用该函数，返回的多个闭包所引用的外部变量是多个副本，原因是每次调用函数都会为局部变量分配内存
2.用一个闭包函数多次，如果该闭包修改了其引用的外部变量，则每一次调用该闭包对该外部变量都有影响，因为闭包函数共享外部引用
```

```go
func main() {
	f := fa(1)
	g := fa(1)
	println(f(1))
	println(f(1))
	println(g(1))
	println(g(1))
	//0xc0000180b0 1
	//2
	//0xc0000180b0 2
	//3
	//0xc0000180b8 1
	//2
	//0xc0000180b8 2
	//3
}
func fa(a int) func(i int) int {
	return func(i int) int {
		println(&a, a)
		a = a + 1
		return a
	}
}

```

```
f g 引用不同的 a
如果函数调用返回的闭包引用修改了全局变量,则每次调用都会影响全局变量
如果函数返回的闭包引用是全局变量 a，则多次调用函数返回的多个闭包引用的都是同一个 a
调用一个闭包多次引用的也是同一个 a
此时如果闭包中修改了a 值的逻辑，则每次闭包调用都会影响全局变量 a 的值
【使用闭包是为了减少全局变量，所以闭包引用全局变量不是好代码风格】
```

```go
//多个闭包共享局部变量
func faa(base int) (func(int) int, func(int) int) {
	println(&base, base)
	add := func(i int) int {
		base += 1
		println(&base, base)
		return base
	}
	sub := func(i int) int {
		base -= 1
		println(&base, base)
		return base
	}
	return add, sub
}
func main() {
	f, g := faa(0)
	s, k := faa(0)
	//
	println(f(1), g(3))
	println(s(1), k(3))
	//0xc00008e000 0
	//0xc00008e008 0
	//0xc00008e000 1
	//0xc00008e000 0
	//1 0
	//0xc00008e008 1
	//0xc00008e008 0
	//1 0
}
//f g 引用同一个base，s k引用一个base，
```

```
闭包设计减少全局变量，在函数调用过程中隐式的传递共享变量
对象是附有行为的数据，闭包是附有书的行为
```

## 10.Panic 和 recover

```
内置函数处理 go 运行时错误,panic 主动抛出错误,recover 用来捕获 panic 抛出的错误
函数签名
panic(i interface{})
revocer()interface{}
```

```
引发 panic 有两种情况
一种是主动调用 panic函数
另一种是程序产生运行时错误抛出
发生 panic 后，程序会从调用 panic 的函数位置或发生 panic 的地方立即返回
逐层向上执行函数的 defer 语句,然后逐层打印堆栈,知道被 recover 捕获或者运行到最外层函数而退出
defer 逻辑里也可以再次调用 panic 或抛出 panic ,defer 里面的 panic 能够被后续执行的 defer 捕获
recover() 用来捕获 panic 继续向上传递，【搭配 defer 只有在 defer 后面的函数体内被直接调用才能捕获 panic 终止异常,否则返回 nil,异常继续向外传递】
【可以有连续多个 panic 被抛出,连续多个panic 的场景只能出现在延迟调用中，只有最后一次 panic 能被捕获】
【init 函数引发的 panic 只能在 init 函数中捕获】
【函数并不能捕获内部新启动的 goroutine 抛出的 panic】
```

## 11.error

```
withStack 
https://www.bilibili.com/video/BV1PJ411d7aA?from=search&seid=14568917228173979190
```

# 类型系统

- 命名类型

```
命名类型可以通过标识符来表示，go 基本类型有 20 个预声明简单类型
自定义类型是命名类型
```

- 未命名类型

```
类型由预声明类型、关键字、操作符组成
复合类型 数组、切片、map、channel、指针、function、struct、interface 都是类型字面量，也都是未命名类型
```



## 自定义类型

```
type newType oldType
自定义类型的 newType 的底层类型是逐层递归向下查找的，知道查找到 oldType 是预声明类型或类型字面量为止
oldType 可以是 【自定义类型，预声明类型，未命名类型】
【newType 和 oldType 具有相同的底层类型，并且都继承了底层类型的操作集合】
type T1 string
type T2 T1
type T3 []string
type T4 T3
type T5 []T1
type T6 T5
【T1 和 T2 底层类型都是 string，T3 和 T4 的底层类型都是 []string】
【T6 和 T5 底层类型都是 []T1】
【T6,T5 和 T3,T4 底层类型是不一样的,一个是 []T1，一个是 []string】
【底层类型在类型赋值和类型强制类型转换时会使用】
```

## 类型赋值

```
go 是强类型语言
两个命名类型相同的条件
1.两个类型声明的语句完全相同
2.命名类型和未命名类型永远不相同
3.两个未命名类型相同的条件是他们的类型声明字面量的结构相同，并且内部元素类型相同
4.通过类型别名语句声明的两个类型相同
```

```
go 1.9 引入了类型别名语法 type T1 = T2 
t1 类型完全和 t2 一样
历史问题，新旧类型兼容
```

```
类型是 T1 的变量 a
类型是 T2 的变量 b
a 可以赋值给 b 的条件必须要满足一个
1.T1 和 T2 类型相同
2.T1 和 T2 具有相同的底层类型,并且 T1 和 T2 里面至少有一个是未命名类型
3.T2 是接口类型，T1 是具体类型,T1 的方法集是 T2 方法集 的超集
4.T1 和 T2 都是通道类型，他们拥有相同的元素类型,并且至少有一个是未命名类型
5.a 是预声明标识符 nil，T2 是 pointer，function,slice,channel,map,interface类型中的一种
6.a 是一个字面常量值，可以用来表示类型 T 的值   　　　　　　　　    
```

## 类型强制转换

```
非常量类型的变量 x 可以强制转换并传递给类型 T 需要满足如下任意条件
1.x 可以直接复制给 T 类型变量
2.x 的类型和 T 类型具有相同的底层类型
3.x 的类型和 T 都是未命名的指针类型，并且指针指向的类型具有相同的底层类型
4.x 的类型和 T 类型都是整数，或者都是浮点型
5.x 的类型和 T 类型都是复数
6.x 是整数值或 []byte 类型值，T 是 string 类型
7.x 是一个字符串，T 是 []byte 或 []rune
```

```
⚠️
数值类型和 string 类型之间的相互转换可能造成值部分丢失
其他的转换仅是类型的转换，不会造成值的改变，string 和数字之间的转换可使用 strconv
【不支持指针和 interger 之间的直接转换，可以使用 unsafe 包处理】
```

# 方法	

### 类型方法

```go
类型接收
func (c TypeName) methodName(ParamList)(ReturnList){
  method body
}
func (t *TypeName) methodName(ParamList)(ReturnList){
  method body
}
```

```
类型方法
1.可以为命令类型增加方法（除了接口）,非命名类型不能自定义方法
2.为类型增加方法有一个限制，方法的定义必须和类型的定义在同一个包中
3.方法的命名空间的可见性和变量一样，大写开头的方法可以在包外被访问
4.使用 type 定义的自定义类型是一个新类型，新类型不能调用原有的类型方法，但是底层类型支持的运算可以被新的类型继承
```

### 方法调用

