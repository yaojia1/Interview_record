## 集合概述

### Java 集合概览

Java 集合， 也叫作容器，主要是由两大接口派生而来：一个是 `Collection`接口，主要用于存放单一元素；另一个是 `Map` 接口，主要用于存放键值对。对于`Collection` 接口，下面又有三个主要的子接口：`List`、`Set` 和 `Queue`。

Java 集合框架如下图所示：

![](./pic/java-collection-hierarchy.png)

注：图中只列举了主要的继承派生关系，并没有列举所有关系。比方省略了`AbstractList`, `NavigableSet`等抽象类以及其他的一些辅助类

长度：

- java 中的 `length`属性是针对数组说的
- java 中的 `length()` 方法是针对字符串说的
- java 中的 `size()` 方法是针对泛型集合说的，泛型有多少个元素

### 说说 List, Set, Queue, Map 四者的区别？

- `List`(对付顺序的好帮手): 存储的元素是**有序的、可重复**的。
- `Set`(注重独一无二的性质): 存储的元素是**无序的、不可重复**的。
- `Queue`(实现排队功能的叫号机): 按特定的排队规则来确定先后顺序，存储的元素是**有序的、可重复的**。
- `Map`(用 key 来搜索的专家): **使用键值对**（key-value）存储，**key 是无序的、不可重复的，value 是无序的、可重复的，每个键最多映射到一个值**。

### 集合框架底层数据结构总结

 `Collection` 接口下面的集合。

#### List

- `Arraylist`： `Object[]` 数组
- `Vector`：`Object[]` 数组
- `LinkedList`： 双向链表(JDK1.6 之前为循环链表，JDK1.7 取消了循环)

#### Set

- `HashSet`(无序，唯一): 基于 `HashMap` 实现的，底层采用 `HashMap` 来保存元素
- `LinkedHashSet`: `LinkedHashSet` 是 `HashSet` 的子类，并且其内部是通过 `LinkedHashMap` 来实现的。有点类似于我们之前说的 `LinkedHashMap` 其内部是基于 `HashMap` 实现一样，不过还是有一点点区别的
- `TreeSet`(有序，唯一): 红黑树(**自平衡的排序二叉树**)

#### Queue

- `PriorityQueue`: `Object[]` 数组来实现二叉堆
- `ArrayQueue`: `Object[]` 数组 + 双指针

再来看看 `Map` 接口下面的集合。

#### Map

- `HashMap`：底层是基于拉链式散列结构即**由数组和链表或红黑树组成**，数组是 `HashMap` 的主体。

  为解决哈希冲突：

  * 链表长度小于阈值（默认为 8）：由数组+链表组成的（“拉链法”解决冲突）；

  * 当大于阈值：将链表转化为红黑树，以减少搜索时间（如果当前数组长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）

- `LinkedHashMap`： `LinkedHashMap` 继承自 `HashMap`，上面结构的基础上，增加了一条双向链表，使得**上面的结构可以保持键值对的插入顺序**。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。

- `Hashtable`： 数组+链表组成的，数组是 `Hashtable` 的主体，链表则是主要为了解决哈希冲突而存在的

- `TreeMap`： 红黑树（自平衡的排序二叉树）

### 如何选用集合?

根据键值获取到元素值时就选用 `Map` 接口下的集合

- 需要排序时选择 `TreeMap`
- 不需要排序时就选择 `HashMap`
- 需要保证线程安全就选用 `ConcurrentHashMap`。

只需要存放元素值时，就选择实现`Collection` 接口的集合

- 保证元素唯一时选择实现 `Set` 接口的集合比如 `TreeSet` 或 `HashSet`
- 不需要就选择实现 `List` 接口的比如 `ArrayList` 或 `LinkedList`

### 为什么要使用集合？

保存一组类型相同的数据时，使用数组存储对象具有一定的弊端：

- 长度不可变；
- 声明时要决定数组的数据类型；
- 数据有序的、可重复的，特点单一。

集合**提高了数据存储的灵活性**，Java 集合不仅可以用来**存储不同类型不同数量**的对象，还**可以保存具有映射关系的数据**。

## Collection 子接口之 List

