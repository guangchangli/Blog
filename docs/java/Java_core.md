# Java_core

## String

```java
多构造方法
public String(String original)
public String(String value[])
public String(StringBuffer buffer)
public String(StringBuilder builder)
```

### 字符串比较

```java
compareTo(String anothehrString) true
compareToIgnoreCase() 0
equals(Object obj)
```

### 主要方法

```
indexOf()
lastIndexOf
contains
toLowerCase
toUpperCase
length
trim
replace
split
join
```

### == equals

```
== 对于基本数据类型是比较值是否相等，对于引用类型是比较引用地址是否相同
Object 中 equals 方法就是 == , String 重写了是比较两个字符串是否相等

```

### final

```
String final 修饰在参数传递过程是不可变的，一方面是为了安全，一方面是为了效率避免了重新拷贝新值传递参数
不可变才能使用常量池
```

### StringBuffer StringBuilder

```
StringBuffer 线程安全
StringBuilder 线程不安全
```

### jvm

```
创建字符串的方式
1.new String()
2.直接赋值
3.intern
直接赋值会先去字符串常量池中查找是否已经有此值
	如果有，则把引用地址直接指向此值
	否则，会先在常量池中创建，然后再把引用指向此值
new String（）一定会先在堆上创建一个字符串对象，然后在去常量池中查询此字符串的值是否存在
	如果不存在会先在常量池中创建此字符串，然后把引用的值指向此字符串
intern
	先去查询常量池中是否已经存在
		如果存在，返回常量池中的引用
		找不到，1.7 之前复制一个副本放到常量池，1.7后将堆上的地址引用复制到常量池
```

```
 				String s1=new String("Java");//堆上引用 
        String s2=s1.intern();//复制堆上引用到常量池
        String s3="Java";//常量池
        System.out.println(s1==s2);//false
        System.out.println(s1==s3);//false
        System.out.println(s2==s3);//true
```

```
				String s4="abc";
        String s5="a";
        String s6="bc";
        String s7=s5+s6;//StringBuilder.append
        System.out.println(s4==s7);//false
```

```
				String s8 = "abc";
        final String s9 = "a";
        final String s10="bc";
        System.out.println(s8==s9+s10);//true
        final编译器优化，直接替换为值
```

```
 				String s11=new String("a")+new String("b");//引用堆中，常量池中有 a b
        s11.intern();//查找常量池 复制堆引用到常量池
        String s12="ab";
        System.out.println(s11==s11.intern());//true
        System.out.println(s11==s12);//true
        
        String s11=new String("a")+new String("b");//引用堆中，常量池中有 a b
        String s12="ab";
        System.out.println(s11==s11.intern());//false
        System.out.println(s11==s12);//false
```

## HashMap

```
初始化长度 1 << 16
最大长度   1 << 30
扩容因子   0.75 柏松分布平衡扩容和哈希冲突
链表长度大于 8  && 数组【桶】大于 64 链表转红黑树 
桶内元素小于 6 红黑树转换为链表
jdk1.8 在扩容的时候没有重新计算每个元素的 hash ，通过高位运算（e.hash & oldCap）来确定是否需要移动
高一位为1 时，表示元素在扩容时位置发生了变化,新的下标位置等于原下标位置+原数组长度
```

```
1.HashMap采用链地址法，低十六位和高十六位异或以及hashlength-1来减少hash冲突
2.在1.7采用头插入，1.8优化为尾插入，因为头插入容易产生环形链表死循环问题
3.在1.7扩容位置为hash 原容量判断，为0放低位，否则高位，低位位置不变，高位位置=原位置+原容量
4.树化和退树化8和6的选择，红黑树平均查找为logn，长度为8时，查找长度为3，而链表平均为8除以2；当为6时，查找长度一样，而树化需要时间；然后中间就一个数防止频繁转换
5.容量设置为2的n次方主要是可以用位运算实现取模运算，位运算采用内存操作，且能解决负数问题；同时hashlength-1时，length-1为奇数的二进制都为1，index的结果就等同与hashcode后几位，只要hashcode均匀，那么hash碰撞就少
6.负载因子0.75主要是太大容易造成hash冲突，太小浪费空间，0.75*2^* [>16] 正好是整数，柏松分布
```

```
1.7 hashmap
并发可能会形成闭环
tomcat会存在dos攻击风险，tomcat 修改了参数限制为10000【非法参数可能会使tomcat内部产生大量链表 O(n)】
```

### api

```
putIfAbsent
merge
compute
computeIfAbsent
replace

```



## LinkHashMap

```
 public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V> {
 }
 public LinkedHashMap(int initialCapacity,  float loadFactor, boolean accessOrder) {
     super(initialCapacity, loadFactor);
     this.accessOrder = accessOrder;
}
```

```
LinkedHashMap 继承 HashMap，双向链表实现，线程不安全 【插入顺序，访问顺序】
accessOrder 设置为 false，表示不是访问顺序而是插入的顺序进行排序的，默认false
表示 linkedhashMap 中存储的顺序是按照调用 put 方法插入的顺序进行排序的。
```

## ConcurrentHashMap

## Thread

### 状态

