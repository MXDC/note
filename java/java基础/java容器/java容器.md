---
categories:
  - java
  - java基础
  - java容器
---
#### Map

* get方法调用getNode()，如果返回节点为`null`则返回`null`，否则返回节点的值`e.value` :notes:getOrDefault

```java
public V get(Object key) {    
    Node<K,V> e;    
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
```

* 键必须是唯一的。不能对同一个键存放两个值。如果对同一个键两次调用put方法，第二个值就会取代第一个值。
* put将返回用这个键参数存储的上一个值



#### HashMap源码

HashMap采用哈希算法实现，是Map接口最常用的实现类。 底层采用了哈希表存储数据。

##### HashMap主要成员变量

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
	/**
	* This map usually acts as a binned (bucketed) hash table, but
    * when bins get too large, they are transformed into bins of
    * TreeNodes, each structured similarly to those in
    * java.util.TreeMap.
	*/
	
	/**
     * The default initial capacity - MUST be a power of two.
     * 核心数组默认初始大小为16（必须是2的整数幂）
     */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
    /** ... */
    /**
     * The load factor used when none specified in constructor.
     * 负载因子（核心数组被占用超过0.75启动扩容(大约两倍扩容)）
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    /**
     *The table, initialized on first use, and resized as
     * necessary.
     */
    transient Node<K,V>[] table;
    
    /**
     * The next size value at which to resize (capacity * load factor).
     */
     int threshold;
}
```

```java
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;
    	/** ... **/
}
```



> 数组结构的每一个格称为**桶**(bucket)。capacity即为桶的数量(The <i>capacity</i> is the number of buckets in the hash table)。size为键值对(每一個鍵值對也叫做*Entry*)的个数。
>
> 变量集合的时间是和桶的数量和键值对的个数是成比例的。如果初始capacity太大会影响迭代效率(Iteration over collection views requires time proportional to the "capacity" of the HashMap instance (the number of buckets) plus its size (the number of key-value mappings). Thus, it's very important not to set the initial capacity too high (or the load factor too low) if iteration performance is important.)

> 链接：https://www.nowcoder.com/questionTerminal/95e4f9fa513c4ef5bd6344cc3819d3f7来源：牛客网
>
> ​        在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出Concurrent Modification Exception。 
>
> ​         原理：迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个 modCount 变量。集合在被遍历期间如果内容发生变化，就会改变modCount的值。每当迭代器使用hashNext()/next()遍历下一个元素之前，都会检测modCount变量是否为expectedmodCount值，是的话就返回遍历；否则抛出异常，终止遍历。

##### **HashMap重要方法深度解析**

######  **1、构造方法**

```java
HashMap()    //无参构造方法，threshold为0
HashMap(int initialCapacity)  //指定初始容量的构造方法 会调用HashMap(initialCapacity, DEFAULT_LOAD_FACTOR)
HashMap(int initialCapacity, float loadFactor) //指定初始容量和负载因子
HashMap(Map<? extends K,? extends V> m)  //指定集合，转化为HashMap
```

HashMap提供了四个构造方法，构造方法中 ，依靠第三个方法来执行的，**但是前三个方法都没有进行数组的初始化操作，即使调用了构造方法此时存放HaspMap中数组元素的table表长度依旧为0** 。在第四个构造方法中调用了inflateTable()方法完成了table的初始化操作，并将m中的元素添加到HashMap中。

* HashMap#tableSizeFor()
  源码：

```java
static final int MAXIMUM_CAPACITY = 1 << 30;
/**
 * Returns a power of two size for the given target capacity.
 */
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
这个方法被调用的地方：
public HashMap(int initialCapacity, float loadFactor) {
    /**省略此处代码**/
    /** initialCapacity 不能<0, loadFactor 不能<=0 ,否则抛出异常**/
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
}
```
由此可以看到，当在实例化HashMap实例时，如果给定了initialCapacity，**由于HashMap的capacity都是2的幂，因此这个方法用于找到大于等于initialCapacity的最小的2的幂**（initialCapacity如果就是2的幂，则返回的还是这个数）。

下面分析这个算法：
首先，为什么要对cap做减1操作。int n = cap - 1;
这是为了防止，cap已经是2的幂。如果cap已经是2的幂， 又没有执行这个减1操作，则执行完后面的几条无符号右移操作之后，返回时再加一,capacity将是这个cap的2倍。如果不懂，要看完后面的几个无符号右移之后再回来看看。
下面看看这几个无符号右移操作：
如果n这时为0了（经过了cap-1之后），则经过后面的几次无符号右移依然是0，最后返回的capacity是1（最后有个n+1的操作）。
这里只讨论n不等于0的情况。
第一次右移

`n |= n >>> 1;`

由于n不等于0，则n的二进制表示中总会有一bit为1，这时考虑最高位的1。通过无符号右移1位，则将最高位的1右移了1位，再做或操作，使得n的二进制表示中与最高位的1紧邻的右边一位也为1，如000011xxxxxx。
第二次右移

`n |= n >>> 2;`

注意，这个n已经经过了n |= n >>> 1; 操作。假设此时n为000011xxxxxx ，则n无符号右移两位，会将最高位两个连续的1右移两位，然后再与原来的n做或操作，这样n的二进制表示的高位中会有4个连续的1。如00001111xxxxxx 。
第三次右移

`n |= n >>> 4;`
这次把已经有的高位中的连续的4个1，右移4位，再做或操作，这样n的二进制表示的高位中会有8个连续的1。如00001111 1111xxxxxx 。
以此类推
注意，容量最大也就是32bit的正数，因此最后n |= n >>> 16; ，最多也就32个1，但是这时已经大于了MAXIMUM_CAPACITY ，所以取值到MAXIMUM_CAPACITY 。
![1569166420999.jpg](https://raw.githubusercontent.com/MXDC/images_bed/master/img/1569166420999.jpg)

当n=0时，右移后还是0，但n=-1时，右移后还是-1(补码为1......111)

注意，得到的这个capacity却被赋值给了threshold。

`this.threshold = tableSizeFor(initialCapacity);`

开始以为这个是个Bug，感觉应该这么写：

`this.threshold = tableSizeFor(initialCapacity) * this.loadFactor;`
这样才符合threshold的意思（当HashMap的size到达threshold这个阈值时会扩容）。
[但是，请注意，在构造方法中，并没有对table这个成员变量进行初始化，table的初始化被推迟到了put方法中，在put方法中会对threshold重新计算，]()

> 版权声明：本文为CSDN博主「fan2012huan」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/fan2012huan/article/details/51097331

###### **2、添加方法**

- 当调用`put()`时，如下方式设定了初始容量。
- 调用 `putVal(hash(key), key, value, false, true)`

```java
public V put(K key, V value) {    return putVal(hash(key), key, value, false, true);}
```

* hash(key)

  ```java
      static final int hash(Object key) {
          int h;
          // key为null,在核心数组的第一个位置
          // h = key.hashCode()去hashCode
          // h >>> 16
          return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
      }
  ```

  

* 在putVal()中，进入第一个if，调用resize(),返回核心数组

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
    /** .... **/
}
```