### Arraylist 和 Vector 的区别?

- `ArrayList` 是 `List` 的主要实现类，底层使用 `Object[ ]`存储，**适用于频繁的查找工作，线程不安全** ；
- `Vector` 是 **`List` 的古老实现类**，底层使用`Object[ ]` 存储，**线程安全**的。

### Arraylist 与 LinkedList 区别?

1. **是否保证线程安全：** `ArrayList` 和 `LinkedList` 都是不同步的，也就是**不保证线程安全**；

2. **底层数据结构：**

    `Arraylist` 底层使用的是 **`Object` 数组**；

   `LinkedList` 底层使用的是 **双向链表** 数据结构

3. **插入和删除是否受元素位置的影响：**

   - `ArrayList` 采用数组存储，所以**插入和删除元素的时间复杂度受元素位置的影响**。 追加到末尾，时间复杂度 O(1)；在指定位置为 O(n-i)，因为要执行向后位/向前移位的操作。
   - `LinkedList` 采用链表存储，所以，**在头尾**插入或者删除**不受位置的影响**，近似 O(1)；在**指定位置为 O(n)** ，因为需要**先移动到指定位置**再插入。

4. **是否支持快速随机访问：** **`LinkedList` 不支持高效的随机元素访问**，而 `ArrayList` 支持。快速随机访问就是通过元素的序号快速获取元素对象(对应于`get(int index)`方法)。

5. **内存空间占用：** ArrayList 的空 间浪费主要体现在**在 list 列表的结尾会预留一定的容量空间**，而 LinkedList 的空间花费则体现在它的**每一个元素都需要消耗比 ArrayList 更多的空间**（因为要存放直接后继和直接前驱以及数据）。

#### 补充内容:双向链表和双向循环链表

**双向链表：** 包含两个指针，一个 prev 指向前一个节点，一个 next 指向后一个节点。

**双向循环链表：** 最后一个节点的 next 指向 head，而 head 的 prev 指向最后一个节点，构成一个环。

###  ArrayList 的扩容机制

**`int newCapacity = oldCapacity + (oldCapacity >> 1)`所以 ArrayList 每次扩容之后容量都会变为原来的 1.5 倍左右（oldCapacity 为偶数就是 1.5 倍，否则是 1.5 倍左右）！**

`ensureCapacity`方法：

如有必要，增加此 ArrayList 实例的容量，以确保它至少可以容纳由minimum capacity参数指定的元素数

**最好在 add 大量元素之前用 `ensureCapacity` 方法，以减少增量重新分配的次数**

## Collection 子接口之 Set

### comparable 和 Comparator 的区别

- `comparable` 接口实际上是出自`java.lang`包，它有一个 `compareTo(Object obj)`方法用来排序
- `comparator`接口实际上是出自 `java.util`包，它有一个`compare(Object obj1, Object obj2)`方法用来排序

**对一个集合使用自定义排序时**：重写`compareTo()`方法或`compare()`方法

**对某一个集合实现两种排序方式**，重写`compareTo()`方法和使用自制的`Comparator`方法或者以两个 Comparator 来实现歌名排序和歌星名排序，第二种代表我们只能使用两个参数版的 `Collections.sort()`.

#### Comparator 定制排序

    Collections.sort(arrayList, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2.compareTo(o1);
            }
        });

#### 重写 compareTo 方法

实现Comparable接口`public  class Person implements Comparable<Person>`

重写compareTo return 1,0,-1

### 无序性和不可重复性的含义是什么

1、什么是无序性？无序性不等于随机性 ，无序性是指存储的数据**在底层数组中并非按照数组索引的顺序添加 ，而是根据数据的哈希值决定的。**

2、什么是不可重复性？不可重复性是指添加的元素按照 equals()判断时 ，返回 false，需要同时重写 equals()方法和 HashCode()方法。

### 比较 HashSet、LinkedHashSet 和 TreeSet 三者的异同

- `HashSet`、`LinkedHashSet` 和 `TreeSet` **都是 `Set` 接口的实现类**，**都能保证元素唯一，并且都不是线程安全的。**

