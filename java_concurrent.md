# 多线程基础

## 创建线程

1.实例化一个`Thread`实例，然后调用它的`start()`方法：

方法一：从`Thread`派生一个自定义类，然后覆写`run()`方法：

方法二：创建`Thread`实例时，传入一个`Runnable`实例：

` Thread t = new Thread(new MyRunnable());`

或lambda写法：`Thread t = new Thread(() -> {});`

### 线程的优先级

可以对线程设定优先级，设定优先级的方法是：

```
Thread.setPriority(int n) // 1~10, 默认值5
```

## 线程状态

### 等待

通过对另一个线程对象调用`join()`方法可以等待其执行结束；

可以指定等待时间，超过等待时间线程仍然没有结束就不再等待；

对已经运行结束的线程调用`join()`方法会立刻返回。

### 中断

对目标线程调用`interrupt()`，发出了“中断请求”，至于`t`线程是否能立刻响应，要看具体代码。

目标线程通过检测`isInterrupted()`标志获取自身是否已中断

- 如果目标线程处于等待状态，`join()`/`sleep()`都会立刻结束等待并抛出`InterruptedException`，可以被catch到；
- 目标线程需要反复检测自身状态是否是`interrupted()`状态，如果是，就立刻结束运行。

另一个常用的中断线程的方法是**设置标志位**，需要使用`volatile`关键字。

### 守护线程（Daemon Thread）

在JVM中，所有非守护线程都执行完毕后，无论有没有守护线程，虚拟机都会自动退出。

因此，JVM退出时，不必关心守护线程是否已结束。

方法：在调用`start()`方法前，调用`setDaemon(true)`把该线程标记为守护线程

### 等待

**`sleep()` 方法没有释放锁，而 `wait()` 方法释放了锁** 。

wait()

必须在`synchronized`块中才能调用`wait()`方法，进入等待，**释放线程获得的锁**，`wait()`方法返回后，线程又会重新试图获得锁。

当一个线程在`wait()`等待时，它就会释放`this`锁

- 在相同的锁对象上调用`notify()`方法，会唤醒一个正在`this`锁等待的线程，从而使得等待线程从`this.wait()`方法返回。
- 使用`notifyAll()`将唤醒所有当前正在`this`锁等待的线程，而`notify()`只会唤醒其中一个（具体哪个依赖操作系统，有一定的随机性）。要始终在`while`循环中`wait()`，并且每次被唤醒后拿到`this`锁就必须再次判断：

通常来说，`notifyAll()`更安全。有些时候，如果我们的代码逻辑考虑不周，用`notify()`会导致只唤醒了一个线程，而其他线程可能永远等待下去醒不过来了。

`wait`和`notify`用于多线程协调运行：

- 在`synchronized`内部可以调用`wait()`使线程进入等待状态；
- 必须在已获得的锁对象上调用`wait()`方法；
- 在`synchronized`内部可以调用`notify()`或`notifyAll()`唤醒其他等待线程；
- 必须在已获得的锁对象上调用`notify()`或`notifyAll()`方法；
- 已唤醒的线程还需要重新获得锁后才能继续执行。



## 使用synchronized关键字

在使用`synchronized`的时候，不必担心抛出异常。因为无论是否有异常，都会在`synchronized`结束处正确释放锁：

更好的方法是把`synchronized`修饰同步方法改变静态变量。

缺点：这种锁一是很重，二是获取时必须一直等待，没有额外的尝试机制。任何时刻，只允许一个线程修改。

## 并发处理concurrent包

`ReentrantLock`增加了一些高级功能：

- **等待可中断** : 通过 `lock.lockInterruptibly()` 
- **可实现公平锁** : `ReentrantLock(boolean fair)`**构造方法可以指定是公平锁还是非公平锁**。
- **可实现选择性通知（锁可以绑定多个条件）**

### 使用ReentrantLock

必须先获取到锁，再进入`try {...}`代码块，最后使用`finally`保证释放锁；

```
private final Lock lock = new ReentrantLock();
private final Condition condition = lock.newCondition();
public void add(int n) {
        lock.lock();
        try {
            count += n;
        } finally {
            lock.unlock();
        }
    }
```

`ReentrantLock`可以尝试获取锁：

```
if (lock.tryLock(1, TimeUnit.SECONDS)) {
    try {
        ...
    } finally {
        lock.unlock();
    }
}
```

#### wait

使用`Condition`对象来实现`wait`和`notify`的功能。

```
private final Lock lock = new ReentrantLock();
private final Condition condition = lock.newCondition();
```

`Condition`提供的`await()`、`signal()`、`signalAll()`原理和`synchronized`锁对象的`wait()`、`notify()`、`notifyAll()`是一致的，并且其行为也是一样的：

- `await()`会释放当前锁，进入等待状态；
- `signal()`会唤醒某个等待线程；
- `signalAll()`会唤醒所有等待线程；
- 唤醒线程从`await()`返回后需要重新获得锁。

和`tryLock()`类似，`await()`可以在等待指定时间后，如果还没有被其他线程通过`signal()`或`signalAll()`唤醒，可以自己醒来：`condition.await(1, TimeUnit.SECOND)`

### ReadWriteLock悲观读写锁

使用`ReadWriteLock`，它保证：

