# 流程控制     
## scanner
```
import java.util.Scanner;

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
## 数据类型转换
数据类型的转换是在所赋值的数值类型和被变量接收的数据类型不一致时发生的，它需要从一种数据类型转换成另一种数据类型。数据类型的转换可以分为隐式转换（自动类型转换）和显式转换（强制类型转换）两种。

### 隐式转换（自动类型转换）
条件<br>
1. 两种数据类型彼此兼容
2. 目标类型的取值范围大于源数据类型（低级类型数据转换成高级类型数据）
当以上 2 个条件都满足时，拓宽转换（widening conversion）发生。例如 byte 类型向 short 类型转换时，由于 short 类型的取值范围较大，会自动将 byte 转换为 short 类型。

在运算过程中，由于不同的数据类型会转换成同一种数据类型，所以整型、浮点型以及字符型都可以参与混合运算。自动转换的规则是从低级类型数据转换成高级类型数据。转换规则如下：<br>
* 当以上 2 个条件都满足时，拓宽转换（widening conversion）发生。例如 byte 类型向 short 类型转换时，由于 short 类型的取值范围较大，会自动将 byte 转换为 short 类型。
* 数值型数据的转换：byte→short→int→long→float→double。
* 字符型转换为整型：char→int。显式转换（强制类型转换）
### 强制转换
当两种数据类型不兼容，或目标类型的取值范围小于源类型时，自动转换将无法进行，这时就需要进行强制类型转换。其语法格式如下：<br>
`(type)variableName`<br>
其中，type 为 variableName 要转换成的数据类型，而 variableName 是指要进行类型转换的变量名称，强制转换的实例如下：<br>
```
int a = 3;
double b = 5.0;
a = (int)b;
```

### int -> string
* 第一种方法：s=i+"";
* 第二种方法：s=String.valueOf(i);
* 第三种方法：s=Integer.toString(i)

当object为null时该方法返回"null"，而非null!!!:采用Integer.toString()的基础仍是Object#toString()，因为java.lang.Object类中已有public方法toString()，所以对任何严格意义上的Java对象都可以调用此方法，但使用时需要注意，必须保证object不是null值，否则将会抛出NullPointerException异常！！！
### string -> int
- (1)Integer.parseInt(str)	
- (2)Integer.valueOf(str).intValue()
两者运行时间基本差不多。但是第二种方法，先是将String => Integer => int，即将String包装类型转化为Integer包装类型，再将包装类型转化为基本类型。

```
//String => int
String s = "123456789";
 
//(1)Integer.parseInt(str)		
for(int i=0; i<100000; i++){
	int n = Integer.parseInt(s);
}

//(2)Integer.valueOf(str).intValue()
for(int i=0; i<100000; i++){
	int n = Integer.valueOf(s).intValue();
}
```

### string list -> int list
1. Convert List<String> to Stream<String> using List.stream(). 
2. Convert Stream<String> to Stream<Integer> using Stream.map().
3. Accumulate Stream<Integer> into List<Integer> using Collectors.toList().

该字符串不包含可解析的整数Note that map() operation will throw a NumberFormatException if the string does not contain a parsable integer.

``` 
// Convert `List<String>` to `List<Integer>`
class Main
{
    public static void main(String[] args)
    {
        List<String> list = Arrays.asList( "-1" , "2", "3", "4", "5" );
 
        List<Integer> newList = list.stream()
                                    .map(s -> Integer.parseInt(s))
                                    .collect(Collectors.toList());
 
        System.out.println(newList);        // [-1, 2, 3, 4, 5]
    }
}
```

**等通**<br>
```
// Convert `List<String>` to `List<Integer>`
class Main
{
    public static void main(String[] args)
    {
        List<String> list = Arrays.asList( "-1" , "2", "3", "4", "5" );
 
        List<Integer> newList = new ArrayList<>();
        for (String s : list) {
            newList.add(Integer.parseInt(s));
        }
 
        System.out.println(newList);        // [-1, 2, 3, 4, 5]
    }
}
```