- **主要区别在于底层数据结构不同**:

  * `HashSet` 的底层数据结构是哈希表（**基于 `HashMap` 实现**）。

  * `LinkedHashSet` 的底层数据结构是**链表和哈希表**，元素的插入和取出顺序满足 FIFO。

  * `TreeSet` 底层数据结构是**红黑树，元素是有序的**，排序的方式有自然排序和定制排序。

- 底层数据结构不同又导致这三者的**应用场景不同**:

  * `HashSet` 用于不需要保证元素插入和取出顺序的场景

  * `LinkedHashSet` 用于保证元素的插入和取出顺序满足 FIFO 的场景

  * `TreeSet` 用于支持**对元素自定义排序规则**的场景。

## Collection 子接口之 Queue

### Queue 与 Deque 的区别

- `Queue` **一端插入，另一端删除**，一般遵循 **先进先出（FIFO）** 规则。
- `Deque` 是**双端队列**，在队列的**两端均可以插入或删除元素**。

`Queue` 扩展了 `Collection` 的接口，根据 **因为容量问题而导致操作失败后处理方式的不同** 可以分为两类方法: 一种在操作失败后会抛出异常，另一种则会返回特殊值。

| `Queue` 接口 | 抛出异常  | 返回特殊值 |
| ------------ | --------- | ---------- |
| 插入队尾     | add(E e)  | offer(E e) |
| 删除队首     | remove()  | poll()     |
| 查询队首元素 | element() | peek()     |

`Deque` 扩展了 `Queue` 的接口, 增加了在队首和队尾进行插入和删除的方法，同样根据失败后处理方式的不同分为两类：

| `Deque` 接口 | 抛出异常      | 返回特殊值      |
| ------------ | ------------- | --------------- |
| 插入队首     | addFirst(E e) | offerFirst(E e) |
| 插入队尾     | addLast(E e)  | offerLast(E e)  |
| 删除队首     | removeFirst() | pollFirst()     |
| 删除队尾     | removeLast()  | pollLast()      |
| 查询队首元素 | getFirst()    | peekFirst()     |
| 查询队尾元素 | getLast()     | peekLast()      |

事实上，`Deque` 还提供有 `push()` 和 `pop()` 等其他方法，可用于模拟栈。


### ArrayDeque 与 LinkedList 的区别

`ArrayDeque` 和 `LinkedList` **都实现了 `Deque` 接口**，两者都具有队列的功能，但两者有什么区别呢？

- `ArrayDeque` 是**基于可变长的数组和双指针**来实现，而 `LinkedList` 则通过**链表**来实现。

- `ArrayDeque` **不支持存储 `NULL` 数据**，但 `LinkedList` 支持。

- `ArrayDeque` **插入时可能存在扩容**过程, 不过均摊后的插入操作依然为 O(1)。虽然 `LinkedList` 不需要扩容，但是**每次插入数据时均需要申请新的堆空间，均摊性能相比更慢**。

从**性能的角度上，选用 `ArrayDeque` 来实现队列要比 `LinkedList` 更好**。此外，`ArrayDeque` 也可以用于实现栈。

### 说一说 PriorityQueue

`PriorityQueue` 与 `Queue` 的区别在于**元素出队顺序是与优先级相关**的，即总是优先级最高的元素先出队。

这里列举其相关的一些要点：

- `PriorityQueue` **利用了二叉堆的数据结构**来实现的，**底层使用可变长的数组**来存储数据
- `PriorityQueue` 通过堆元素的上浮和下沉，实现了在 O(logn) 的时间复杂度内插入元素和删除堆顶元素。
- `PriorityQueue` 是**非线程安全**的，且**不支持存储 `NULL` 和 `non-comparable` 的对象**。
- `PriorityQueue` 默认是小顶堆，但**可以接收一个 `Comparator` 作为构造参数**，从而来自定义元素优先级的先后。

`PriorityQueue` 在面试中可能更多的会出现在手撕算法的时候，典型例题包括堆排序、求第K大的数、带权图的遍历等，所以需要会熟练使用才行。

## Map 接口

### HashMap 和 Hashtable 的区别

