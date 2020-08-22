# JVM

[TOC]



### 1.区域划分

​		![区域划分](../picture-md/jvm.png)

``线程私有 虚拟机栈，本地方法栈，程序计数器``

#### 1.程序计数器

```
线程私有
存放当前线程字节码指令，字节码解释器通过改变程序计数器存的值，来选去下一条来执行的指令。
流程控制，异常，跳转，线程恢复等功能都是通过计数器实现。
线程私有，独自存储，可以保证多线程切换线程可以回到正常的位置，互不干扰。
由于存放的是下一条指令地址，所以不会出现 oom
```

#### 2.栈

```
虚拟机栈 线程私有，生命周期与线程相同。
描述的是 java 方法执行的线程内存模型，每个方法执行会同步创建栈帧。
存储 局部变量表，操作数栈，动态链接，方法出口。
方法调用到执行完毕，对应一个栈帧在虚拟机栈中入 到出栈过程
```

```
局部变量表
存放编译期间可知的基本数据类型、对象引用、returnAddress 类型（指向一条字节码指令）
数据类型在局部变量表中的存储空间以局部变量槽（slot）表示，64 长度的 long 和 double 类型的数据会占用两个变量槽，其余数据类型只占用一个。
局部变量表所需要的内存空间在编译器完成分配，当进入一个方法时，这个方法需要在栈帧中分配多大的局部变量空间是完全确定的，运行期间不回改变局部变量表的大小（slot 数量）
```

```
如果线程请求的栈深度大于虚 拟机所允许的深度，将抛出StackOverflowError异常;
如果Java虚拟机栈容量可以动态扩展[2]，当栈扩展时无法申请到足够的内存会抛出OutOfMemoryError异常。
Hotspot 不支持栈扩展,申请成功了就不会 oom
```

#### 3.堆

```
线程共享，虚拟机启动创建，存放几乎所有对象实例和数组，物理不连续
```

#### 4.方法区（Meta-space）

```
JDK7 之前永久代 PermSpace
线程共享
用于存储已被虚拟机加载的类型信息、常量、静态变量、即时编译器编译后的代码缓存等数据
jdk8 改用了本地内存实现元空间
和堆一样，不连续存储，可以选择固定大小，可拓展，还可以选择不实现垃圾收集
也可以进行垃圾回收，主要针对常量池和类型卸载
低版本的 HotSpot 方法区可能会出现 oom
```

#### 5.运行时常量池

```
方法区的一部分，class文件中除了有类似的版本、字段、方法、接口等信息，还有一部分是常量池
用于存放编译期间生成的各种字面量与符号引用，类加载后存放到方法区的运行时常量池中
```

```
动态性
常量也可以运行时产生，可以在运行时放入
String.intern()
```

```
无法申请内存也会 oom
```

#### 6.直接内存

```
会影响动态扩展 oom
在JDK 1.4中新加入了NIO(New Input/Output)类，引入了一种基于通道(Channel)与缓冲区 (Buffer)的I/O方式，它可以使用Nat ive函数库直接分配堆外内存，然后通过一个存储在Java堆里面的 DirectByteBuffer对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在Java堆和Native堆中来回复制数据。
虚引用
DirectByteBuffer
```

### 7.本地内存

```
进程内存，jvm运行时内存+本地内存
```

![dbb](../picture-md/dbb.png)

```
如果用HeapByteBuffer来接收网络数据
需要把数据从内核先拷贝到一个临时的本地内存，再从临时本地内存拷贝到 JVM 堆,而不是直接从内核拷贝到 JVM 堆上。
数据从内核拷贝到 JVM 堆的过程中，JVM 可能会发生 GC，GC 过程中对象可能会被移动，
也就是说 JVM 堆上的字节数组可能会被移动，这样的话 Buffer 地址就失效了。
如果这中间经过本地内存中转，从本地内存到 JVM 堆的拷贝过程中 JVM 可以保证不做 GC。
```