- 只允许一个线程写入（其他线程既不能写入也不能读取）；
- 没有写入时，多个线程允许同时读（提高性能）。

用`ReadWriteLock`实现这个功能十分容易。我们需要创建一个`ReadWriteLock`实例，然后分别获取读锁和写锁：

```
    private final ReadWriteLock rwlock = new ReentrantReadWriteLock();
    private final Lock rlock = rwlock.readLock();
    private final Lock wlock = rwlock.writeLock();
    rlock.lock();rlock.unlock;
```

使用`ReadWriteLock`可以提高读取效率：

- `ReadWriteLock`只允许一个线程写入；
- `ReadWriteLock`允许多个线程在没有写入时同时读取；
- `ReadWriteLock`适合读多写少的场景。

问题：

如果有线程正在读，写线程需要等待读线程释放锁后才能获取写锁，即**读的过程中不允许写**，这是一种悲观的读锁。

### StampedLock乐观读写锁

`StampedLock`和`ReadWriteLock`相比，改进之处在于：读的过程中也允许获取写锁后写入！这样一来，我们读的数据就可能不一致，所以，需要一点额外的代码来判断读的过程中是否有写入，这种读锁是一种乐观锁。

写入：

```
private final StampedLock stampedLock = new StampedLock();
long stamp = stampedLock.writeLock(); // 获取写锁
stampedLock.unlockWrite(stamp); // 释放写锁
```

读取：

1. 通过`long stamp = stampedLock.tryOptimisticRead()`获取一个乐观读锁，并返回版本号。

2. 通过`stampedLock.validate(stamp)`去验证版本号`

   * 如果在读取过程中没有写入，版本号不变，验证成功，我们就可以放心地继续后续操作。

   * 如果在读取过程中有写入，版本号会发生变化，验证将失败。
     - 在失败的时候，我们再通过获取悲观读锁再次读取：`stamp = stampedLock.readLock(); `
     - try 读取后finally `stampedLock.unlockRead(stamp); // 释放悲观读锁`

由于写入的概率不高，程序在绝大部分情况下可以通过乐观读锁获取数据，极少数情况下使用悲观读锁获取数据。

可见，`StampedLock`把读**锁细分为乐观读和悲观读**，能**进一步提升并发效率**。但这也是有代价的：一是**代码更加复杂**，二是`StampedLock`是**不可重入锁，不能在一个线程中反复获取同一个锁**。

### 使用Concurrent并发集合

针对`List`、`Map`、`Set`、`Deque`等，`java.util.concurrent`包也提供了对应的并发集合类。我们归纳一下：

| interface | non-thread-safe         | thread-safe                              |
| :-------- | :---------------------- | :--------------------------------------- |
| List      | ArrayList               | CopyOnWriteArrayList                     |
| Map       | HashMap                 | ConcurrentHashMap                        |
| Set       | HashSet / TreeSet       | CopyOnWriteArraySet                      |
| Queue     | ArrayDeque / LinkedList | ArrayBlockingQueue / LinkedBlockingQueue |
| Deque     | ArrayDeque / LinkedList | LinkedBlockingDeque                      |

`java.util.concurrent`包提供的线程安全的集合：`ArrayBlockingQueue`。

`BlockingQueue`：当一个线程调用这个`TaskQueue`的`getTask()`方法时，该方法内部可能会让线程变成等待状态，直到队列条件满足不为空，线程被唤醒后，`getTask()`方法才会返回。

#### 旧的线程安全集合转换器

`java.util.Collections`工具类提供

`Map threadSafeMap = Collections.synchronizedMap(unsafeMap);`但是它实际上对所有读写方法都用`synchronized`加锁，性能比`java.util.concurrent`集合要低很多，所以不推荐使用。

### Concurrent原子操作的封装类Atomic

提供了一组原子操作的封装类，它们位于`java.util.concurrent.atomic`包。

我们以`AtomicInteger`为例，它提供的主要操作有：

- 增加值并返回新值：`int addAndGet(int delta)`
- 加1后返回新值：`int incrementAndGet()`
- 获取当前值：`int get()`
- 用CAS方式设置：`int compareAndSet(int expect, int update)`

Atomic类是通过无锁（lock-free）的方式实现的线程安全（thread-safe）访问。它的主要原理是利用了CAS：Compare and Set。CAS是指，在这个操作中，如果`AtomicInteger`的当前值是`prev`，那么就更新为`next`，返回`true`。如果`AtomicInteger`的当前值不是`prev`，就什么也不干，返回`false`。

### Fork/Join线程池

可以：把一个大任务拆成多个小任务并行执行。

原理：判断一个任务是否足够小，如果是，直接计算，否则，就分拆成几个小任务分别计算。这个过程可以反复“裂变”成一系列小任务。

实现：任务类必须继承自`RecursiveTask`或`RecursiveAction`。

### `ThreadLocal`

它可以在一个线程中传递同一个对象。

初始化：静态字段

```
static ThreadLocal<User> threadLocalUser = new ThreadLocal<>();
```

使用：通过设置一个`User`实例关联到`ThreadLocal`中，在移除之前，所有方法都可以随时获取到该`User`实例