1. **线程是否安全：**

    `HashMap` 是非线程安全的，`Hashtable` 是线程安全的

   保证线程安全用 `ConcurrentHashMap`

2. **效率：** 因为线程安全的问题，`HashMap` 高一点。另外，**`Hashtable` 基本被淘汰**；

3. **对 Null key 和 Null value 的支持：** 

   `HashMap` 可以，但 null 作为键只能有一个，null 作为值可以有多个

   Hashtable 不允许，会抛出 `NullPointerException`。

4. **初始容量大小和每次扩充容量大小的不同 ：** 

   ① `Hashtable` 默认的初始大小为 11，每次扩充为 2n+1。`HashMap` 默认的初始化大小为 16。之后每次扩充为 2 倍。

   ② 创建时如果给定了容量初始值，那么 Hashtable 会直接使用你给定的大小，而 `HashMap` 会将其扩充为 2 的幂次方大小（`HashMap` 中的`tableSizeFor()`方法保证，下面给出了源代码）。也就是说 `HashMap` 总是使用 2 的幂作为哈希表的大小,后面会介绍到为什么是 2 的幂次方。

5. **底层数据结构：**

    JDK1.8 以后的 `HashMap` 当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

   Hashtable 没有这样的机制。

### HashMap 和 HashSet 区别

**`HashSet` 底层就是基于 `HashMap` 实现**的。（`HashSet` 的源码非常非常少，因为除了 `clone()`、`writeObject()`、`readObject()`是 `HashSet` 自己不得不实现之外，其他方法都是直接调用 `HashMap` 中的方法。

|               `HashMap`                |                          `HashSet`                           |
| :------------------------------------: | :----------------------------------------------------------: |
|           实现了 `Map` 接口            |                       实现 `Set` 接口                        |
|               存储键值对               |                          仅存储对象                          |
|     调用 `put()`向 map 中添加元素      |             调用 `add()`方法向 `Set` 中添加元素              |
| `HashMap` 使用键（Key）计算 `hashcode` | `HashSet` 使用成员对象来计算 `hashcode` 值，对于两个对象来说 `hashcode` 可能相同，所以`equals()`方法用来判断对象的相等性 |

### HashMap 和 TreeMap 区别

相比于`HashMap`来说 `TreeMap` 主要多了对集合中的元素根据键排序的能力以及对集合内元素的搜索的能力。

`TreeMap` 和`HashMap` 都继承自`AbstractMap` ，但是`TreeMap`它还实现了`NavigableMap`接口和`SortedMap` 接口。

- 实现 `NavigableMap` 接口让 `TreeMap` 有了**对集合内元素的搜索**的能力。

- 实现`SortedMap`接口让 `TreeMap` 有了**对集合中的元素根据键排序**的能力。默认是按 key 的升序排序，不过我们也可以指定排序的比较器。

### HashSet 如何检查重复

`HashSet` 会先计算对象的`hashcode`值来判断对象加入的位置，同时也会与其他加入的对象的 `hashcode` 值作比较，如果没有相符的 `hashcode`，`HashSet` 会假设对象没有重复出现。但是如果发现有相同 `hashcode` 值的对象，这时会调用`equals()`方法来检查 `hashcode` 相等的对象是否真的相同。如果两者相同，`HashSet` 就不会让加入操作成功

**`hashCode()`与 `equals()` 的相关规定：**

1. 如果两个对象相等，则 `hashcode` 一定也是相同的
2. 两个对象相等,对两个 `equals()` 方法返回 true
3. 两个对象有相同的 `hashcode` 值，它们也不一定是相等的
4. 综上，`equals()` 方法被覆盖过，则 `hashCode()` 方法也必须被覆盖
5. `hashCode()`的默认行为是对堆上的对象产生独特值。如果没有重写 `hashCode()`，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）。

**==与 equals 的区别**

对于基本类型来说，== 比较的是值是否相等；

对于引用类型来说，== 比较的是两个引用是否指向同一个对象地址（两者在内存中存放的地址（堆内存地址）是否指向同一个地方）；

对于引用类型（包括包装类型）来说，equals 如果没有被重写，对比它们的地址是否相等；如果 equals()方法被重写（例如 String），则比较的是地址里的内容。