* resize()

   HashMap扩容可以分为三种情况：

  第一种：使用默认构造方法初始化HashMap。从前文可以知道HashMap在一开始初始化的时候会返回一个空的table，并且thershold为0。因此第一次扩容的容量为默认值DEFAULT_INITIAL_CAPACITY也就是16。同时threshold = DEFAULT_INITIAL_CAPACITY * DEFAULT_LOAD_FACTOR = 12。

  第二种：指定初始容量的构造方法初始化HashMap。那么从下面源码可以看到初始容量会等于threshold，接着threshold = 当前的容量（threshold） * DEFAULT_LOAD_FACTOR。

  第三种：HashMap不是第一次扩容。如果HashMap已经扩容过的话，那么每次table的容量以及threshold量为原有的两倍。

  > 版权声明：本文为CSDN博主「青元子」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
  > 原文链接：https://blog.csdn.net/u010890358/article/details/80496144

```java
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold; //带参数构造函数为tableSizeFor(initialCapacity)，无参数为0
        int newCap, newThr = 0;
        if (oldCap > 0) {//已经初始化核心数组
            // 超过最大容量
            // 原数组长度大于最大容量(1073741824) 则将threshold设Integer.MAX_VALUE=2147483647
			// 接近MAXIMUM_CAPACITY的两倍
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            // 没有超过最大容量
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
    	// 核心数组还没初始化，带参构造函数，threshold属性已经被赋值
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        //  核心数组还没初始化，无参构造函数，threshold属性还未被赋值
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        // 当newThr为0时，
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            // //在newCap 与ft 小于最大容量时(不满足时不讨论)，执行  newThr  = ft
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
         /** ... **/
        return newTab;
    }

```