使用`ThreadLocal`要用`try ... finally`结构，并在`finally`中清除：当前线程执行完相关代码后，很可能会被重新放入线程池中，如果`ThreadLocal`没有被清除，该线程执行其他代码时，会把上一次的状态带进去。

为了保证能释放`ThreadLocal`关联的实例，我们可以通过`AutoCloseable`接口配合`try (resource) {...}`结构，让编译器自动为我们关闭。例如，一个保存了当前用户名的`ThreadLocal`可以封装为一个`UserContext`对象：

## 线程池

### 构造函数

线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式

#### `ThreadPoolExecutor`参数

**`ThreadPoolExecutor` 3 个最重要的参数：**

- **`corePoolSize` :** 最小可以同时运行的线程数量。
- **`maximumPoolSize` :** 当前可以同时运行的线程数量
- **`workQueue`:** 当新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，新任务就会被存放在队列中。

`ThreadPoolExecutor`其他常见参数:

1. **`keepAliveTime`**:当线程池中的线程数量大于 `corePoolSize` 的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了 `keepAliveTime`才会被回收销毁；
2. **`unit`** : `keepAliveTime` 参数的时间单位。
3. **`threadFactory`** :executor 创建新线程的时候会用到。
4. **`handler`** :饱和策略。关于饱和策略下面单独介绍一下。

#### `ThreadPoolExecutor` 饱和策略

**`ThreadPoolExecutor` 饱和策略定义:**

如果当前同时运行的线程数量达到最大线程数量并且队列也已经被放满了任务时，`ThreadPoolTaskExecutor` 定义一些策略:

- **`ThreadPoolExecutor.AbortPolicy`：** 抛出 `RejectedExecutionException`来拒绝新任务的处理。
- **`ThreadPoolExecutor.CallerRunsPolicy`：** 调用执行自己的线程运行任务，也就是直接在调用`execute`方法的线程中运行(`run`)被拒绝的任务，如果执行程序已关闭，则会丢弃该任务。因此这种策略会降低对于新任务提交速度，影响程序的整体性能。如果您的应用程序可以承受此延迟并且你要求任何一个任务请求都要被执行的话，你可以选择这个策略。
- **`ThreadPoolExecutor.DiscardPolicy`：** 不处理新任务，直接丢弃掉。
- **`ThreadPoolExecutor.DiscardOldestPolicy`：** 此策略将丢弃最早的未处理的任务请求。

举个例子： Spring 通过 `ThreadPoolTaskExecutor` 或者我们直接通过 `ThreadPoolExecutor` 的构造函数创建线程池的时候，当我们不指定 `RejectedExecutionHandler` 饱和策略的话来配置线程池的时候默认使用的是 `ThreadPoolExecutor.AbortPolicy`。在默认情况下，`ThreadPoolExecutor` 将抛出 `RejectedExecutionException` 来拒绝新来的任务 ，这代表你将丢失对这个任务的处理。 对于可伸缩的应用程序，建议使用 `ThreadPoolExecutor.CallerRunsPolicy`。当最大池被填满时，此策略为我们提供可伸缩队列。（这个直接查看 `ThreadPoolExecutor` 的构造函数源码就可以看出，比较简单的原因，这里就不贴代码了）



### 通过 Executor 框架的工具类 Executors 来实现

`ExecutorService`接口表示线程池，Java标准库提供的几个常用实现类有：

- FixedThreadPool：线程数固定的线程池；
- CachedThreadPool：线程数根据任务动态调整的线程池；
- SingleThreadExecutor：仅单线程执行的线程池。
- ScheduledThreadPool：调度多个定时任务，可以取代旧的`Timer`。
  * `ses.schedule();`几秒后执行一次性任务:
  * `ses.scheduleAtFixedRate`：几秒后固定时间间隔触发，不管任务执行多长时间：
  * `ses.scheduleWithFixedDelay`：几秒后触发，上一次任务执行完毕后，等待固定的时间间隔

es.submit()提交线程

关闭线程池：

- `shutdown()`等待正在执行的任务先完成，然后再关闭。
- `shutdownNow()`会立刻停止正在执行的任务
- `awaitTermination()`则会等待指定的时间让线程池关闭。



## Callable

`Runnable`接口有个问题，它的方法没有返回值。

`Callable`接口，有一个返回值：`Callable`接口是一个泛型接口，可以返回指定类型的结果。

`ExecutorService.submit()`方法，提交一个`Callable`任务后，返回一个`Future`类型：

一个`Future<V>`接口表示一个未来可能会返回的结果，它定义的方法有：

- `get()`：获取结果（异步任务还没有完成，那么`get()`会阻塞）
- `get(long timeout, TimeUnit unit)`：获取结果，但只等待指定的时间；
- `cancel(boolean mayInterruptIfRunning)`：取消当前任务；
- `isDone()`：判断任务是否已完成。

调用阻塞方法`get()`，或轮询看`isDone()`是否为`true`，会使主线程也会被迫等待。

#### `CompletableFuture`

它针对`Future`做了改进，可以传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法。

`CompletableFuture`可以指定异步处理流程：

- `thenAccept()`处理正常结果；
- `exceptional()`处理异常结果；
- `thenApplyAsync()`用于串行化另一个`CompletableFuture`；
- `anyOf()`和`allOf()`用于并行化多个`CompletableFuture`。