### HashMap 的底层实现

JDK1.8 之前 `HashMap` 底层是 **数组和链表** 结合在一起使用也就是 **链表散列**。

存储过程：

1. 通过 **key 的 hashCode** 经过**扰动函数**处理过后得到 **hash 值**

2. 通过 (n - 1) & hash **判断当前元素存放的位置**（这里的 n 指的是数组的长度）

3. 如果当前位置存在元素的话，就判断该元素与要存入的**元素的 hash 值以及 key 是否相同**：

   * 相同直接覆盖，

   * 不相同就通过拉链法解决冲突（加到改地址的单链表中）

4. **当链表长度大于阈值（默认为 8）**，判断数组的长度是否小于 64

   * 小于 64：先进行数组扩容，但是这样一来，整个Hash表里的**无素都需要被重算一遍**。这叫rehash，这个成本相当的大。

   * 大于64：将链表转化为红黑树，以减少搜索时间。

**扰动函数：指的是 HashMap 的 hash 方法。** 为了**防止一些实现比较差的 hashCode() 方法**，可以**减少碰撞。**

> TreeMap、TreeSet 以及 JDK1.8 之后的 HashMap 底层都用到了红黑树。红黑树就是为了解决二叉查找树的缺陷，因为二叉查找树在某些情况下会退化成一个线性结构。

![jdk1.8之后的内部结构-HashMap](.\pic\HashMap.png)

### HashMap 的长度为什么是 2 的幂次方

为了能让 HashMap 存取高效，尽量较少碰撞，也就是要尽量把数据分配均匀。Hash 值的范围大概 40 亿的映射空间，一般应用很难出现碰撞。但内存是放不下。所以这个是不能直接拿来用的。

**计算存储地址**：根据key求hash散列值，散列值对数组的长度取模，得到的余数是对应的数组下标。

**算法采用二进制位操作 &，相对于%能够提高运算效率，**

根据公式：**hash%length==hash&(length-1)（ length 是 2 的 n 次方）** 

**下标取模的计算方法为：“ `(n - 1) & hash`”。**（n 代表数组长度）。

**所以 HashMap 的长度是 2 的幂次方。**

### HashMap 多线程操作导致死循环问题

主要原因在于**并发下的 Rehash 会造成元素之间会形成一个循环链表**。不过，jdk 1.8 后解决了这个问题，但是还是不建议在多线程下使用 HashMap,因为多线程下使用 HashMap 还是会存在其他问题比如数据丢失。并发环境下推荐使用 ConcurrentHashMap 。

详情请查看：<https://coolshell.cn/articles/9606.html>

### HashMap 有哪几种常见的遍历方式?

HashMap **遍历从大的方向来说，可分为以下 4 类**：

1. 迭代器（Iterator）方式遍历；
2. For Each 方式遍历；
3. Lambda 表达式遍历（JDK 1.8+）;
4. Streams API 遍历（JDK 1.8+）。

但每种类型下又有不同的实现方式，因此具体的遍历方式又可以分为以下 7 种：

1. 使用迭代器（Iterator）

   * EntrySet 的方式进行遍历；

   * KeySet 的方式进行遍历；

2. 使用 For Each 

   * EntrySet 的方式进行遍历；

   * KeySet 的方式进行遍历；

3. 使用 Lambda 表达式的方式进行遍历；

4. 使用 Streams API 单线程的方式进行遍历；

5. 使用 Streams API 多线程的方式进行遍历。

两个 `entrySet` 的性能相近，并且执行速度最快，接下来是 `stream` ，然后是两个 `keySet`，性能最差的是 `KeySet` 。**从以上结果可以看出 `entrySet` 的性能比 `keySet` 的性能高出了一倍之多，因此我们应该尽量使用 `entrySet` 来实现 Map 集合的遍历**。

1. 迭代器：

```java
Iterator<Map.Entry<Integer, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
  Map.Entry<Integer, String> entry = iterator.next();
}
```

3. lambda

```java
// 根据 map 中的 key 去判断删除
map.keySet().removeIf(key -> key == 1);
map.forEach((key, value) -> {
    System.out.println("show:" + key);
});
```