```
如果使用 HeapByteBuffer，你会发现 JVM 堆和内核之间多了一层中转，而 DirectByteBuffer 用来解决这个问题
DirectByteBuffer 对象本身在 JVM 堆上，但是它持有的字节数组不是从 JVM 堆上分配的，而是从本地内存分配的。
DirectByteBuffer 对象中有个 long 类型字段 address，记录着本地内存的地址
在接收数据的时候，直接把这个本地内存地址传递给 C 程序，C 程序会将网络数据从内核拷贝到这个本地内存
JVM 可以直接读取这个本地内存，这种方式比 HeapByteBuffer 少了一次拷贝，因此一般来说它的速度会比 HeapByteBuffer 快好几倍。
```



### 2.HotSpot 对象创建

（普通对象，不包括数组和Class对象）

##### **步骤**

```
new指令
检测参数能不能在常量池中定位到类的符号引用
检查类的引用是否已经加载、解析、初始化
加载类 
分配内存 
	取决堆是否是规整的内存空间
		规整 指针碰撞  【使用过的内存 ｜指针｜ 空闲内存】 指针移动 
				并发会出问题 
					使用 cas和 重试机制避免
					TLAB 内存分配按照线程申请空间 本地缓存区用完了分配新的需要同步锁定
		不规整 空闲列表 列表存储可用空闲内存地址 查找适合的地址分配 更新列表
设置对象必要信息 
	元信息 实例对应类信息 gc分代信息 是否启用偏向锁
虚拟机创建对象结束

java代码
class <init>构造

```

##### 过程

**new指令 --> 检测参数能不能在常量池中定位到类的符号引用 --> 检查类的引用是否已经加载、解析、初始化**

```
当Java虚拟机遇到一条字节码new指令时
首先将去检查这个指令的参数是否能在常量池中定位到 一个类的符号引用
并且检查这个符号引用代表的类是否已被加载、解析和初始化过。
如果没有，那必须先执行相应的类加载过程
```

```
在类加载检查通过后，接下来虚拟机将为新生对象分配内存。
对象所需内存的大小在类加载完成 后便可完全确定
为对象分配空间的任务实际上便等同于把一块确定 大小的内存块从Java堆中划分出来
```

##### 指针碰撞

```
假设Java堆中内存是绝对规整的
所有被使用过的内存都被放在一边，空闲的内存被放在另一边
中间放着一个指针作为分界点的指示器
那所分配内存就仅仅是把那个指针向空闲空间方向挪动一段与对象大小相等的距离
这种分配方式称为“指针碰撞”(Bump The Pointer)
```

##### 空闲列表

```
如果Java堆中的内存并不是规整的
已被使用的内存和空闲的内存相互交错在一起，那就没有办法简单地进行指针碰撞了
虚拟机就必须维护一个列表，记录上哪些内存块是可用的
在分配的时候从列表中找到一块足够大的空间划分给对象实例，并更新列表上的记录
这种分配方式称 为“空闲列表”(Free List)
```



```
选择哪种分配方式由Java堆是否规整决定
而Java堆是否规整又由所采用 的垃圾收集器是否带有空间压缩整理(Comp act)的能力决定。
因此，当使用Serial、ParNew等带压缩 整理过程的收集器时，系统采用的分配算法是指针碰撞，既简单又高效
当使用CM S这种基于清除(Sweep)算法的收集器时，理论上[1]就只能采用较为复杂的空闲列表来分配内存。
```

##### **创建对象可能出现并发问题**

```
除如何划分可用空间之外，还有另外一个需要考虑的问题:
对象创建在虚拟机中是非常频繁的行为，即使仅仅修改一个指针所指向的位置
在并发情况下也并不是线程安全的，可能出现正在给对象 A分配内存
指针还没来得及修改，对象B又同时使用了原来的指针来分配内存的情况。
```