`CompletableFuture`的优点是：

- 异步任务结束时，会自动回调某个对象的方法；
- 异步任务出错时，会自动回调某个对象的方法；
- 主线程设置好回调后，不再关心异步任务的执行。



# 概念问题解析

## 什么是线程和进程?

### 何为进程?

进程是**程序的一次执行过程**，是**系统运行程序的基本单位**，是动态的。系统运行一个程序即是一个进程从创建，运行到消亡的过程。

### 何为线程?

线程是一个比进程更小的执行单位，也被称为轻量级进程。

一个进程在其执行的过程中可以产生多个线程，多个线程共享进程的**堆**和**方法区**资源，但每个线程有自己的**程序计数器**、**虚拟机栈**和**本地方法栈**

### java程序

**一个 Java 程序的运行是 main 线程和多个其他线程同时运行。**

**启动 main 函数时其实就是启动了一个 JVM 的进程**，主线程是main 函数所在的线程。

当所有线程都运行结束时，JVM退出，进程结束。

如果有一个线程没有退出，JVM进程就不会退出。所以，必须保证所有线程都能及时结束。

### 进程和线程的关系

进程和线程是**包含关系**：**一个进程可以包含一个或多个线程**，但至少会有一个线程。

### 区别

1. **基本上各进程是独立的，同一进程中的线程极有可能会相互影响。**
2. **线程执行开销小，但不利于资源的管理和保护；而进程正相反。**

### 多进程 vs 多线程优缺点

**多进程的缺点**在于：

- 创建进程比创建线程开销大，尤其是在Windows系统上；
- 进程间通信比线程间通信要慢，因为线程间通信就是读写同一个变量，速度很快。

**多进程的优点**在于：

**多进程稳定性比多线程高**，因为在多进程的情况下，一个进程崩溃不会影响其他进程，而在多线程的情况下，任何一个线程崩溃会直接导致整个进程崩溃。

### 为什么要使用多线程呢?

先从总体上来说：**提高程序的执行效率提高程序运行速度**

- **从计算机底层来说：** 线程间的**切换和调度的成本**远远小于进程。另外，**多核 CPU** 时代意味着多个线程可以同时运行，这**减少了线程上下文切换的开销**。
- **从当代互联网发展趋势来说：** 现在的**系统动不动就要求百万级甚至千万级的并发量**，而多线程并发编程正是开发高并发系统的基础，利用好多线程机制可以大大**提高系统整体的并发能力以及性能**。

### 使用多线程可能带来什么问题?

并发编程并不总是能提高程序运行速度，可能会遇到问题，比如：**内存泄漏、死锁、线程不安全**等等。

### 说说并发（concurrent）与并行（parallel）的区别?

- **并发**：两个及两个以上的作业在同一 **时间段** 内执行。（同时发生）
  * 顺序（sequential）：上一个开始执行的任务完成后，当前任务才能开始执行
- **并行**：两个及两个以上的作业在同一 **时刻** 执行。（同时进行）
  * 串行（serial）：有一个任务执行单元，从物理上就只能一个任务、一个任务地执行

并行一定会允许并发。并发单核就可以做到，并行必须多核CPU才能办到（并发宏观并行，微观串行）。前者关注的是任务的抽象调度，后者关注的是任务的实际执行。

## 线程资源

### 程序计数器为什么是私有的?

程序计数器私有主要是为了**线程切换后能恢复到正确的执行位置**。

程序计数器主要有下面两个作用：

1. 字节码解释器**通过改变程序计数器来依次读取指令，从而实现代码的流程控制**，如：顺序执行、选择、循环、异常处理。
2. 在多线程的情况下，**程序计数器用于记录当前线程执行的位置**，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。

需要注意的是，如果执行的是 native 方法，那么程序计数器记录的是 undefined 地址，只有执行的是 Java 代码时程序计数器记录的才是下一条指令的地址。

### 虚拟机栈和本地方法栈为什么是私有的?

为了**保证线程中的局部变量不被别的线程访问到**，虚拟机栈和本地方法栈是线程私有的。

- **虚拟机栈：** 每个 Java 方法在执行的同时会创建一个**栈帧用于存储局部变量表、操作数栈、常量池引用等信息**。从方法调用直至执行完成的过程，就对应着一个栈帧在 Java 虚拟机栈中入栈和出栈的过程。
- **本地方法栈：** 和虚拟机栈所发挥的作用非常相似，区别是： **虚拟机栈为虚拟机执行 Java 方法 （也就是字节码）服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。** 在 HotSpot 虚拟机中和 Java 虚拟机栈合二为一。

### 一句话简单了解堆和方法区

**堆和方法区是所有线程共享的资源**，

- 堆是进程中最大的一块内存，主要用于**存放新创建的对象** (几乎所有对象都在这里分配内存)
- 方法区主要用于**存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据**。

## 说说线程的生命周期和状态?

### 生命周期

![Java 线程的状态 ](.\pic\Java线程的状态.png)

1. 线程创建之后它将处于 **NEW（新建）** 状态

2. 调用 `start()` 方法后开始运行， JVM 层面看到 RUNNABLE 状态，操作系统层面：

   * 线程这时候处于 **READY（可运行）** 状态。

   * 可运行状态的线程获得了 CPU 时间片（timeslice）后就处于 **RUNNING（运行）** 状态。