#### 常被问到的HashMap和Hashtable的区别**

##### **1、线程安全**

两者最主要的区别在于Hashtable是线程安全，而HashMap则非线程安全。

Hashtable的实现方法里面都添加了synchronized关键字来确保线程同步，因此相对而言HashMap性能会高一些，我们平时使用时若无特殊需求建议使用HashMap，在多线程环境下若使用HashMap需要使用Collections.synchronizedMap()方法来获取一个线程安全的集合。

**Note：**

Collections.synchronizedMap()实现原理是Collections定义了一个SynchronizedMap的内部类，这个类实现了Map接口，在调用方法时使用synchronized来保证线程同步,当然了实际上操作的还是我们传入的HashMap实例，简单的说就是Collections.synchronizedMap()方法帮我们在操作HashMap时自动添加了synchronized来实现线程同步，类似的其它Collections.synchronizedXX方法也是类似原理。

##### **2、针对null的不同**

HashMap可以使用null作为key，而Hashtable则不允许null作为key
虽说HashMap支持null值作为key，不过建议还是尽量避免这样使用，因为一旦不小心使用了，若因此引发一些问题，排查起来很是费事。
**Note：**HashMap以null作为key时，总是存储在table数组的第一个节点上。

##### **3、继承结构**

HashMap是对Map接口的实现，HashTable实现了Map接口和Dictionary抽象类。

##### **4、初始容量与扩容**

HashMap的初始容量为16，Hashtable初始容量为11，两者的填充因子默认都是0.75。

HashMap扩容时是当前容量翻倍即:capacity*2，Hashtable扩容时是容量翻倍+1即:capacity*2+1。

##### **5、两者计算hash的方法不同**

Hashtable计算hash是直接使用key的hashcode对table数组的长度直接进行取模

```java
int hash = key.hashCode();
int index = (hash & 0x7FFFFFFF) % tab.length;
```

HashMap计算hash对key的hashcode进行了二次hash，以获得更好的散列值，然后对table数组长度取摸。

```java
int hash = hash(key.hashCode());
int i = indexFor(hash, table.length);

static int hash(int h) {
        // This function ensures that hashCodes that differ only by
        // constant multiples at each bit position have a bounded
        // number of collisions (approximately 8 at default load factor).
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }
 
 static int indexFor(int h, int length) {
        return h & (length-1);
```



***

Iterator 迭代器，它是java集合的顶层接口（不包括map系列的集合，map接口是map 系列集合的顶层接口）核心方法如下：

 boolean hasNext();
 E next();
 void remove()
 forEachRemaining(Consumer super E action)

如果想要查看集合中的所有元素，就请求一个迭代器，并在hasNext返回true时反复地调用next方法
“for each”循环可以与任何实现了Iterable接口的对象一起工作，这个接口只包含一个抽象方法：

```java
IteratorT iterator();

default void forEach(Consumer super T action) {  }

default SpliteratorT spliterator() { }

```
 Spliterator是一个可分割迭代器(splitable iterator)，可以和iterator顺序遍历迭代器一起看。Spliterator就是为了并行遍历元素而设计的一个迭代器，jdk1.8中的集合框架中的数据结构都默认实现了spliterator
可以调用forEachRemaining方法并提供一个lambda表达式（它会处理一个元素）

Iterator接口的remove方法将会删除上次调用next方法时返回的元素。

如果想删除两个相邻的元素，不能直接地这样调用：
```java
it.remove()
it.remove()  Error
```


  collection E extends Iterable E

```java
public interface IterableT {
    IteratorT iterator(); 抽象方法,返回一个迭代器
    default void forEach(Consumer super T action) {
        Objects.requireNonNull(action);
        for (T t  this) {
            action.accept(t);
        }
    }
```
Java类库提供了一个类AbstractCollection，它将基础方法size和iterator抽象化了。此时，一个具体的集合类可以扩展AbstractCollection类了。现在要由具体的集合类提供iterator方法和size()方法