```
解决这个问题 有两种可选方案:
一种是对分配内存空间的动作进行同步处理
实际上虚拟机是采用CAS配上失败重试的方式保证更新操作的原子性
另外一种是把内存分配的动作按照线程划分在不同的空间之中进行
每个线程在Java堆中预先分配一小块内存，称为本地线程分配缓冲(Thread Local Allocation Buffer TLAB)
哪个线程要分配内存，就在哪个线程的本地缓冲区中分配
只有本地缓冲区用完了，分配新的缓存区时才需要同步锁定
虚拟机是否使用TLAB，可以通过-XX:+/-UseTLAB参数来 设定。
```

```
内存分配完成之后，虚拟机必须将分配到的内存空间(但不包括对象头)都初始化为零值
如果使用了TLAB的话，这一项工作也可以提前至TLAB分配时顺便进行。
这步操作保证了对象的实例字段 
在Java代码中可以不赋初始值就直接使用，使程序能访问到这些字段的数据类型所对应的零值。
```

##### **对象设置必要信息**

```
接下来，Java虚拟机还要对对象进行必要的设置
例如这个对象是哪个类的实例、如何才能找到类的元数据信息
对象的哈希码(实际上对象的哈希码会延后到真正调用O bject ::has hCode()方法时才 计算)、
对象的GC分代年龄等信息。
这些信息存放在对象的对象头(Object Header)之中。
根据虚拟机当前运行状态的不同，如是否启用偏向锁等，对象头会有不同的设置方式。
```

**为了能在多数情况下分配得更快，设计了一个叫作Linear Allocat ion Buffer的分配缓冲区，通过空闲列表拿到一大块分配缓冲区之后，在它里面仍然可以使用指 针碰撞方式来分配。**

### 3对象内存布局

![对象头](../picture-md/object_header.png)

**对象头(Header)、实例数据(Instance Data)和对齐填充(Padding)。**

##### 对象头(64位 12B)

```
Mark Word：包含一系列的标记位，比如轻量级锁的标记位，偏向锁标记位等等
在32位系统占4字节，在64位系统中占8字节；
Class Pointer：用来指向对象对应的Class对象（其对应的元数据对象）的内存地址
在32位系统占4字节，在64位系统中占8字节；
Length：如果是数组对象，还有一个保存数组长度的空间，占4个字节；
```

```
两类信息
1.存储对象自身的运行时数据  MarkWord 
hashcode gc分代年龄 锁状态 线程持有锁 偏向线程id 偏向时间戳等
32 64位虚拟机中分别为32位和64位 MarkWord
2.类型指针 klass 64位 8B
对象指向想它的类型元数据的指针 通过这个指针来确定改对象是哪个类的实例
但是查找对象的元数据信息不一定要经过对象本身
3.如果对象是一个java数组，对象头还必须有一块用于记录数组长度的数据
因为虚拟机可以通过普通对象的元数据确定对象的大小，数组的额长度不确定，无法确定元数据中的信息推断出数组的大小
```

##### 实例数据

```
存储java代码真实数据 定义的字段 
无论是从父类继承下来的，还是在子类中定义的字段都必须记录起来
这部分的存储顺序会受到虚拟机分配策略参数(-XX:FieldsAllocationSty le参数)和字段在Java源码中定义顺序的影响。 
HotSpot虚拟机默认的分配顺序为
longs/doubles
ints
shorts/chars
bytes/booleans
oops(Ordinary Object Pointers，OOPs)，
从以上默认的分配策略中可以看到，相同宽度的字段总是被分配到一起存放
在满足这个前提条件的情况下
在父类中定义的变量会出现在子类之前
如果HotSpot虚拟机的 +XX:CompactFields参数值为true(默认就为true)
那子类之中较窄的变量也允许插入父类变量的空 隙之中，以节省出一点点空间。
```

##### 对齐填充

```
需要对齐才存在
HotSpot 要求对象起始地址必须是 8字节的整数倍
对象头部分不需要 正好 实例数据不一定 所以这一部分用来填充对齐
```

### 4.对象模型

