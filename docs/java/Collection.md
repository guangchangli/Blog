# Collection

![iterator](/opt/blog/docs/picture-md/iterator.png)

## collection

```

```



## AutoCloseable

```java
interface AutoCloseable
void close() throws Exception;
/**An object that may hold resources (such as file or socket handles)
 * until it is closed. The {@link #close()} method of an {@code AutoCloseable}
 * object is called automatically when exiting a {@code
 * try}-with-resources block for which the object has been declared in
 * the resource specification header. This construction ensures prompt
 * release, avoiding resource exhaustion exceptions and errors that
 * may otherwise occur.
 */
```

## Iterable

```java
 Iterator<T> iterator();
 default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }
 default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
```

## Collection

```java
interface Collection<E> extends Iterable<E>
/**
 *The JDK does not provide any <i>direct</i>
 * implementations of this interface: it provides implementations of more
 * specific subinterfaces like <tt>Set</tt> and <tt>List</tt>
 */
```

### Api

```java
int size();
boolean isEmpty();
boolean contains(Object o);
Iterator<E> iterator();
Object[] toArray();
boolean add(E e);
boolean remove(Object o);
boolean containsAll(Collection<?> c);
boolean addAll(Collection<? extends E> c);
boolean removeAll(Collection<?> c);
default boolean removeIf(Predicate<? super E> filter) 
boolean retainAll(Collection<?> c);
void clear();
```

### List

```java
有序，第一个索引值是0  arrayList stack linkedList vector
arrayList 扩容 1.5
 /**
     * The maximum size of array to allocate.
     * Some VMs reserve some header words in an array.
     * Attempts to allocate larger arrays may result in
     * OutOfMemoryError: Requested array size exceeds VM limit
     */
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
```

### Set

```
不允许重复 hashSet (hashMap) treeSet (treeMap)
```

### Map

```

```



## Tranisent

**对象 <=> 二进制字节数组**

```
jdk 两种序列化
实现了 Serializable 接口是自动序列化的
实现 Externalizable 则需要手动序列化，通过 writeExternal 和 readExternal 方法手动进行
```

```
1）transient修饰的变量不能被序列化；

2）transient只作用于实现 Serializable 接口；

3）transient只能用来修饰普通成员变量字段；

4）不管有没有 transient 修饰，静态变量都不能被序列化；

5) 序列化对象里面的属性是对象的话也要实现序列化接口。

6) 类的对象序列化后，类的序列化ID不能轻易修改，不然反序列化会失败。

7) 类的对象序列化后，类的属性有增加或者删除不会影响序列化，只是值会丢失。

8) 如果父类序列化了，子类会继承父类的序列化，子类无需添加序列化接口。

9) 如果父类没有序列化，子类序列化了，子类中的属性能正常序列化，但父类的属性会丢失，不能序列化。

10) 用Java序列化的二进制字节数据只能由Java反序列化，不能被其他语言反序列化。如果要进行前后端或者不同语言之间的交互一般需要将对象转变成Json/Xml通用格式的数据，再恢复原来的对象。
```

**serialVersionUID 反序列化运行时校验，编译时会自动生成**

## Array

### 数组初始化

```
int[] arr = {1,2,3}
int[] arr = new int[0];
```

### api

```
Arrays.copyOf(oldArr,newArrLength);
			.copyOfRange(arr,start,end)
Arrays.sort(arr); 快排
      .binarySearch(type[] a,type a)
      .binarySearch(type[] a,int start,int end,type b);
Arrays.deepToString(arr)打印二维数组
```

## Stream

#### 操作

| 中间操作     | 无状态         | unordered()、filter()、map()、mapToInt()、flatmap()、peek()  |
| ------------ | -------------- | ------------------------------------------------------------ |
|              | **有状态 **    | **distinct()、sorted()、limit()、skip()**                    |
| **结束操作** | **非短路操作** | **foreach()、toArray()、reduce()、collect()、max()、min()、count()** |
|              | **短路操作 **  | **anyMatch()、allMatch()、noneMatch()、findFirst()、findAny()** |

```
Stream上的所有操作分为两类：中间操作和结束操作
中间操作只是一种标记，只有结束操作才会触发实际计算。
	中间操作又可以分为无状态的(Stateless)和有状态的(Stateful)
		无状态中间操作是指元素的处理不受前面元素的影响
		有状态的中间操作必须等到所有元素处理之后才知道最终结果，比如排序是有状态操作，在读取所有元素之前并不能确定排序结果
结束操作又可以分为短路操作和非短路操作
	短路操作是指不用处理全部元素就可以返回结果，比如找到第一个满足条件的元素。
	之所以要进行如此精细的划分，是因为底层对每一种情况的处理方式不同。
```

