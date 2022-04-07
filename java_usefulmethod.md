我的笔记全没了！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！

[TOC]

# Scanner 输入控制

```
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in); // 创建Scanner对象
        System.out.print("Input your name: "); // 打印提示
        String name = scanner.nextLine(); // 读取一行输入并获取字符串
        System.out.print("Input your age: "); // 打印提示
        int age = scanner.nextInt(); // 读取一行输入并获取整数
        System.out.printf("Hi, %s, you are %d\n", name, age);
         // 格式化输出
    }
}
```

1. import导入java.util.Scanner
2. 创建Scanner对象并传入System.in。
   ` Scanner scanner = new Scanner(System.in);`
   System.out代表标准输出流，而System.in代表标准输入流。
3. 读取用户输入的字符串，使用scanner.nextLine()，
   读取输入的整数，scanner.nextInt()。Scanner会自动转换数据类型，因此不必手动转换。

## String -> List\<String>

```
List<String> list = Arrays.stream(split)
          .collect(Collectors.toList());
```

## String->int\[]

```
        String[] split = in.nextLine().split(" ");
        int[] jobs = new int[split.length];
        for (int i = 0; i < split.length; i++) {
            jobs[i] = Integer.parseInt(split[i]);
        }
        Arrays.sort(jobs);
```



## 数组类型转换

#### int[]

```
int[] example1 = list.stream().mapToInt(i->i).toArray();
// OR
int[] example2 = list.stream().mapToInt(Integer::intValue).toArray();
//输入直接转换
int[] nums= Arrays.stream(inp.nextLine().split(","))
            .map(Integer::parseInt).mapToInt(Integer::intValue).toArray();
```

#### Integer[]

```
Integer[] input= Arrays.stream(inp.nextLine().split("\\s+"))
          .map(Integer::parseInt).toArray(Integer[]::new);
```

#### List\<Integer>

```
List<Integer> numms= Arrays.stream(in.nextLine().split(","))
                    .map(Integer::parseInt).collect(Collectors.toList());
```

#### List\<String>

```
List<String> ids= Arrays.stream(inp.nextLine().split(","))
                .collect(Collectors.toList());
```

#### List类型顺便排序：

```
List<String> ids= Arrays.stream(inp.nextLine().split(",")).sorted((Comparator<String>) (o1 , o2) -> {
            int o1len = o1.length(), o2len = o2.length();
            int temp1, temp2;
            for (int i = 0; i < o1.length() + o2.length(); i++) {
                if (i >= o1len) {
                    temp1 = o2.charAt(i - o1len);
                } else temp1 = o1.charAt(i);
                if (i >= o2len) {
                    temp2 = o1.charAt(i - o2len);
                } else temp2 = o2.charAt(i);
                if (temp1 == temp2) continue;
                return temp2 - temp1;
            }
            return 0;
        }).collect(Collectors.toList());
```

#### 数组内容合格检测

```
boolean err=Arrays.stream(str.split(""))//string分成单个字符的string[]
                .allMatch(
                istr -> (istr.compareTo("A") >= 0 && istr.compareTo("Z") <= 0)
                        || ((istr.compareTo("z") <= 0) && (istr.compareTo("a") >= 0)));
```

### 数组复制

#### 使用Arrays.copyOf[Range函数]

`int[] arr = Arrays.copyOf(sourceArray, sourceArray.length);`<br>
Java的数组有几个特点：

1. 数组所有元素初始化为默认值，整型都是0，浮点型是0.0，布尔型是false；
2. 数组一旦创建后，**大小就不可改变**。
3. 要访问数组中的某一个元素，需要使用索引。数组索引从0开始，例如，5个元素的数组，索引范围是0~4。
4. 可以修改数组中的某一个元素，使用赋值语句，例如，ns[1] = 79;。
5. 可以用数组变量.length获取数组大小：  
6. **数组是引用类型**，在使用索引访问数组元素时，如果索引超出范围，运行时将报错：

#### 使用subList

```java
import java.util.ArrayList;

public class hello {
    public static void main(String[] args) {
        // create an empty array list
        ArrayList<String> color_list = new ArrayList<String>();

        // use add() method to add values in the list
        color_list.add("White");
        color_list.add("Black");
        color_list.add("Red");

        //Return portion of the list : fromindex(inclusive)->1,  toindex(exclusive)->3
        ArrayList<String> new_color_list1 = new ArrayList<String>(color_list.subList(1, 3));
    }
}

```