```
HotSpot中采用了OOP-Klass模型，它是描述Java对象实例的模型，它分为两部分：

类被加载到内存时，就被封装成了klass，klass包含类的元数据信息
像类的方法、常量池这些信息都是存在klass里的
可以认为它是java里面的java.lang.Class对象，记录了类的全部信息；
OOP（Ordinary Object Pointer）指的是普通对象指针
包含MarkWord 和元数据指针
MarkWord用来存储当前指针指向的对象运行时的一些状态数据
元数据指针则指向klass
用来告诉你当前指针指向的对象是什么类型，也就是使用哪个类来创建出来的；

那么为何要设计这样一个一分为二的对象模型呢？
这是因为HotSopt JVM的设计者不想让每个对象中都含有一个vtable（虚函数表）
所以就把对象模型拆成klass和oop，其中oop中不含有任何虚函数
而klass就含有虚函数表，可以进行method dispatch。
```

![object](../picture-md/object.png)

```
上图可以看到，通过栈帧中的对象引用reference找到Java堆中的对象，再通过对象的instanceOop中的元数据指针klass来找到方法区中的instanceKlass，从而确定该对象的类型。

下面来分析一下，执行new A()的时候，JVM 做了什么工作
首先，如果这个类没有被加载过，JVM就会进行类的加载，
并在JVM内部创建一个instanceKlass对象表示这个类的运行时元数据（相当于Java层的Class对象）
初始化对象的时候（执行invokespecial A::）
JVM就会创建一个instanceOopDesc对象表示这个对象的实例
然后进行Mark Word的填充，将元数据指针指向Klass对象，并填充实例变量。

元数据—— instanceKlass 对象会存在元空间（方法区
而对象实例 instanceOopDesc 会存在Java堆
Java虚拟机栈中会存有这个对象实例的引用。
```

##### **成员变量重排序**

```
为了提高性能，每个对象的起始地址都对齐于8字
当封装对象的时候为了高效率
对象字段声明的顺序会被重排序成下列基于字节大小的顺序：

double (8字节) 和 long (8字节)
int (4字节) 和 float (4字节)
short (2字节) 和 char (2字节)：char在java中是2个字节。java采用unicode，2个字节（16位）来表示一个字符。
boolean (1字节) 和 byte (1字节)
reference引用 (4/8 字节)
```

**一个空对象，对象头12B占用(压缩)**

**内存对齐减少内存读取次数，花费更少的时钟周期完成**

##### 指针压缩

```
jdk1.6 update 144 开始指针压缩
jvm配置参数:UseCompressedOops，compressed--压缩、oop--对象指针
启用指针压缩:-XX:+UseCompressedOops，禁止指针压缩:-XX:-UseCompressedOops
```

```
在64位平台的HotSpot中使用32位指针，内存使用会多出1.5倍左右
使用较大指针在主内存和缓存之间移动数据，占用较大宽带，同时GC也会承受较大压力
为了减少64位平台下内存的消耗，启用指针压缩功能
在jvm中，32位地址表示4G个对象的指针，
在4G-32G堆内存范围内，可以通过编码、解码方式进行优化，使得jvm可以支持更大的内存配置
堆内存小于4G时，不需要启用指针压缩，jvm会直接去除高32位地址，即使用低虚拟地址空间
```

```
堆内存大于32G时，压缩指针会失效，会强制使用64位(即8字节)来对java对象寻址
这就会出现1的问题，所以堆内存不要大于32G为好
```

##### 指针压缩原理

```
1.解释器解释字节码，植入压缩指令，进行编码、解码
2.需要操作系统底层支持:GC堆从虚拟地址0开始分配
```

##### 压缩内容

```
在64位系统下
存放Class Pointer的空间大小是8字节，MarkWord是8字节，对象头为16字节;
64位开启指针压缩的情况下
存放Class Pointer的空间大小是4字节，MarkWord是8字节，对象头为12字节;
如果是数组对象
对象头的大小为：数组对象头8字节+数组长度4字节+对齐4字节=16字节
其中对象引用占4字节（未开启指针压缩的64位为8字节
数组MarkWord为4字节（64位未开启指针压缩的为8字节）;
静态属性不算在对象大小内
```