3. 线程进入等待状态

   * **WAITING（等待线程）：依靠其他线程的通知才能够返回到运行状态**。执行 `wait()`方法之后，需要**别的线程**调用同一个对象上的 `notify() `或者 `notifyAll()` 方法，
   * **TIMED_WAITING(超时等待)：当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态**。比如 `sleep（long millis）`或 `wait（long millis）`

4. 当线程**调用同步方法**时（资源），在没有获取到锁的情况下，线程将会进入到 **BLOCKED（阻塞）** 状态。

5. 线程在执行 Runnable 的`run()`方法之后将会进入到 **TERMINATED（终止）** 状态。

Java 线程状态变迁如下图所示

![Java 线程状态变迁 ](.\pic\Java+线程状态变迁.png)

### 什么是上下文切换?

 **上下文切换**：发生线程切换时

1. 保存当前线程的运行条件和状态（也称上下文，比如程序计数器，栈信息等），留待线程下次占用 CPU 的时候恢复现场
2. 线程会从占用 CPU 状态中退出，并加载下一个将要占用 CPU 的线程上下文。

发生情景：

- 主动让出 CPU，比如调用了 `sleep()`, `wait()` 等。
- 时间片用完，因为操作系统要防止一个线程或者进程长时间占用CPU导致其他线程或者进程饿死。
- 调用了阻塞类型的系统中断，比如请求 IO，线程被阻塞。
- 被终止或结束运行

上下文切换是现代操作系统的基本功能，因其每次需要保存信息恢复信息，这将会占用 CPU，内存等系统资源进行处理，也就意味着效率会有一定损耗，**如果频繁切换就会造成整体效率低下**。

### 说说 sleep() 方法和 wait() 方法区别和共同点?

- 两者都可以暂停线程的执行。
- 两者最主要的区别在于：**`sleep()` 方法没有释放锁，而 `wait()` 方法释放了锁** 。
- `wait()` 通常被用于线程间交互/通信，`sleep() `通常被用于暂停执行。
- `wait()` 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 `notify() `或者 `notifyAll()` 方法。`sleep() `方法执行完成后，线程会自动苏醒。或者可以使用 `wait(long timeout)` 超时后线程会自动苏醒。

### 为什么我们调用 start() 方法时会执行 run() 方法，为什么我们不能直接调用 run() 方法？

**总结： 调用 `start()` 方法方可启动线程并使线程进入就绪状态，直接执行 `run()` 方法的话不会以多线程的方式执行。**

调用 `start()`方法，会**启动一个线程并使线程进入了就绪状态**，当**分配到时间片后就可以开始运行**了。 

**`start()` 会执行线程的相应准备工作**，然后自动执行 `run()` 方法的内容，这是真正的多线程工作。

直接执行 `run()` 方法并不是多线程工作，会把 `run()` 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它。

## 什么是线程死锁?如何避免死锁?

### 认识线程死锁

多个线程同时被阻塞，它们中的**一个或者全部都在等待某个资源被释放**。由于线程被无限期地阻塞，因此程序不可能正常终止。

产生死锁的四个**必要条件**：

1. 互斥条件：该资源任意一个时刻只由一个线程占用。
2. 请求与保持条件：一个线程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件:线程已获得的资源在未使用完之前不能被其他线程强行剥夺，只有自己使用完毕后才释放资源。
4. 循环等待条件:若干线程之间形成一种头尾相接的循环等待资源关系。

### 如何预防和避免线程死锁?

**如何预防死锁？** 破坏死锁的产生的必要条件即可：

1. **破坏请求与保持条件** ：一次性申请所有的资源。
2. **破坏不剥夺条件** ：占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源。
3. **破坏循环等待条件** ：靠按序申请资源来预防。按某一顺序申请资源，释放资源则反序释放。破坏循环等待条件。

**如何避免死锁？**

避免死锁就是在资源分配时，**借助于算法（比如银行家算法）对资源分配进行计算评估，使其进入安全状态。**

> **安全状态** 指的是系统能够按照某种线程推进顺序（P1、P2、P3.....Pn）来为每个线程分配所需资源，直到满足每个线程对资源的最大需求，使每个线程都可顺利完成。称<P1、P2、P3.....Pn>序列为安全序列。



## 1.synchronized 关键字

### 1.1.说一说自己对于 synchronized 关键字的了解

**`synchronized` 关键字解决的是多个线程之间访问资源的同步性**

**`synchronized`关键字可以保证被它修饰的方法或者代码块在任意时刻只能有一个线程执行。**

### 1.2. 说说自己是怎么使用 synchronized 关键字

**synchronized 关键字最主要的三种使用方式：**

**1.修饰实例方法:** 作用于当前对象实例加锁，进入同步代码前要获得 **当前对象实例的锁**

**2.修饰静态方法:**

- **作用于类的所有对象实例** ：**静态成员不属于任何一个实例对象，是类成员**（ _static 表明这是该类的一个静态资源，**不管 new 了多少个对象，只有一份**_）。
- 实例的锁（非静态 `synchronized` 方法）和类的锁（静态 `synchronized` 方法）不互斥