4. stream

```
map.entrySet().stream().filter(m -> 1 != m.getKey()).forEach((entry) -> {
    if (entry.getKey() == 1) {
        System.out.println("del:" + entry.getKey());
    } else {
        System.out.println("show:" + entry.getKey());
    }
});
```

- 我们不能在遍历中使用集合 `map.remove()` 来删除数据，这是非安全的操作方式，
- 可以使用迭代器的 `iterator.remove()` 的方法来删除数据，这是安全的删除集合的方式。
- 可以使用 Lambda 中的 `removeIf` 来提前删除数据，
- 使用 Stream 中的 `filter` 过滤掉要删除的数据进行循环
- 也可以在 `for` 循环前删除数据在遍历也是线程安全的。

综合性能和安全性来看，**我们应该尽量使用迭代器（Iterator）来遍历 `EntrySet` 的遍历方式来操作 Map 集合**，这样就会既安全又高效了。

### ConcurrentHashMap 和 Hashtable 的区别

`ConcurrentHashMap` 和 `Hashtable` 的区别主要体现在实现线程安全的方式上不同。

- **底层数据结构：** JDK1.7 的 `ConcurrentHashMap` 底层采用 **分段的数组+链表** 实现，JDK1.8 采用的数据结构跟 `HashMap` 的结构一样，数组+链表/红黑二叉树。

- **实现线程安全的方式（重要）：** 

  ①**`ConcurrentHashMap`（分段锁）** 用 `Node` 数组+链表+红黑树的数据结构来实现，并发控制使用 **`synchronized` 和 CAS 来操作**。

  ② **`Hashtable`(同一把锁)** :使用 `synchronized` 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态.

### ConcurrentHashMap 线程安全的具体实现方式/底层具体实现

#### JDK1.8 

`ConcurrentHashMap` 取消了 `Segment` 分段锁，采用 CAS 和 `synchronized` 来保证并发安全。数据结构跟 HashMap1.8 的结构类似，数组+链表/红黑二叉树。Java 8 在链表长度超过一定阈值（8）时将链表（寻址时间复杂度为 O(N)）转换为红黑树（寻址时间复杂度为 O(log(N))）

**`synchronized` 只锁定当前链表或红黑二叉树的首节点**，这样只要 hash 不冲突，就不会产生并发，效率又提升 N 倍。

## Collections 工具类

Collections 工具类常用方法:

1. 排序
2. 查找,替换操作
3. 同步控制(不推荐，需要线程安全的集合类型时请考虑使用 JUC 包下的并发集合)

### 排序操作

```java
void reverse(List list)//反转
void shuffle(List list)//随机排序
void sort(List list)//按自然排序的升序排序
void sort(List list, Comparator c)//定制排序，由Comparator控制排序逻辑
void swap(List list, int i , int j)//交换两个索引位置的元素
void rotate(List list, int distance)//旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面
```

### 查找,替换操作

```java
int binarySearch(List list, Object key)//对List进行二分查找，返回索引，注意List必须是有序的
int max(Collection coll)//根据元素的自然顺序，返回最大的元素。 类比int min(Collection coll)
int max(Collection coll, Comparator c)//根据定制排序，返回最大元素，排序规则由Comparatator类控制。类比int min(Collection coll, Comparator c)
void fill(List list, Object obj)//用指定的元素代替指定list中的所有元素
int frequency(Collection c, Object o)//统计元素出现次数
int indexOfSubList(List list, List target)//统计target在list中第一次出现的索引，找不到则返回-1，类比int lastIndexOfSubList(List source, list target)
boolean replaceAll(List list, Object oldVal, Object newVal)//用新元素替换旧元素
```

### 同步控制

`Collections` 提供了多个`synchronizedXxx()`方法·，该方法可以将指定集合包装成线程同步的集合，从而解决多线程并发访问集合时的线程安全问题。

我们知道 `HashSet`，`TreeSet`，`ArrayList`,`LinkedList`,`HashMap`,`TreeMap` 都是线程不安全的。`Collections` 提供了多个静态方法可以把他们包装成线程同步的集合。