```
1.对象的全局静态变量(即类属性)
2.对象头信息:    64位平台下，原生对象头大小为16字节，压缩后为12字节
对象头大小=Class Pointer的空间大小为4字节+MarkWord为8字节=12字节；
3.对象的引用类型: 64位平台下，引用类型本身大小为8字节，压缩后为4字节
4.对象数组类型:   64位平台下，数组类型本身大小为24字节，压缩后16字节
```

##### 不压缩内容

```
1.指向非Heap的对象指针
2.局部变量、传参、返回值、NULL指针
```

```

指针压缩
 * 对象头 12B 数组+4 空数组+0
不压缩
 * 对象头16B 数组 8
```

![arr](../picture-md/arr.png)

```java
package com.aladdin.jvm;

import org.openjdk.jol.info.ClassLayout;

/**
 * 指针压缩
 * 对象头 12B 数组+4 空数组+0
 * 不压缩
 * 对象头16 数组 8
 *
 * @author lgc
 **/
public class ObjectSize {
    Long[] longs = new Long[8];
    long[] arr = new long[8];


    public static void main(String[] args) {
        ObjectSize objectSize = new ObjectSize();
        objectSize.longs = new Long[]{1l, 1l, 1l, 1l, 1l, 1l, 1l, 1l};
        objectSize.arr = new long[]{1l, 1l, 1l, 1l, 1l, 1l, 1l, 1l};
        //12+4(longs)+ 4(arr)+4(padding)
        System.out.println(ClassLayout.parseInstance(objectSize).toPrintable());
        System.out.println("包含非基本类型属性的对象大小");
        //12+4 int
        System.out.println(ClassLayout.parseInstance(new A()).toPrintable());
        System.out.println("包含非基本类型属性的对象大小");
        //12+4
        System.out.println(ClassLayout.parseInstance(new B()).toPrintable());
        System.out.println("基本类型数组对象的大小");
        //12+4+0+0
        System.out.println(ClassLayout.parseInstance(new int[0]).toPrintable());
        //12+4+4+4
        System.out.println(ClassLayout.parseInstance(new int[1]).toPrintable());
        System.out.println("非基本类型数组对象的大小");
        //16+0+0
        System.out.println(ClassLayout.parseInstance(new A[0]).toPrintable());
        //16+4+4
        System.out.println(ClassLayout.parseInstance(new A[1]).toPrintable());
        System.out.println(ClassLayout.parseInstance(new B[0]).toPrintable());
        System.out.println(ClassLayout.parseInstance(new B[1]).toPrintable());
    }
}

class A {
    //基本类型
    int a = 0;
}

class B {
    //引用类型
    Integer b = 0;
}
```

### 5.对象的访问定位

#### **句柄、直接指针**

##### 句柄

```
堆中可能划分出一块内存作为句柄池，reference 存储的就是对象的句柄地址
句柄包含了对象实例数据与类型数据各自具体的地址信息
对象被移动只会改变句柄中实例数据指针
```

##### 直接指针

```
reference 存储的直接就是对象地址
节省一次指针定位，速度更快
```

### 6.伪共享（false sharing）

**多线程修改独立变量在同一个缓存行时，会影响性能**

##### MESI（缓存行资源状态）

```
出现跨核访问资源的时候，要通过内存控制器，传输会占用总线带宽资源
```

```
M（修改，Modified）：本地处理器已经修改缓存行，即是脏行
它的内容与内存中的内容不一样，并且此 cache 只有本地一个拷贝(专有)；
E（专有，Exclusive）：缓存行内容和内存中的一样，而且其它处理器都没有这行数据；
S（共享，Shared）：缓存行内容和内存中的一样, 有可能其它处理器也存在此缓存行的拷贝；
I（无效，Invalid）：缓存行失效, 不能使用。
```

##### 状态转换

```
初始：一开始时，缓存行没有加载任何数据，所以它处于 I 状态。
```

```
本地写（Local Write）：如果本地处理器写数据至处于 I 状态的缓存行，则缓存行的状态变成 M。
```