```
new 				新建状态
runable 		就绪状态，可以运行的线程状态，可能是正在运行或者在排队等待操作系统给他分配 cpu 资源
blocked 		阻塞等待锁的线程状态，表示处于阻塞状态的线程正在等待监视器锁（sync）
waiting 		等待状态，一个处于等待状态的线程正在等待另一个线程通知或者被中断
time_waited 计时等待状态，wait() join()，指定时间返回
terminnated 终止状态，线程执行完成
```

```
 1.sleep 运行 -> 阻塞 不释放资源
 2.yield 运行 -> 就绪 等待调度，只是暂时让出 cpu 
 3.join join(0) 阻塞当前线程
 
 sleep 是静态方法 thread 等待
 wait 是 object 方法 对象等待 没有参数的 需要被动唤醒
 join 是实例方法
 sleep join 都可以抛出异常 可被中断
 yield 静态方法 异常不可中断
```

### blocked & waiting

```
blocked 状态是活跃状态，只是在等待资源锁
waiting 是自身调用了 object.wait() 或者是 thread.join() 或者是 locksupport.park() 进入了等待状态，需要等待其他行为来中断
```

### start&run

```java
start() 属于 Thread 自身方法，并且使用 synchronzied 保证线程安全
public synchronized void start() {
    // 状态验证，不等于 NEW 的状态会抛出异常
    if (threadStatus != 0)
        throw new IllegalThreadStateException();
    // 通知线程组，此线程即将启动

    group.add(this);
    boolean started = false;
    try {
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {
            // 不处理任何异常，如果 start0 抛出异常，则它将被传递到调用堆栈上
        }
    }
}

```

```java
run() 方法是 Runnable 的抽象方法，必须由调用类重写此方法【也就是业务的方法、体】
public class Thread implements Runnable {
 // 忽略其他方法......
  private Runnable target;
  @Override
  public void run() {
      if (target != null) {
          target.run();
      }
  }
}
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}

```

```
1.从执行的效果来说，start() 方法可以开启多线程，让线程从 NEW 状态转换成 RUNNABLE 状态，而 run() 方法只是一个普通的方法。
2.可调用的次数不同，start() 方法不能被多次调用，否则会抛出 java.lang.IllegalStateException；而 run() 方法可以进行多次调用，	因为它只是一个普通的方法而已。
```

### 线程优先级

```java
// 线程可以拥有的最小优先级
public final static int MIN_PRIORITY = 1;
// 线程默认优先级
public final static int NORM_PRIORITY = 5;
// 线程可以拥有的最大优先级
public final static int MAX_PRIORITY = 10
 
线程的优先级可以理解为线程抢占 CPU 时间片的概率，优先级越高的线程优先执行的概率就越大，但并不能保证优先级高的线程一定先执行。
在程序中我们可以通过 Thread.setPriority() 来设置优先级

public final void setPriority(int newPriority) {
    ThreadGroup g;
    checkAccess();
    // 先验证优先级的合理性
    if (newPriority > MAX_PRIORITY || newPriority < MIN_PRIORITY) {
        throw new IllegalArgumentException();
    }
    if((g = getThreadGroup()) != null) {
        // 优先级如果超过线程组的最高优先级，则把优先级设置为线程组的最高优先级
        if (newPriority > g.getMaxPriority()) {
            newPriority = g.getMaxPriority();
        }
        setPriority0(priority = newPriority);
    }
}
```

### 常用方法

#### join()

***在一个线程中调用 other.join() ，这时候当前线程会让出执行权给 other 线程，直到 other 线程执行完或者过了超时时间之后再继续执行当前线程,所以只是暂时阻塞***

```java
public final synchronized void join(long millis)
throws InterruptedException {
    long base = System.currentTimeMillis();
    long now = 0;
    // 超时时间不能小于 0
    if (millis < 0) {
        throw new IllegalArgumentException("timeout value is negative");
    }
    // 等于 0 表示无限等待，直到线程执行完为之
    if (millis == 0) {
        // 判断子线程 (其他线程) 为活跃线程，则一直等待
        while (isAlive()) {
            wait(0);
        }
    } else {
        // 循环判断
        while (isAlive()) {
            long delay = millis - now;
            if (delay <= 0) {
                break;
            }
            wait(delay);
            now = System.currentTimeMillis() - base;
        }
    }
}

```

#### jield()

***native 方法，表示给线程调度器一个线程愿意让出 CPU 使用权的暗示，但是线程调度器可能会忽略这个暗示***

```java
public static void main(String[] args) throws InterruptedException {
    Runnable runnable = new Runnable() {
        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                System.out.println("线程：" +
                        Thread.currentThread().getName() + " I：" + i);
                if (i == 5) {
                    Thread.yield();
                }
            }
        }
    };
    Thread t1 = new Thread(runnable, "T1");
    Thread t2 = new Thread(runnable, "T2");
    t1.start();
    t2.start();
}
#每次结果不一定相同

```



## 对象

### 创建对象

```
new
Construct.newInstance()
Class.forName().newInstance()
clone
序列化
```



### 浅拷贝&深拷贝

```java
浅拷贝 拷贝对象地址
深拷贝 拷贝对象所有值
  深拷贝需要全部深拷贝（递归深拷贝）
clone() 是 object 的 navite 方法
Cloneable 接口只标识具有 clone 功能，需要重写 clone 方法
```

## 函数式编程

``1.不可变数据``

```
不依赖外部数据，不改变外部数据的值
```

``2.first class functions ``

``3.尾递归优化``

```
重用 stack ，提升性能
```

### pipeline

```

```

