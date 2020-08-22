# 锁

1. ### CAS

   ```
   1.开始
   2.读取当前值 E
   3.计算结果值 V
   4.比较 E 和当前新值 N（再次读取） 相等更新 V 不想等在读取更新
   5.ABA 其他线程修改后和原值相同 马士兵说你女朋友和你分手又回来了 。。。
   ```

   ```
   ABA问题  解决 version | booble
   AtmoicStamprReference
   ```

   ##### 本质实现

   ```
   lock cmpxchg 执行
   lock保证原子性  锁缓存 涉及到计算机的嗅探技术
   ```

   对象的内存布局  Object o =new Object() 占了多少字节 16	

   ```
   class pointer 压缩
   ```

2. ### 对象内存布局

   普通对象

   ```
   markword					对象头    8字节
   class pointer 	  类型指针  4字节
   instance data			实例数据
   padding						对齐
   ```

   数组

   ```
   markword					对象头
   class pointer 	  类型指针
   length(4字节)		   数组长度
   instance data			实例数据
   padding						对齐
   ```

3. ### Hotspot 锁的升级

   锁升级过程中 备份 markword 到线程栈 LR  记录之前的 hashcode

   ```
   无锁态
   锁状态(new) unused 25  hashcode(调用才有) 31 unused 1 分代年龄 4 偏向锁位 1 锁标志位 2
   ```

   ```
   偏向锁
   偏向锁 当前线程指针 54 Epoch2 unused 1 分代年龄 4  偏向锁位 1 锁标志位 2
   记录线程 id 号
   ```

   ```
   轻量级锁 自旋锁 无锁
   如果有任何线程竞争锁 升级为轻量级锁，撤销偏向锁 cas 修改标志指向线程栈中 Lock Record 的指针
   自旋十次
   ```

   ```
   重量级锁
   队列 等待队列 被 cpu 调度 不消耗 cpu 资源
   ```

4. #### 锁降级

   ```
   在 gc 时候触发，但是已经 gc 线程调用被回收了 没有人占用 降级也没意义
   ```

5. ### 锁消除 lock eliminate

   ```
   public void add(String str1,String str2){
   		new StringBuffer =new 
   }
   StrBuffer 被sync修饰
   sb 引用 只会在 add 方法引用 不可能被其他线程引用 
   ```

6. ### 锁粗化 lock coaesening

   ```
   
   ```

7. ### synchronized实现

   ```
   monitorenter moniterexit
   执行过程中自动升级
   lock comxchg
   ```

   

8. ### 逃逸分析

9. jvm jit just in time compiler

10. reentrantloc k

11. ### 锁升级过程中 markword hashcode 怎么存

12. ### volatile

    ```java
    public class HelloVolatile {
        volatile boolean running = true;
    
        void m() {
            System.out.println("m start ");
            while (running) {
            }
            System.out.println("m end");
        }
    
        @SneakyThrows
        public static void main(String[] args) {
            HelloVolatile t = new HelloVolatile();
            new Thread(t::m, "t1").start();
            TimeUnit.SECONDS.sleep(1);
            t.running = false;
        }
    }
    
    ```

13. ### volatile 原理 可见性 Cache line  内存块读取 嗅探技术 cpu 内存保证一致性（MESI）

    ```
    cpu 把每个 cache line 标记四种状态(MESI)
    modified exclusive shard invalid  
    MESI 能解决就用MESI 不行就锁总线(缓存)
    ```

    

    ```
    l1 l2 (单核内) l3(在 cpu 内多核心共享) 
    ```

14. ### 乱序执行 禁止指令重排

    ```
    指令耗时，执行后面的指令 指令不安顺序执行
    ```

15. ### volatile 解决指令重排 JVM内存屏障

    ```
    屏障两边的指令不可以重排，保证有序
    代码 标记 volatile
    对应 class ACC_VOLATILE 
    ```

16. ### 系统底层保证有序

    ```
    
    ```

17. ### JSR内存屏障

    ```
    LoadLoad 屏障 对于 Load1;LoadLoad;Load2 
    ```

    

18. ### 超线程

    ```
    一个 ALU 对应多个 PC
    ```

19. ### disruptor 

20. ### LinkedTransferQueue

21. ### Happen-before