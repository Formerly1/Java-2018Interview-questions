# java面试题

## 1.hashcode相等的两个对象一定相等吗?equals呢?反过来相等吗？

_考点：Java基础_

### 解答：

​	hashcode相等的两个对象一定相等，hashcode是由特定的hash函数通过算法生成，通过jdk api对hashcode的解释：对同一对象多次调用，返回的hashcode应该相同。

​	如果x.equals(y)==true，那么两个对象不一定相等，Object类对equals()方法的默认实现认为如果x.equals(y)==true，那么x==y，但是，如果某个类override此方法，也可以实现两个对象不相等，但是两个对象的值相等，也判定x.equals(y)==true。

​	反过来的情况：1.两个对象相等hashcode一定相等吗？如果根据相同的hash算法那一定相同。

	两个对象相等，那x.equals(y)结果是？true。

## 2.hashmap和treemap的区别?底层数据结构都是什么?

### 区别：

（1）底层结构：hashmap底层数据结构是：hash table；treemap底层数据结构是：红黑树

（2）是否对key进行排序：hashmap不对key进行排序，treemap对key进行排序

## 3.hashtable,hashmap底层实现是什么?hashtable和concurrenthashmap底层实现的区别?

## 4.请用至少四种写法?写单例模式的实现.

以下是【[维基百科](https://en.wikipedia.org/wiki/Singleton_pattern)和[博客](https://www.cnblogs.com/zhaoyan001/p/6365064.html)】搬运过来的：

单例模式解决如下问题：

* 如何确保一个类只有一个实例？
* 如何简单地访问类的唯一实例？
* 一个类如何控制它的实例化？
* 如何限制一个类的实例个数？

设计思路：

* 关键思路是让类自身去控制它的实例化，将构造函数设置为private，防止外部直接实例化
* 用静态函数类似static getInstance()来返回类的实例

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

## 5.介绍一下集合框架?

_集合框架要完整介绍起来内容很多，此处只做重要的介绍。_

### 集合框架的根接口Collection和Map

#### Collection接口：

主要的子接口：List，Set，Queue

（1）List：该接口是有序集合，且里面元素可以重复，元素都有索引，继承该接口的类主要有：LinkedList, ArrayList, Vector, Stack，不同的类有不同的作用。

* LinkedList：底层数据结构是双向链表， _**允许插入任何值，包括null；非线程安全；迭代器是快速失败的**_ ，也就是说如果在迭代的过程中，不是通过迭代器改变数据结构（包括删除，增加元素等），那么会抛出异常，如果通过迭代器改变数据结构（例如在调用迭代器的next()方法后，再调用remove()方法删除迭代器返回的最后一个元素）则不会失败。
* ArrayList：底层数据结构是数组，可以自动动态调整数组大小； _**允许插入任何值，包括null；非线程安全；迭代器是快速失败的**_ ；几乎和Vector一样
* Vector：底层数据结构是数组，可以自动动态调整数组大小； _**允许插入任何值，包括null**；**线程安全；迭代器是快速失败的**_
* Stack：堆栈类，实现了后进先出（LIFO），提供了一般的堆栈执行操作，例如push,pop,peek等，但是应该 _**优先使用Deque类**_ ，该类提供更完整的功能。

（2）Set：该接口的元素 _**不允许重复**_ ，Set的主要实现类有HastSet和TreeSet。HashSet依赖于HashMap，它实际上是通过HashMap实现的；TreeSet依赖于TreeMap，它实际上是通过TreeMap实现的

* HastSet： _**无序，允许null元素**_ ，底层是hash表实现，迭代所需时间复杂度由HashSet的实例数量和HashMap的实例容量相加决定。如果需要迭代时的高性能，不能把初始容量设置太大（或者负载因子设置太小）。
* TreeSet： _**元素依照他们的自然顺序进行排序**_ ，或者由在设置创建时提供的Comparator进行排序，这取决于使用哪个构造函数，元素的基本操作（增加，删除，包括等）提供log(n)的时间复杂度。 _**非线程安全，迭代器快速失败**_ 。

（3）Queue：该接口 _**不支持阻塞**_ ，BlockingQueue扩展了该接口可以实现阻塞； _**不允许null元素插入**_

#### Map接口：

键值对的集合类， _**不能包含重复的键**_ ，每个键对应至多一个值。AbstractMap是个抽象类，它实现了Map接口中的大部分API。而HashMap，TreeMap，WeakHashMap都是继承于AbstractMap。Hashtable虽然继承于Dictionary，但它实现了Map接口

* HashMap：基于 _**哈希表实现**_ ； _**允许null键和null值**_ ；HashMap类大致等同于HashTable，但是它是 _**非线程安全**；**无序**_ ；一般负载因子为0.75；
* TreeMap：基于 _**红黑树实现**；**有序**_ （根据自然顺序排序，或者由创建时实现的Comparator排序，取决于具体构造函数）；一般的常规操作例如get,put,remove等都提供 _**log(n)的时间复杂度**；**非线程安全**_
* WeakHashMap：带有弱键，当key不再使用时，key对应的值也会被回收♻️； _**允许null键和null值**；**非线程安全**_
* Hashtable： _**不允许null键或者null值**；**线程安全**_ ；如果需要高并发性能，则建议使用ConcurrentHashMap而非Hashtable


## 线上频繁发生full gc如何处理?CPU使用率过高怎么办?说出你的思路和处理方法?
full gc原因：
1.系统调用了System.gc()方法，会建议系统执行full gc方法，但是不是一定会执行
2.内存泄露导致，例如资源未释放，会导致访问量大的时候，大多数对象进入old代，old代满了后会发生频繁gc
解决：
jmap -dump把java堆文件dump下来存储
查看dump文件


## 讲讲类加载机制?都有哪些类加载器?这些类加载器都加载哪些类文件?说说你在项目中用到类加载器的例子?
类加载机制：
加载顺序：
1.加载 
2.验证：验证加载后的字节码是否符合jvm规范
3.准备：为类变量（静态变量）分配内存空间和赋初始值，不是程序中赋的初始值，例如：
```java
public static int v = 8080;
```
在此阶段只会初始化为0
4.解析：将常量池里面的符号引用转换为直接引用
5.初始化：为类变量真正赋值，例如上面的v赋值为8080
6.使用 7.卸载

类加载器有3种：
1.启动类加载器：加载位于JAVA_HOME/lib文件夹里的类库
2.扩展类加载器：加载位于JAVA_HOME_lib_ext文件夹里面的类库
3.应用程序类加载器：负责加载用户路径上的类库


## hashtable,hashmap底层实现是什么?hashtable和concurrenthashmap底层实现的区别?
HashTable底层数据结构是Entry<K,V>类，该类是Map.Entry<K,V>接口的实现类，Entry<K,V>类的底层数据结构类似链表，数据Filed包括，hash值，key，value
Put方法：由源码可用看出put分几部分：由源码看出 _**HashTable是线程安全**_ 的
1.根据key计算hash值，hash值通过计算后得到了key对于的value在table中的index
2.在对应的table[index]中遍历Entry，查看是否有相同的key
3.如果有，则替换旧值为新值，返回旧值
4.如果没有，添加key,value到Entry中
```java
public synchronized V put(K key, V value) {
    // Make sure the value is not null
    if (value == null) {
        throw new NullPointerException();
    }

    // Makes sure the key is not already in the hashtable.
    Entry<?,?> tab[] = table;
    int hash = key.hashCode();
    int index = (hash & 0x7FFFFFFF) % tab.length;
    @SuppressWarnings("unchecked")
    Entry<K,V> entry = (Entry<K,V>)tab[index];
    for(; entry != null ; entry = entry.next) {
        if ((entry.hash == hash) && entry.key.equals(key)) {
            V old = entry.value;
            entry.value = value;
            return old;
        }
    }

    addEntry(hash, key, value, index);
    return null;
}

```

这个方法是新增节点方法，分为几部分
1.如果count大于了threshold(初始容量*负载因子），则rehash，并且重新计算hash值和index
2.让tab[index]重新指向一个新的Entry，而该Entry的next指向之前旧的Entry，也就是说将新的Entry成为tab[index]存储的链表的头节点
```java
private void addEntry(int hash, K key, V value, int index) {
	  //HashTable结构变化的次数，例如增加节点，或者rehash()
    modCount++;

    Entry<?,?> tab[] = table;
    if (count >= threshold) {
        // Rehash the table if the threshold is exceeded
        rehash();

        tab = table;
        hash = key.hashCode();
        index = (hash & 0x7FFFFFFF) % tab.length;
    }

    // Creates the new entry.
    @SuppressWarnings("unchecked")
    Entry<K,V> e = (Entry<K,V>) tab[index];
    tab[index] = new Entry<>(hash, key, value, e);
    count++;
}
```

get方法：
1.根据key计算出hash值，index
2.通过index找到Entry，然后通过next方法遍历，找到hash值和key都相同的Entry，返回value
3.没找到返回null
```java
public synchronized V get(Object key) {
    Entry<?,?> tab[] = table;
    int hash = key.hashCode();
    int index = (hash & 0x7FFFFFFF) % tab.length;
    for (Entry<?,?> e = tab[index] ; e != null ; e = e.next) {
        if ((e.hash == hash) && e.key.equals(key)) {
            return (V)e.value;
        }
    }
    return null;
}
```

HashMap底层数据结构是Node类，但是也是实现了Map.Entry接口
其实HashMap的put方法和HashTable类似，区别是该方法是非线程安全
```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    //如果table为空，则resize初始化table
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    //计算出index，如果index对应的Node是null，则直接新建一个Node
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        //如果index对应的Node的hash,key与put的key相同则e被赋值为index对应的Node：p
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
		  //如果p是TreeNode的子类，则用TreeNode版本的putTreeVal来处理节点
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            //遍历p所在的链表
            for (int binCount = 0; ; ++binCount) {
                //如果p没有下一个节点，则将当前的key,value新建一个节点作为p的下一个节点
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                //否则，如果找到相同节点，则循环结束
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                //没找到相同节点，继续遍历
                p = e;
            }
        }
        //如果有相同的key对于的Node，则替换旧值，返回旧值
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    //否则，没找到相同的key，新建了一个Node，则增加modCount，和一些其他操作
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}

```

Get方法：和HashTable也是类似，区别是该方法是非线程安全
```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
     
        if ((e = first.next) != null) {
            //如果index对应的first节点是TreeNode子类，则调用TreeNode对应的getTreeNode处理
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                //从first开始遍历，如果找到就返回节点
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}

```

HashMap和ConcurrentHashMap底层实现的区别：
1.在新建节点上，后者是线程同步的

首先看一下ConcurrentHashMap的put方法，其实实现思想与HashMap差不多，而且底层数据结构也是Node类
```java
public V put(K key, V value) {
    return putVal(key, value, false);
}

/** Implementation for put and putIfAbsent */
final V putVal(K key, V value, boolean onlyIfAbsent) {
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode());
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, I, fh;
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        //如果index对应的Node为null，则直接新建一个节点
        else if ((f = tabAt(tab, I = (n - 1) & hash)) == null) {
            //通过锁机制实现新节点的建立，保证了线程安全
            if (casTabAt(tab, I, null,
                         new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else {
            V oldVal = null;
            //对需要操作的Node进行同步
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    if (fh >= 0) {
                        binCount = 1;
                        //遍历，找到key相同的则替换旧值，否则新建一个节点
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    //同样如果f属于TreeBin或其子类，交给子类处理
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                       value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    addCount(1L, binCount);
    return null;
}
```


## synchronize 和Lock接口的区别是什么?synchronize什么情况下是对象锁?什么情况下是全局锁?为什么?
Lock接口：
该接口提供了比synchronize更广泛的锁操作🔒
一般一个锁同一时刻只能有一个线程获得，但是有些锁例外，例如读锁

用synchronize方法提供了对每个对象的隐式监控，但是要求所有锁的获得与释放必须在一个块状结构中：而且所有锁🔒的获得和释放要以相反的顺序进行，并且所有锁的释放都必须在他们获得锁的范围内进行

Lock接口提供了优于synchronize的方法：例如尝试用非阻塞模式获得锁（tryLock()方法)，可以获得可中断的锁（lockInterruptibly()），也包括可以获得带超时的锁（tryLock(long, TimeUnit)）

该接口还提供了比隐式监控更有保证的有序，不可重入锁，或者死锁监测等

当synchronize作用于一个非static对象（包括this）或者非static方法时，则是对象锁，对象锁只能保证同一个实例实现同步，但是如果类是单例，作用也和全局锁一样

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Main {

    private final List<Integer> nonStaticList = new ArrayList<>();

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(5);
        for (int I = 0; I < 10; I++) {
            executorService.execute(() -> {
                Main main = new Main();
                main.testNonStaticObject();
            });
        }
    }

    //对象锁，只能保证同一个实例同步
    public synchronized void testNonStaticMethodSync(){
        try {
            Thread.sleep(1500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "执行，时间：" + System.currentTimeMillis());
    }

    //非static的对象只能实现实例同步，不同的实例不能实现同步
    public void testNonStaticObject(){
        synchronized (nonStaticList){
            try {
                System.out.println(Thread.currentThread().getName() + "开始执行，时间：" + System.currentTimeMillis());
                Thread.sleep(1500);
                nonStaticList.add(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "结束执行，时间：" + System.currentTimeMillis());
        }
    }

    //this相当于锁定该实例对象，也是对象锁
    public void testObjectThis(){
        synchronized (this){
            try {
                Thread.sleep(1500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "执行，时间：" + System.currentTimeMillis());
        }
    }

}
```
全局锁：多个实例访问的时候可以实现同步，真正的同步
全局锁主要是三个方面：1.static方法上添加synchronize关键字，2.synchronize作用与static的filed，3.synchronize作用与类的class

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Main {

    private static final List<Integer> staticList = new ArrayList<>();

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(5);
        for (int I = 0; I < 10; I++) {
            executorService.execute(() -> {
                Main main = new Main();
                main.testStaticObject();
            });
        }
    }

    //类锁，多个实例也能实现同步
    public static synchronized void testStaticMethodSync(){
        try {
            Thread.sleep(1500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "执行，时间：" + System.currentTimeMillis());
    }

    //static对象可以实现类同步，不同实例之间能实现同步
    public void testStaticObject(){
        synchronized (staticList){
            try {
                System.out.println(Thread.currentThread().getName() + "开始执行，时间：" + System.currentTimeMillis());
                Thread.sleep(1500);
                staticList.add(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "结束执行，时间：" + System.currentTimeMillis());
        }
    }

    //类锁，锁住整个类，不同的实例访问也能实现同步
    public void testClassSync(){
        synchronized (Main.class){
            try {
                Thread.sleep(1500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "执行，时间：" + System.currentTimeMillis());
        }
    }


}
```