List 接口(Ordered Collection)是Collection 接口的三个子接口（List、Set、Queue）之一。它是各种具体列表（ArrayList、LinkedList，Vector-线程安全）的公共接口。它们共同的特点是可以通过索引访问聚集中的对象
```java
ArrayList
ListString list = new ArrayListString();
list.add(1);
list.add(12);
index  size  index  0
[0,size]
list.add(1,)其他往后移动
list.set(1,)修改元素
list.get(1)通过索引获取元素
list.indexOf(Object)返回第一次出现的元素索引
list.lastIndexOf(Object)返回最后一次出现的索引

```
LinkedList。。在Java程序设计语言中，所有链

表实际上都是双向链接的（doubly linked）——


```java
jdk8 为双向无头结点链表
```
##### Map接口
集合有两个基本接口：Collection和Map。由于映射包含键值对，所以要用put方法来插入值。要从集合读取元素，可以用迭代器访问元素。不过，从映射中读取值则要使用get方法：



HashMap采用哈希算法实现，是Map接口最常用的实现类。
Map就是用来存储“键(key)-值(value) 对”的。 Map类中存储的“键值对”通过键来标识，所以“键对象”不能重复。
Map 接口的实现类有HashMap、TreeMap、HashTable、Properties等


###### HashMap与HashTable的区别   
1. HashMap 线程不安全，效率高。允许key或value为null。
2. HashTable 线程安全，效率低。不允许key或value为null。

[httpsblog.csdn.netqq_38182963articledetails78940047](httpsblog.csdn.netqq_38182963articledetails78940047)

###### TreeMap

TreeMap和HashMap实现了同样的接口Map，因此，用法对于调用者来说没有区别。HashMap效率高于TreeMap;在需要排序的Map时才选用TreeMap。
 有序指的是存储顺序与添加顺序相同，并且可以通过下标访问，List就是这样。无序刚好相反，指的是存储顺序与添加顺序无关，没有下标，当然也不可能通过下标访问，Set就是如此。有序、无序是指在进行插入操作时，插入位置的顺序性先插的位置在前，后插的位置在后，则为有序，反之无序。实现了List接口的集合类全部有序，如ArrayList、LinkedList
实现了Set接口的集合类中，HashSet无序，TreeSet排序实现了Map接口的集合类中，HashMap无序，TreeMap排序

##### Set
 Collection 只是一个接口, 表示是一组集合, 没有特点 (是否排序, 是否去重)。而Set 是有特点的，那就是 唯一，可以理解为： 不包含重复元素的 collection。对于接口, Set 接口，本身可以不需要重新定义这个接口,（例如：Set和 Collection 里面的add 接口定义 是一样的）， 但是 ，重新定义， 可以写上自己 特殊的注释，对于这种接口层次的重新定义, 我觉得除了可以加自己的特殊注释外, 也可以一目了然的知道 这个接口的方法; 尤其对于JDK，并且是常用的接口,如果不写注释，并且不是那么常用的接口，可以省略 ，继承父接口方法即可。



Set接口继承自Collection，Set接口中没有新增方法，方法和Collection保持完全一致。我们在前面通过List学习的方法，在Set中仍然适用。因此，学习Set的使用将没有任何难度。      Set容器特点：无序、不可重复。无序指Set中的元素没有索引，我们只能遍历查找;不可重复指不允许加入重复的元素。更确切地讲，新元素如果和Set中某个元素通过equals()方法对比为true，则不能加入;甚至，Set中也只能放入一个null元素，不能多个。      Set常用的实现类有：HashSet、TreeSet等，我们一般使用HashSet。

##### 迭代器
```java
变量list 和set都一样
List list = new ArrayList();
        list.add(1);
        list.add(2);
        list.add(3);
        for(Iterator i = list.iterator();i.hasNext();) {
        System.out.println(i.next());
           }
           
 遍历map 1
 1.获取set
SetEntryString, String ss = map.entrySet();
获取Iterator
IteratorEntry iterator = ss.Iterator()
while(iterator.hasNext()){
    Entry e = iterator.next()
    e.getKey();
    e.getValue()
}

遍历map 2
SetString ss = map.keySet();
        for (IteratorString iterator = ss.iterator(); iterator.hasNext();) {
            String key = iterator.next();
            System.out.println(key + -- + map.get(key));
        }

```

[遍历集合的方法](httpwww.sxt.cnJava_jQuery_in_actionnine-ergodicset.html)