**3.修饰代码块** ：指定加锁对象，对给定对象/类加锁。`synchronized(this|object){}` 表示进入同步代码库前要获得**给定对象的锁**。`synchronized(类.class)` 表示进入同步代码前要获得 **当前 class 的锁**

**总结：**

- `synchronized` 关键字加到 `static` 静态方法和 `synchronized(class)` 代码块上都是是给 Class 类上锁。
- `synchronized` 关键字加到实例方法上是给对象实例上锁。
- 尽量不要使用 `synchronized(String a)` 因为 JVM 中，字符串常量池具有缓存功能！

#### 单例模式

> 保证一个类仅有一个实例，并提供一个访问它的全局访问点。

单例模式（Singleton）的目的是为了保证在一个进程中，某个类有且仅有一个实例。

因为这个类只有一个实例，**单例的构造方法必须是`private`，**这样就防止了调用方自己创建实例

实现方式：

1. 只有`private`构造方法，确保外部无法实例化；
2. 通过`private static`变量持有唯一实例，保证全局唯一性；
3. 通过`public static`方法返回此唯一实例，使外部调用方能获取到实例。
4. 序列化和反序列化会绕过普通类的`private`构造方法从而创建出多个实例，而枚举类就没有这个问题。

#### 延迟加载

即在调用方第一次调用`getInstance()`时才初始化全局唯一实例，在多线程中竞争条件下会创建出多个实例。必须对整个方法进行加锁，但加锁会严重影响并发性能，所以有双重检查，要真正实现延迟加载，只能通过Java的ClassLoader机制完成。

那我们什么时候应该用Singleton呢？实际上，很多程序，尤其是Web程序，大部分服务类都应该被视作Singleton，如果全部按Singleton的写法写，会非常麻烦，所以，通常是通过约定让框架（例如Spring）来实例化这些类，保证只有一个实例，调用方自觉通过框架获取实例而不是`new`操作符：

```
@Component // 表示一个单例组件
public class MyService {
    ...
}
```

因此，除非确有必要，否则Singleton模式一般以“约定”为主，不会刻意实现它。

**双重校验锁实现对象单例（线程安全）**

```java
public class Singleton {
    //使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。
    private volatile static Singleton uniqueInstance;
    private Singleton() {}
    public  static Singleton getUniqueInstance() {
       //先判断对象是否已经实例过，没有实例化过才进入加锁代码
        if (uniqueInstance == null) {
            //类对象加锁
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```



### 1.3. 构造方法可以使用 synchronized 关键字修饰么？

**构造方法不能使用 synchronized 关键字修饰。**

构造方法本身就属于线程安全的，不存在同步的构造方法一说。

### 1.3. 讲一下 synchronized 关键字的底层原理

**synchronized 关键字底层原理属于 JVM 层面。**

**两者的本质都是对对象监视器 monitor 的获取。**

**`synchronized` 同步语句块**：使用 `monitorenter` 和 `monitorexit` 指令，其中 `monitorenter` 指令指向同步代码块的开始位置，`monitorexit` 指令则指明同步代码块的结束位置。

**`synchronized` 修饰的方法**：使用`ACC_SYNCHRONIZED` 标识，该标识指明了该方法是一个同步方法，JVM从而执行相应的同步调用。

每个对象中都内置了一个 `ObjectMonitor`对象,当执行 `monitorenter` 指令时:

1. 线程试图获取锁也就是获取 **对象监视器 `monitor`** 的持有权。

2. 如果锁的计数器为 0 则表示锁可以被获取

   * 获取后将锁计数器设为 1 也就是加 1。

   * 对象锁的的拥有者线程才可以执行 `monitorexit` 指令来释放锁。在执行 `monitorexit` 指令后，将锁计数器设为 0，表明锁被释放，其他线程可以尝试获取锁。

3. 如果获取对象锁失败，那当前线程就要阻塞等待，直到锁被另外一个线程释放为止。

### 1.4. 说说 JDK1.6 之后的 synchronized 关键字底层做了哪些优化，可以详细介绍一下这些优化吗

JDK1.6 对锁的实现**引入了大量的优化**，如偏向锁、轻量级锁、自旋锁、适应性自旋锁、锁消除、锁粗化等技术**来减少锁操作的开销**。

锁主要存在四种状态，依次是：无锁状态、偏向锁状态、轻量级锁状态、重量级锁状态，他们会随着竞争的激烈而逐渐升级。注意锁可以升级不可降级，这种策略是为了提高获得锁和释放锁的效率。