```
本地读（Local Read）：如果本地处理器读取处于 I 状态的缓存行，很明显此缓存没有数据给它。
此时分两种情况：
(1)其它处理器的缓存里也没有此行数据，则从内存加载数据到此缓存行后，再将它设成 E 状态
	表示只有我一家有这条数据，其它处理器都没有
(2)其它处理器的缓存有此行数据，则将此缓存行的状态设为 S 状态
（备注：如果处于M状态的缓存行，再由本地处理器写入/读出，状态是不会改变的）
```

```
远程读（Remote Read）：
假设我们有两个处理器 c1 和 c2，如果 c2 需要读另外一个处理器 c1 的缓存行内容
c1 需要把它缓存行的内容通过内存控制器 (Memory Controller) 发送给 c2
c2 接到后将相应的缓存行状态设为 S
在设置之前，内存也得从总线上得到这份数据并保存。
```

```
远程写（Remote Write）：其实确切地说不是远程写，而是 c2 得到 c1 的数据后，不是为了读，而是为了写
也算是本地写，只是 c1 也拥有这份数据的拷贝，这该怎么办呢？
c2 将发出一个 RFO (Request For Owner) 请求，它需要拥有这行数据的权限，其它处理器的相应缓存行设为 I
除了它自已，谁不能动这行数据
这保证了数据的安全，同时处理 RFO 请求以及设置I的过程将给写操作带来很大的性能消耗。
```

##### RFO

```
1. 线程的工作从一个处理器移到另一个处理器, 它操作的所有缓存行都需要移到新的处理器上。
此后如果再写缓存行，则此缓存行在不同核上有多个拷贝，需要发送 RFO 请求了。
2. 两个不同的处理器确实都需要操作相同的缓存行
会发生 RFO
```

![false_sharing](../picture-md/false_sharing.png)

```
一个运行在处理器 core1上的线程想要更新变量 X 的值
同时另外一个运行在处理器 core2 上的线程想要更新变量 Y 的值
但是，这两个频繁改动的变量都处于同一条缓存行。
两个线程就会轮番发送 RFO 消息，占得此缓存行的拥有权。
当 core1 取得了拥有权开始更新 X，则 core2 对应的缓存行需要设为 I 状态。
当 core2 取得了拥有权开始更新 Y，则 core1 对应的缓存行需要设为 I 状态(失效态)。
```

```
轮番夺取拥有权不但带来大量的 RFO 消息，而且如果某个线程需要读此行数据时，L1 和 L2 缓存上都是失效数据，只有 L3 缓存上是同步好的数据。
读 L3 的数据非常影响性能。更坏的情况是跨槽读取，L3 都要 miss，只能从内存上加载。
表面上 X 和 Y 都是被独立线程操作的，而且两操作之间也没有任何关系。只不过它们共享了一个缓存行，但所有竞争冲突都是来源于共享。
```

## 自动内存管理

**哪些需要回收？**

**什么时候回收？**

**怎么回收？**

### 对象已死

```
程序计数器、虚拟机栈、本地方法栈3个区域随线程而生，随线程而灭
栈中的栈帧随着方法的进入和退出而有条不紊地执行着出栈和入栈操作
每一个栈帧中分配多少内存基本上是在类结构确定下来时就已知的
(尽管在运行期会由即时编译器进行一些优化，但在基于概念模型的讨论里，大体上可以认为是编译期可知的)
因此这几个区域的内存分配和回收都具备确定性， 在这几个区域内就不需要过多考虑如何回收的问题
当方法结束或者线程结束时，内存自然就跟随着 回收了。
而Java堆和方法区这两个区域则有着很显著的不确定性:
一个接口的多个实现类需要的内存可能 会不一样，一个方法所执行的不同条件分支所需要的内存也可能不一样
只有处于运行期间，才能知道程序究竟会创建哪些对象，创建多少个对象
这部分内存的分配和回收是动态的。垃圾收集器 所关注的正是这部分内存该如何管理
```

#### 引用计数算法

```
纯的引用计数 就很难解决对象之间相互循环引用的问题。会造成一堆垃圾
```

#### 可达性分析算法

