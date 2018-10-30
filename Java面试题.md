**1.hashcode相等的两个对象一定相等吗?equals呢?反过来相等吗？**

考点：Java基础

解答：

​	hashcode相等的两个对象一定相等，hashcode是由特定的hash函数通过算法生成，通过jdk api对hashcode的解释：对同一对象多次调用，返回的hashcode应该相同。

​	如果x.equals(y)==true，那么两个对象不一定相等，Object类对equals()方法的默认实现认为如果x.equals(y)==true，那么x==y，但是，如果某个类override此方法，也可以实现两个对象不相等，但是两个对象的值相等，也判定x.equals(y)==true。

​	反过来的情况：1.两个对象相等hashcode一定相等吗？如果根据相同的hash算法那一定相同。

​				2.两个对象相等，那x.equals(y)结果是？true。

**2.hashmap和treemap的区别?底层数据结构都是什么?**

区别：

（1）底层结构：hashmap底层数据结构是：hash table；treemap底层数据结构是：红黑树

（2）是否对key进行排序：hashmap不对key进行排序，treemap对key进行排序

3.hashtable,hashmap底层实现是什么?hashtable和concurrenthashmap底层实现的区别?

**4.请用至少四种写法?写单例模式的实现.**

以下是【[维基百科](https://en.wikipedia.org/wiki/Singleton_pattern)和[博客](https://www.cnblogs.com/zhaoyan001/p/6365064.html)】搬运过来的：

单例模式解决如下问题：

- 如何确保一个类只有一个实例？
- 如何简单地访问类的唯一实例？
- 一个类如何控制它的实例化？
- 如何限制一个类的实例个数？

设计思路：

- 关键思路是让类自身去控制它的实例化，将构造函数设置为private，防止外部直接实例化
- 用静态函数类似static getInstance()来返回类的实例

实现：

（1）饿汉式：

```java

public class Singleton {
    private final static Singleton INSTANCE = new Singleton();

    private Singleton(){}

    public static Singleton getInstance(){
        return INSTANCE;
    }
}

//通过静态代码快加在类实例
public class Singleton {

    private static Singleton instance;

    static {
        instance = new Singleton();
    }

    private Singleton() {}

    public static Singleton getInstance() {
        return instance;
    }
}
```
（2）懒汉式（双重检查）

```java
public class Singleton {

    private static volatile Singleton singleton;

    private Singleton() {}

    public static Singleton getInstance() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```

（3）静态内部类

```java
public class Singleton {

    private Singleton() {}

    private static class SingletonInstance {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonInstance.INSTANCE;
    }
}
```

这种方式只会在第一次调用getInstance()时候才实例化一个Singleton，其他时候直接返回该实例

（4）枚举

```java
public enum Singleton {
    INSTANCE;
}
```

枚举用起来非常简单易用，且线程安全。

**5.介绍一下集合框架?**

集合框架要完整介绍起来内容很多，此处只做重要的介绍。

1.集合框架的根接口Collection和Map

==**Collection接口：**==

主要的子接口：List，Set，Queue

（1）List：该接口是有序集合，且里面元素可以重复，元素都有索引，继承该接口的类主要有：LinkedList, ArrayList, Vector, Stack，不同的类有不同的作用。

- LinkedList：底层数据结构是双向链表，**允许插入任何值，包括null；非线程安全；迭代器是快速失败的**，也就是说如果在迭代的过程中，不是通过迭代器改变数据结构（包括删除，增加元素等），那么会抛出异常，如果通过迭代器改变数据结构（例如在调用迭代器的next()方法后，再调用remove()方法删除迭代器返回的最后一个元素）则不会失败。
- ArrayList：底层数据结构是数组，可以自动动态调整数组大小；**允许插入任何值，包括null；非线程安全；迭代器是快速失败的**；几乎和Vector一样
- Vector：底层数据结构是数组，可以自动动态调整数组大小；**允许插入任何值，包括null**；**线程安全；迭代器是快速失败的**
- Stack：堆栈类，实现了后进先出（LIFO），提供了一般的堆栈执行操作，例如push,pop,peek等，但是应该**优先使用Deque类**，该类提供更完整的功能。

（2）Set：该接口的元素**不允许重复**，Set的主要实现类有HastSet和TreeSet。HashSet依赖于HashMap，它实际上是通过HashMap实现的；TreeSet依赖于TreeMap，它实际上是通过TreeMap实现的

- HastSet：**无序，允许null元素**，底层是hash表实现，迭代所需时间复杂度由HashSet的实例数量和HashMap的实例容量相加决定。如果需要迭代时的高性能，不能把初始容量设置太大（或者负载因子设置太小）。
- TreeSet：**元素依照他们的自然顺序进行排序**，或者由在设置创建时提供的Comparator进行排序，这取决于使用哪个构造函数，元素的基本操作（增加，删除，包括等）提供log(n)的时间复杂度。**非线程安全，迭代器快速失败**。

（3）Queue：该接口**不支持阻塞**，BlockingQueue扩展了该接口可以实现阻塞；**不允许null元素插入**

==**Map接口**：==

键值对的集合类，**不能包含重复的键**，每个键对应至多一个值。AbstractMap是个抽象类，它实现了Map接口中的大部分API。而HashMap，TreeMap，WeakHashMap都是继承于AbstractMap。Hashtable虽然继承于Dictionary，但它实现了Map接口

- HashMap：基于**哈希表实现**；**允许null键和null值**；HashMap类大致等同于HashTable，但是它是**非线程安全**；**无序**；一般负载因子为0.75；
- TreeMap：基于**红黑树实现**；**有序**（根据自然顺序排序，或者由创建时实现的Comparator排序，取决于具体构造函数）；一般的常规操作例如get,put,remove等都提供**log(n)的时间复杂度**；**非线程安全**
- WeakHashMap：带有弱键，当key不再使用时，key对应的值也会被回收♻️；**允许null键和null值**；**非线程安全**
- Hashtable：**不允许null键或者null值**；**线程安全**；如果需要高并发性能，则建议使用ConcurrentHashMap而非Hashtable