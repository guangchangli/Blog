### Generic Type

1. #### 什么是泛型

   ```
   声明中含有一个或多个类型参数的类或接口就是泛型类或者泛型接口  好处 安全性检查，可见的描述性 针对编译器
   ```

2. #### 类型

   ```
   参数化类型
   每一种泛型定义一组参数化的类型 类｜接口 <实际类型参数>
   原生态类型
   不带有任何实际类型参数的泛型名称 List<E> List
   就像是类型声明中删除了类型信息一样，是为了兼容 jdk5 之前的类型
   ```

3. #### 避免使用原生态类型

   ```java
     static void testRawType() {
           List<String> strings = new ArrayList<>();
           unsafeAdd(strings);
           System.out.println(strings.get(0));//java.lang.Integer cannot be cast to java.lang.String
       }
   
       /**
        * 注: EffectiveGt.java使用了未经检查或不安全的操作。
        * 注: 有关详细信息, 请使用 -Xlint:unchecked 重新编译。
        * <p>
        * 警告: [unchecked] 对作为原始类型List的成员的add(E)的调用未经过检查
        * list.add("a");
        * ^
        * 其中, E是类型变量:
        * E扩展已在接口 List中声明的Object
        */
       //@SuppressWarnings("unchecked")
       static void unsafeAdd(List list) {
   //        list.add("a");
           list.add(Integer.valueOf(1));
       }
   ```

   ```
   原生态类型不安全 因为要兼容之前的类型 可以放进去所有类型的数据 破坏了类型的约束条件 
   使用通配符
   ```

   **不要使用原生态类型，但是在类文字中必须使用原生态类型，允许使用数组类型和基本数据类型**

   **LIst.Class  int.Class String[].class**

4. #### 尽可能的消除每一个非受检警告

   ```
   保证代码类型安全 尽可能的避免 Class Cast Exception
   ```

5. #### 消除警告

   ```
   @SuppressWarnings 可以使用在方法上，局部变量上 ，return 语句中(不知道怎么用) 不能使用在类上
   尽可能的缩小作用域，并且记录禁止的原因
   ```

   

   ```
   @SuppressWarnings(“unchecked”) 告诉编译器忽略 unchecked 警告信息，如使用List，ArrayList等未进行参数化产生的警告信息。
   
   @SuppressWarnings(“serial”) 如果编译器出现这样的警告信息：The serializable class WmailCalendar does notdeclare a static final serialVersionUID field of type long 使用这个注释将警告信息去掉。
   
   @SuppressWarnings(“deprecation”) 如果使用了使用@Deprecated注释的方法，编译器将出现警告信息。 使用这个注释将警告信息去掉。 · -
   
   @SuppressWarnings(“unchecked”, “deprecation”) 告诉编译器同时忽略unchecked和deprecation的警告信息。 · @SuppressWarnings(value={“unchecked”, “deprecation”}) 等同于@SuppressWarnings(“unchecked”, “deprecation”)
   deprecation ：使用了不赞成使用的类或方法时的警告
   
   
   unchecked ：执行了未检查的转换时的警告，例如当使用集合时没有用泛型 (Generics) 来指定集合保存的类型。
   
   fallthrough ：当 Switch 程序块直接通往下一种情况而没有 Break 时的警告。
   
   path ：在类路径、源文件路径等中有不存在的路径时的警告。
   
   serial ：当在可序列化的类上缺少 serialVersionUID 定义时的警告。
   
   finally ：任何 finally 子句不能正常完成时的警告。
   
   all : 关于以上所有情况的警告。
   ```

6. #### 列表优于数组

   ```
   数组是协变的 数组是具体化的 会在运行时知道和强化元素类型
   泛型是可变的 泛型是通过擦除实现的 只在编译器强化类型信息 运行时丢弃(擦除) 可以使泛型与没有使用泛型的代码随意互用，版本平滑过渡
   ```

   **所以没有泛型数组混用会创建数组错误**