# Character

```
//字符串转字符数组
char[] chars = line.toCharArray();
    int sum = 0;

char c = chars[i];
//判断是否是数字
Character.isDigit(chars[i]));
//转数字
sum = Character.digit(c, 10);//第二个radix基数，是最大的意思？c>radix的时候返回-1
```

# String

```
//切割
String substring = line.substring(start, end);
//判断相等
substring.equals(p)
//转数字
sum = Integer.parseInt(substring);
//类转数组
StringBuilder builder = new StringBuilder();
stus.forEach(x -> builder.append(x.id).append(" "));
```



# 有序数组-排列

```
List<Integer> candidates = new ArrayList<>();

    int[] factorials = new int[n + 1];
    factorials[0] = 1;
    int fact = 1;
    for (int i = 1; i <= n; ++i) {
      candidates.add(i);
      fact *= i;
      factorials[i] = fact;
    }
    k -= 1;
    for (int i = n - 1; i >= 0; --i) {
      // 计算候选数字的index
      int index = k / factorials[i];
      sb.append(candidates.remove(index));
      k -= index * factorials[i];
    }
    System.out.println(sb);
  }
}
```

# 排序lambda

```
    //linklist
    times.sort(Comparator.comparingLong(Main90::getTime));

    times.forEach(System.out::println);
    //赋值
    ints[i] = "present".equals(day.get(i)) ? 0 : 1;
```

# 排序类

```
    static class Stu {
        int id;
        int h;
        int w;

        public Stu(int id, int h, int w) {
            this.id = id;
            this.h = h;
            this.w = w;
        }
    }

    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = Integer.parseInt(in.nextLine());
        String[] h = in.nextLine().split(" ");
        String[] w = in.nextLine().split(" ");
        in.close();
        LinkedList<Stu> stus = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            Stu stu = new Stu(i + 1, Integer.parseInt(h[i]), Integer.parseInt(w[i]));
            stus.add(stu);
        }
        stus.sort((o1, o2) -> o1.h == o2.h ? (o1.w - o2.w) : o1.h - o2.h);
        StringBuilder builder = new StringBuilder();
        stus.forEach(x -> builder.append(x.id).append(" "));
        System.out.println(builder.substring(0, builder.length() - 1));
    }
```

# Map

构造

排序：

使用stream排序：

- 对map的entryset的 list排序：map.entryset().stream.sorted((o1,o2)->o1.k==o2.k?o1.v-o2.v:o1.k-o2.k)（当不是数字时用o1.compareTO(o2)），使用collect(Collector.toList)或者生产Arraylist类，里面加一个Arraylist::new
- 对values排序：使用list：最后直接toarray
- 用distinct去除重复的

```
map.put(cur, map.containsKey(cur) ?
                    map.get(cur) > count ? map.get(cur) : count :
                    count);
//降序+字符串切割排序
        for (Map.Entry<Character, Integer> entry : map.entrySet()) {
            list.add(entry.getKey() + "-" + entry.getValue());
        }

        list.sort(new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o2.split("-")[1].compareTo(o1.split("-")[1]);
            }
        });
```



# TreeSet自动排序

`TreeSet` 是继承`AbstractSet` 有序集合并且实现`NavigableSet` 接口

下面是一些重要总结:

- 不能存放相同元素
- 不保存插入元素顺序
- 默认排序是升序(treeSet#add()增加元素小在左边 大在右边 迭代从左边读取)
- 非线程安全

`TreeSet`实现中，对象是按照自然序升序存储，`TreeSet` 使用平衡二叉搜索树，简单说平衡二叉搜索树，每个节点有额外字节做特殊标记，标记红色和黑色，在删除和插入时候，标记字节确保剩下数还是**平衡树。**

创建一个`TreeSet`实例

`Set<String> treeSet = new TreeSet();`

## 例子

```
//每次重构tree，第一个都是最小的，还能避免相同元素丢失
for (int i = m; i < jobs.length; i++) {
            Integer min = new ArrayList<>(new TreeSet<>(res)).get(0);
            int index = res.indexOf(min);
            res.set(index, res.get(index) + jobs[i]);
        }
```



### TreeSet带参数构造方法

带参数构造方法可以自定义排序方法

```java
Set<String> treeSet = new TreeSet(Comparator.comparing(String::length));
```

**即使`TreeSet`  线程不安全 ，在外部可以使用Collections.synchronizedSet()包装起来**

```java
  Set<String> syncTreeSet = Collections.synchronizedSet(treeSet);
```

##  TreeSet 增加元素 add()

`TreeSet#add()`可以增加元素，元素增加成功放回true否则false

**add()方法增加只有TreeSet不存相同元素** ，下面例子:



```java
@Test
public void whenAddingElement_shouldAddElement() {
    Set<String> treeSet = new TreeSet();
 
    assertTrue(treeSet.add("String Added"));
 }
```



### TreeSet contains()

**contains()方法用于检查给定元素是否存在TreeSet中**,如果元素存在返回true，否则false，下面例子:



```java
@Test
public void whenCheckingForElement_shouldSearchForElement() {
    Set<String> treeSetContains = new TreeSet();
    treeSetContains.add("String Added");
 
    assertTrue(treeSetContains.contains("String Added"));
}
```



## TreeSet remove()

**remove()方法删除指定元素如果存在** ，如果存在元素方法返回true，下面例子:



```java
@Test
public void whenRemovingElement_shouldRemoveElement() {
    Set<String> removeFromTreeSet = new TreeSet();
    removeFromTreeSet.add("String Added");
 
    assertTrue(removeFromTreeSet.remove("String Added"));
```



### TreeSet clear()

如果删除所有元素,可以使用clear()方法:



```java
@Test
public void whenClearingTreeSet_shouldClearTreeSet() {
    Set<String> clearTreeSet = new TreeSet();
    clearTreeSet.add("String Added");
    clearTreeSet.clear();
  
    assertTrue(clearTreeSet.isEmpty());
}
```

### 7: TreeSet size()

size()方法用来计算`TreeSet`中大小，这个是基本方法在接口中:



```java
Test
public void whenCheckingTheSizeOfTreeSet_shouldReturnThesize() {
    Set<String> treeSetSize = new TreeSet();
    treeSetSize.add("String Added");
  
    assertEquals(1, treeSetSize.size());
}
```

### 8:  TreeSet isEmpty()

`isEmpty()` 方法判断`TreeSet` 是否为空



```java
@Test
public void whenCheckingForEmptyTreeSet_shouldCheckForEmpty() {
    Set<String> emptyTreeSet = new TreeSet();
     
    assertTrue(emptyTreeSet.isEmpty());
}
```

### 9: TreeSet iterator()

`iterator()` 方法返回升序迭代器,迭代器实现fail-fast失败，下面观察升序迭代器:



```java
@Test
public void whenIteratingTreeSet_shouldIterateTreeSetInAscendingOrder() {
    Set<String> treeSet = new TreeSet();
    treeSet.add("First");
    treeSet.add("Second");
    treeSet.add("Third");
    Iterator<String> itr = treeSet.iterator();
    while (itr.hasNext()) {
        System.out.println(itr.next());
    }
}
```

另外，`TreeSet`可以降序排序输出，下面例子:



```java
@Test
public void whenIteratingTreeSet_shouldIterateTreeSetInDescendingOrder() {
    TreeSet<String> treeSet = new TreeSet();
    treeSet.add("First");
    treeSet.add("Second");
    treeSet.add("Third");
    Iterator<String> itr = treeSet.descendingIterator();
    while (itr.hasNext()) {
        System.out.println(itr.next());
    }
}
```

**迭代器抛出ConcurrentModificationException如果set被修改在迭代创建之后，除非使用迭代器删除remove()方法**，下面例子:



```java
@Test(expected = ConcurrentModificationException.class)
public void whenModifyingTreeSetWhileIterating_shouldThrowException() {
    Set<String> treeSet = new TreeSe();
    treeSet.add("First");
    treeSet.add("Second");
    treeSet.add("Third");
    Iterator<String> itr = treeSet.iterator();
    while (itr.hasNext()) {
        itr.next();
        treeSet.remove("Second");
    }
}
```

如果使用迭代器删除不会抛出异常



```java
@Test
public void whenRemovingElementUsingIterator_shouldRemoveElement() {
  
    Set<String> treeSet = new TreeSet();
    treeSet.add("First");
    treeSet.add("Second");
    treeSet.add("Third");
    Iterator<String> itr = treeSet.iterator();
    while (itr.hasNext()) {
        String element = itr.next();
        if (element.equals("Second"))
           itr.remove();
    }
  
    assertEquals(2, treeSet.size());
}
```

**在并发时候很难保证不被修改在迭代时候**



作者：左洁
链接：https://www.jianshu.com/p/8ee62500798d
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