```
通过 一系列称为“GC Roots”的根对象作为起始节点集
从这些节点开始，根据引用关系向下搜索，搜索过程所走过的路径称为“引用链”(Reference Chain)
如果某个对象到GC Roots间没有任何引用链相连
或者用图论的话来说就是从GC Roots到这个对象不可达时
则证明此对象是不可能再被使用的
```

固定可作为GC ROOTS的对象

```
1.在虚拟机栈(栈帧中的本地变量表)中引用的对象
譬如各个线程被调用的方法堆栈中使用到的 参数、局部变量、临时变量等
2.在方法区中类静态属性引用的对象，譬如Java类的引用类型静态变量
3.在方法区中常量引用的对象，譬如字符串常量池(String Table)里的引用
在本地方法栈中JNI(即通常所说的Native方法)引用的对象。 ·Java虚拟机内部的引用，如基本数据类型对应的Class对象，一些常驻的异常对象(比如
NullPointExcepiton、OutOfMemoryError)等，还有系统类加载器。 ·所有被同步锁(synchronized关键字)持有的对象。 ·反映Java虚拟机内部情况的JM XBean、JVM TI中注册的回调、本地代码缓存等。
```

```java
jvm stack
native method stack
runtime constant pool
static references in method area
clazz
```

### GC Algorithms

- #### mark swap

- #### copying

- #### mark compact

#### Mark Swap

```
标记清除
简单，碎片化，类似windows 磁盘清理，没有连续的空间
```

#### Copying

```
拷贝
没有碎片，效率高，浪费空间
转移有用的内存，整体清除 
```

#### Mark Compact

```
标记压缩
标记移动，效率低，无碎片
```

### Garbage Collectors

![garbage-collector](../picture-md/garbage-collectors.png)

#### Serial

```
a stop-the-world copying collector which uses a single GC thread
STW 在一个全局安全点 safe point 所有操作暂停，进行垃圾回收(单线程)
```

#### Serial Old

```
s stop-the-world 
mark-sweep-compoact collector that uses a single GC thread
```

**Serial、Serial Old 在j dk 1.0 1.2 使用内存小，内存过大效率低，容易卡死**

#### Parallel Scavenge

```
s stop-the-world copying collector which uses multiple GC threads
```

#### Parallel Old

```
a compacting collector that uses multiple GC threads
```

#### CMS

```
concurrent mark sweep 并发执行，工作线程和回收线程同时进行，低延迟
a mostly concurrent,low-pause collector
4 phases
1.initial mark     初始标记 从跟节点跟踪引用
2.concurrent mark  并发标记 标记垃圾 （耗时用并发提高效率）
3.remark           重新标记 可能标记的垃圾又被引用 占少数
4.concurrent sweep 并发清理
```

#### ParNew

```
Ps 变种 young 专门配合 CMS
a mostly concurrent,low-pause collector
copying colelctot which uses multiple GC threads
if differs from "Parallel Scavenge" in that it has echancements that make it usable with CMS
For example ,“ParNew" does the synchronization needed so that it can run during the concurrent phases of CMS
默认线程数为 CPU 的核数
```

**分布式锁续命遇到垃圾回收器锁失效，采用G1**

```
ParNew ,Serial ,Parallel Scavenge
CMS ,Serial Old ,Parallel Old 
左边六个上下搭配使用
分代模型
G1 物理不分代，逻辑分代
JDK8 PS PO
JDk9、10、11、13 G1
JDK11 可以使用 ZGC
```

![m_c](../picture-md/m_c.png)

```
新生代回收快，存活相对较少
采用复制算法，将 eden 复制到 survivor,清除 eden
下一次回收 复制到第二个 survivor 同时清理前面两个区域
当到达一定年龄(CMS 6,其他 15,最大是15，因为是4bit)复制到老年代 
年轻代和老年代默认是1:2 可以调整 
```

#### 对象出生到消亡

![o_b_d](../picture-md/o_b_d.png)

**进入老年代参数设置 -XX:MaxTenuringThreshold**