关于这几种优化的详细信息可以查看下面这篇文章：[Java6 及以上版本对 synchronized 的优化](https://www.cnblogs.com/wuqinglong/p/9945618.html)

### 1.5. 谈谈 synchronized 和 ReentrantLock 的区别

#### 1.5.1. 两者都是可重入锁

**“可重入锁”**也叫做递归锁，是指在**一个线程中可以多次获取同一把锁**。

java线程是基于“每线程（per-thread）”，而不是基于“每调用的（per-invocation）”的，也就是说java为每个线程分配一个锁，而不是为每次调用分配一个锁。

##### 可重入锁的实现原理

为每个锁关联一个**获取计数器和一个所有者线程**，当计数值为0的时候，这个锁就没有被任何线程持有。

当线程请求加锁时，需要判断锁是否已经被获取：

- 未被持有的锁时，JVM将记下锁的持有者，并且将获取计数值置为1，如果同一个线程再次获取这个锁，计数值将递增，退出一次同步代码块，计算值递减。
- 如果已经被获取，则判断获取锁的线程是否是当前线程。如果是当前线程，则给获取次数加1。如果不是当前线程，则需要等待。

释放锁时，需要给锁的获取次数减1。

如果次数为0了，则需要调用锁的唤醒方法，让锁上阻塞的其他线程得到执行的机会。

如果是不可重入锁的话，就会造成死锁。

#### 1.5.2.synchronized 依赖于 JVM 而 ReentrantLock 依赖于 API

`synchronized` 是依赖于 JVM 实现的，**在虚拟机层面实现的，并没有直接暴露给我们**。`ReentrantLock` 是 JDK 层面实现的（也就是 API 层面，需要 lock() 和 unlock() 方法配合 try/finally 语句块来完成）

#### 1.5.3.ReentrantLock 比 synchronized 增加了一些高级功能

相比`synchronized`，`ReentrantLock`增加了一些高级功能。主要来说主要有三点：

- **等待可中断** : 通过 `lock.lockInterruptibly()` ，正在等待的线程可以选择放弃等待，改为处理其他事情。
- **可实现公平锁** : `ReentrantLock`**可以指定是公平锁还是非公平锁**。而`synchronized`只能是非公平锁。所谓的**公平锁就是先等待的线程先获得锁**。`ReentrantLock`**默认情况是非公平的**，可以通过 `ReentrantLock`类的`ReentrantLock(boolean fair)`构造方法来制定是否是公平的。
- **可实现选择性通知（锁可以绑定多个条件）**: `synchronized`关键字与`wait()`和`notify()`/`notifyAll()`方法相结合可以实现等待/通知机制。`ReentrantLock`类当然也可以实现，但是需要借助于`Condition`接口与`newCondition()`方法。

> `Condition`是 JDK1.5 之后才有的，它具有很好的灵活性，比如可以实现多路通知功能也就是在一个`Lock`对象中可以创建多个`Condition`实例（即对象监视器），**线程对象可以注册在指定的`Condition`中，从而可以有选择性的进行线程通知，在调度线程上更加灵活。 在使用`notify()/notifyAll()`方法进行通知时，被通知的线程是由 JVM 选择的，用`ReentrantLock`类结合`Condition`实例可以实现“选择性通知”** ，这个功能非常重要，而且是 Condition 接口默认提供的。而`synchronized`关键字就相当于整个 Lock 对象中只有一个`Condition`实例，所有的线程都注册在它一个身上。如果执行`notifyAll()`方法的话就会通知所有处于等待状态的线程这样会造成很大的效率问题，而`Condition`实例的`signalAll()`方法 只会唤醒注册在该`Condition`实例中的所有等待线程。

**如果你想使用上述功能，那么选择 ReentrantLock 是一个不错的选择。性能已不是选择标准**

## 2. volatile 关键字

`volatile`关键字的目的是告诉虚拟机：

- 每次访问变量时，总是获取主内存的最新值；
- 每次修改变量后，立刻回写到主内存。

线程间共享变量需要使用`volatile`关键字标记，**确保每个线程都能读取到更新后的变量值。**

Java虚拟机中，变量的值保存在主内存中，但是，当线程访问变量时，它会先获取一个副本，并保存在自己的工作内存中。如果线程修改了变量的值，虚拟机会在某个时刻把修改后的值回写到主内存，但是，这个时间是不确定的！

####  JVM 指令重排的特性

`uniqueInstance` 采用 `volatile` 关键字修饰也是很有必要的， `uniqueInstance = new Singleton();` 这段代码其实是分为三步执行：

1. 为 `uniqueInstance` 分配内存空间
2. 初始化 `uniqueInstance`
3. 将 `uniqueInstance` 指向分配的内存地址

但是由于 JVM 具有指令重排的特性，执行顺序有可能变成 1->3->2。指令重排在单线程环境下不会出现问题，但是在**多线程环境下会导致一个线程获得还没有初始化的实例**。例如，线程 T1 执行了 1 和 3，此时 T2 调用 `getUniqueInstance`() 后发现 `uniqueInstance` 不为空，因此返回 `uniqueInstance`，但此时 `uniqueInstance` 还未被初始化。

使用 `volatile` 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。

### 2.1. CPU 缓存模型

**为什么要弄一个 CPU 高速缓存呢？**

**CPU 缓存则是为了解决 CPU 处理速度和内存处理速度不对等的问题。**

**内存缓存的是硬盘数据用于解决硬盘访问速度过慢的问题。**

**CPU Cache 的工作方式：**

先复制一份数据到 CPU Cache 中，当 CPU 需要用到的时候就可以直接从 CPU Cache 中读取数据，当运算完成后，再将运算得到的数据写回 Main Memory 中。

但是，这样存在 **内存缓存不一致性的问题** 

**CPU 为了解决内存缓存不一致性问题可以通过制定缓存一致协议或者其他手段来解决。**

### 2.2. 讲一下 JMM(Java 内存模型)

Java 内存模型抽象了线程和主内存之间的关系，就比如说线程之间的共享变量必须存储在主内存中。Java 内存模型主要目的是为了屏蔽系统和硬件的差异，避免一套代码在不同的平台下产生的效果不一致。

在 JDK1.2 之前，Java 的内存模型实现总是从**主存**（即共享内存）读取变量，是不需要进行特别的注意的。而在当前的 Java 内存模型下，线程可以把变量保存**本地内存**（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成**数据的不一致**。

> - **主内存** ：所有线程创建的实例对象都存放在主内存中，不管该实例对象是成员变量还是方法中的本地变量(也称局部变量)
> - **本地内存** ：每个线程都有一个私有的本地内存来存储共享变量的副本，并且，每个线程只能访问自己的本地内存，无法访问其他线程的本地内存。本地内存是 JMM 抽象出来的一个概念，存储了主内存中的共享变量副本。

要解决这个问题，就需要把变量声明为 **`volatile`** ，这就指示 JVM，这个变量是共享且不稳定的，每次使用它都到主存中进行读取。

所以，**`volatile` 关键字 除了防止 JVM 的指令重排 ，还有一个重要的作用就是保证变量的可见性。**

### 2.3. 并发编程的三个重要特性

1. **原子性** : 一次操作或者多次操作，**要么所有的操作全部都得到执行**并且不会受到任何因素的干扰而中断，**要么都不执行**。`synchronized` 可以保证代码片段的原子性。
2. **可见性** ：当一个线程对共享变量进行了修改，那么另外的线程都是立即可以看到修改后的最新值。**`volatile` 关键字可以保证共享变量的可见性。**
3. **有序性** ：代码在执行的过程中的先后顺序，Java 在编译器以及运行期间的优化，代码的执行顺序未必就是编写代码时候的顺序。**`volatile` 关键字可以禁止指令进行重排序优化。**

### 2.4. 说说 synchronized 关键字和 volatile 关键字的区别

`synchronized` 关键字和 `volatile` 关键字是两个互补的存在，而不是对立的存在！

- **`volatile` 关键字**是线程同步的**轻量级实现**，所以 **`volatile `性能肯定比` synchronized `关键字要好** 。但是 **`volatile` 关键字只能用于变量而 `synchronized` 关键字可以修饰方法以及代码块** 。
- **`volatile` 关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized` 关键字两者都能保证。**
- **`volatile`关键字主要用于解决变量在多个线程之间的可见性，而 `synchronized` 关键字解决的是多个线程之间访问资源的同步性。**

## 3. ThreadLocal

### 3.1. ThreadLocal 简介

**每个`Thread`中都具备一个`ThreadLocalMap`，存储以`ThreadLocal`为 key ，Object 对象为 value 的键值对。**

如果你创建了一个`ThreadLocal`变量，那么访问这个变量的每个线程都会有这个变量的本地副本

**可以使用 `get（）` 和 `set（）` 方法来获取默认值或将其值更改为当前线程所存的副本的值，从而避免了线程安全问题。**

### 3.4. ThreadLocal 内存泄露问题

`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用,而 value 是强引用。

`ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。这样一来，`ThreadLocalMap` 中就会出现 key 为 null 的 Entry。

假如我们不做任何措施的话，value 永远无法被 GC 回收，这个时候就可能会产生内存泄露。ThreadLocalMap 实现中已经考虑了这种情况，在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 key 为 null 的记录。

**使用完 `ThreadLocal`方法后 最好手动调用`remove()`方法**

## 4. 线程池

### 4.1. 为什么要用线程池？

> **池化技术的思想主要是为了减少每次获取资源的消耗，提高对资源的利用率。**

**线程池**提供了一种限制和管理资源（包括执行一个任务）的方式。 每个**线程池**还维护一些基本统计信息，例如已完成任务的数量。

**使用线程池的好处**：

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。当任务到达时，任务可以不需要等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

### 4.2. 实现 Runnable 接口和 Callable 接口的区别

`Callable`引入目的是处理`Runnable`不支持的用例。

**`Runnable` 接口** **不会返回结果或抛出检查异常**，但是 **`Callable` 接口** 可以。所以，如果任务不需要返回结果或抛出异常推荐使用 **`Runnable` 接口** ，这样代码看起来会更加简洁。

工具类 `Executors` 可以实现将 `Runnable` 对象转换成 `Callable` 对象。（`Executors.callable(Runnable task)` 或 `Executors.callable(Runnable task, Object result)`）。

### 4.3. 执行 execute()方法和 submit()方法的区别是什么呢？

1. **`execute()`方法用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功与否；**
2. **`submit()`方法用于提交需要返回值的任务。线程池会返回一个 `Future` 类型的对象，通过这个 `Future` 对象可以判断任务是否执行成功**，并且可以通过 `Future` 的 `get()`方法来获取返回值，`get()`方法会**阻塞**当前线程直到任务完成，而使用 `get(long timeout，TimeUnit unit)`方法则会**阻塞当前线程一段时间**后立即返回，这时候有可能任务没有执行完。

### 4.4. 如何创建线程池

**方式一：通过构造方法实现**，`ExecutorService`接口

**方式二：通过 Executor 框架的工具类 Executors 来实现**