```
Xms Xmx 堆大小
Xmn 年轻代大小
MinorGC/YGC 年轻代空间耗尽触发
MajorGC/FullGC 老年代无法继续分配内存触发，新生代和老年代同时进行回收
```

![oo_b_d](../picture-md/oo_b_d.png)

```
能在栈上分配就在栈上分配
如果足够大直接分配在 old 区
不够大存在线程本地 TLAB(Thread Local Allocation Buffer)，多个线程往堆内放会存在争抢需要同步处理，耗性能，
使用 TLAB 提高效率
```

## 调优

**内存泄漏 内存不能被回收，漏了**

**内存溢出，内存被占满回收不了，崩溃**

## 四种引用类型

```java
强引用 默认 
软引用 内存不够回收 应用 缓存 										 SoftReference
弱引用 gc 就回收 					 											WeakReference
虚引用 管理堆外内存 os 直接内存 									 PhantomReference
引用队列 (搭配上面引用使用,虚引用必须搭配 引用队列)   ReferenceQueue
```

```
当垃圾回收器准备回收一个对象时，如果还有引用，会在回收对象之前，把这个引用加入到与之关联的引用队列中
可以通过监测 ReferenceQueue 做处理
```

### ThreadLocal

[深入理解](https://blog.csdn.net/cyxinda/article/details/93709143)

**引用： 当一个引用类型的变量内部数据存储是一块内存区域的起始地址，就认为这个变量是引用**

```
1.key 只能是 ThreadLocal 对象 实现的线程隔离，当 ThreadLocal 失去强引用,生命周期只能活到下一次 gc 
	这个时候 ThreadLocalMap 会出现 key 为 null 的 entry value 就会一直存在强引用链 造成内存泄漏
2.ThreadPool 从 pool 中取出 可能会被复用，要保证在上一次结束的时候，关联的 ThreadLocal 清空，否则将产生脏数据
```

**不使用要 remove 清除 entry 主要是清理value,否则 value就泄漏了 **

```java
public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
```

```java
static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
```

```java
private void set(ThreadLocal<?> key, Object value) {

            // We don't use a fast path as with get() because it is at
            // least as common to use set() to create new entries as
            // it is to replace existing ones, in which case, a fast
            // path would fail more often than not.

            Entry[] tab = table;
            int len = tab.length;
            int i = key.threadLocalHashCode & (len-1);

            for (Entry e = tab[i];
                 e != null;
                 e = tab[i = nextIndex(i, len)]) {
                ThreadLocal<?> k = e.get();

                if (k == key) {
                    e.value = value;
                    return;
                }

                if (k == null) {
                    replaceStaleEntry(key, value, i);
                    return;
                }
            }

            tab[i] = new Entry(key, value);
            int sz = ++size;
            if (!cleanSomeSlots(i, sz) && sz >= threshold)
                rehash();
        }

```

```java
private void remove(ThreadLocal<?> key) {
            Entry[] tab = table;
            int len = tab.length;
            int i = key.threadLocalHashCode & (len-1);
            for (Entry e = tab[i];
                 e != null;
                 e = tab[i = nextIndex(i, len)]) {
                if (e.get() == key) {
                    e.clear();
                    expungeStaleEntry(i);
                    return;
                }
            }
        }
```



### 为什么使用弱引用？

```
使用强引用 ThreadLocal =null 对象回收的时候，因为 key 是强引用 不释放 会造成 entry 泄漏 ThreadLocal 对象无法回收
使用弱引用 ThreadLcoal 对象的回收不会因为 Entry 对象里面的 key 影响
				 新的 ThreadLocal 对象执行 set 方法的时候， 遍历map 会有很多 Entry.get() key 为空，说明这个Entry 可以删掉 
				 replaceStaleEntry(key,value，i)替换旧的 entry
WeakReference 就是不会影响被应用对象的GC回收行为 (只要对象被除WeakReference 对象之外所有的对象解除引用后，该对象就可以被回收)
```



## OOM Heap 溢出，其他线程还能运行吗？

```
heap    oom
Oermgen oom
Unable to create new native thread 
```



## Arthas

