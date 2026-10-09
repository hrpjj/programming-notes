# Java 基础语法复习笔记：从入门到核心语法

> 这篇文章面向已经学过 Java、但知识体系不牢固的读者。主体示例兼容 **Java 8**；较新的语法会单独标明最低版本。

@[TOC](Java 基础语法复习目录)

**建议阅读路线：**

- 只想恢复语法手感：第 2～6 章。
- 复习面向对象与考试重点：第 7～10 章。
- 复习集合、文件和工程组织：第 11～13 章。
- 了解 Java 8 函数式语法和新版特性：第 14～15 章。

> **代码说明：** 标有“完整示例”的代码可以按给出的文件名直接编译运行；“代码片段”需要放进类、方法或 `main` 方法的对应位置；“接上例”表示依赖前一段已经定义的类型或变量。

---

## 1. Java 程序如何运行

本章只保留阅读后文必须知道的运行模型：Java 源码如何变成 JVM 可以执行的字节码。

### 1.1 JDK、JRE 与 JVM

| 名称 | 作用 | 主要内容 |
|:---:|:---|:---|
| JVM | 运行 Java 字节码 | 类加载、执行字节码、内存管理 |
| JRE | 提供 Java 运行环境 | JVM + Java 核心类库 |
| JDK | 提供 Java 开发环境 | JRE + `javac`、`java`、`javadoc` 等工具 |

> **版本说明：** “JDK = JRE + 开发工具”适合作为 Java 8 的入门模型。现代 JDK 仍包含运行程序所需的组件，但从 JDK 11 起，Oracle 不再单独提供传统的 JRE 下载包。

Java 源代码先由编译器转换成字节码，再由 JVM 执行：

```text
HelloJava.java  --javac-->  HelloJava.class  --java/JVM-->  程序结果
```

### 1.2 第一个完整程序

**完整示例：保存为 `HelloJava.java`，可直接运行。**

```java
// File: HelloJava.java
public class HelloJava {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

在源文件所在目录执行：

```bash
javac HelloJava.java
java HelloJava
```

输出：

```text
Hello, Java!
```

代码结构说明：

- `public class HelloJava`：声明一个公共类，公共类名必须与文件名一致。
- `public static void main(String[] args)`：Java 程序入口。
- `System.out.println()`：输出内容并换行。
- 每条普通语句通常以分号 `;` 结束，代码块使用 `{}` 包围。

### 1.3 注释

注释不会参与程序执行，记住三种形式即可：

| 写法 | 用途 | 示例 |
|:---|:---|:---|
| `// ...` | 单行说明 | `// 计算总分` |
| `/* ... */` | 多行说明 | 临时解释一段逻辑 |
| `/** ... */` | API 文档注释 | 配合 `@param`、`@return` 和 `javadoc` |

### 1.4 常见命名规范

| 对象 | 推荐写法 | 示例 |
|:---|:---|:---|
| 类、接口 | 大驼峰 | `StudentService` |
| 变量、方法 | 小驼峰 | `studentName`、`getScore()` |
| 常量 | 全大写，下划线分隔 | `MAX_SIZE` |
| 包名 | 全小写 | `com.example.demo` |

> **注意：** 标识符不能以数字开头，不能使用 Java 关键字，并且严格区分大小写。

---

## 2. 变量、数据类型、运算符与输入输出

本章建立阅读 Java 代码所需的最小语法基础，重点是类型、转换规则和输入输出。

### 2.1 变量、常量与作用域

**代码片段：放入方法或 `main` 方法。**

```java
int age = 20;                 // 声明并初始化变量
age = 21;                     // 修改变量

final double PI = 3.1415926;  // final 变量只能赋值一次
```

变量只在声明它的代码块中有效：

```java
public static void showScope() {
    int outside = 10;

    if (outside > 0) {
        int inside = 20;
        System.out.println(outside + inside);
    }

    // System.out.println(inside); // 编译错误：inside 已离开作用域
}
```

### 2.2 八种基本数据类型

**类型与范围：**

| 类型 | 位数 | 典型范围或取值 |
|:---:|:---:|:---|
| `byte` | 8 | -128～127 |
| `short` | 16 | -32768～32767 |
| `int` | 32 | 约 ±21 亿 |
| `long` | 64 | 很大的整数 |
| `float` | 32 | 单精度小数 |
| `double` | 64 | 双精度小数 |
| `char` | 16 | 一个 UTF-16 代码单元 |
| `boolean` | - | `true` 或 `false` |

**字面量与字段默认值：**

| 类型 | 字面量示例 | 字段默认值 |
|:---:|:---|:---:|
| `byte` | `(byte) 100` | `0` |
| `short` | `(short) 20000` | `0` |
| `int` | `100` | `0` |
| `long` | `100L` | `0L` |
| `float` | `3.14F` | `0.0F` |
| `double` | `3.14` | `0.0D` |
| `char` | `'A'`、`'中'` | `'\u0000'` |
| `boolean` | `true` | `false` |

> **注意：** 表中的默认值只适用于字段。局部变量没有默认值，使用前必须显式赋值。

**完整示例：保存为 `PrimitiveDemo.java`。**

```java
// File: PrimitiveDemo.java
public class PrimitiveDemo {
    private int fieldNumber;       // 默认 0
    private boolean fieldFlag;     // 默认 false

    public void printValues() {
        int localNumber = 10;      // 局部变量必须初始化
        System.out.println(fieldNumber);
        System.out.println(fieldFlag);
        System.out.println(localNumber);
    }

    public static void main(String[] args) {
        new PrimitiveDemo().printValues();
    }
}
```

输出依次为 `0`、`false`、`10`，说明字段有默认值，而局部变量必须先赋值。

### 2.3 引用类型

数组、类、接口、枚举和字符串都属于引用类型。变量中保存的是对象引用，未指向对象时可以是 `null`。

```java
String name = "Alice";
int[] scores = {90, 85, 92};
StringBuilder builder = new StringBuilder("Java");
Object nobody = null;
```

这些变量保存的是对象或数组的引用；引用可以指向对象，也可以是 `null`。

### 2.4 常用字面量

下面集中展示常见进制、长整型、小数、字符和转义写法：

```java
int decimal = 26;
int binary = 0b11010;
int octal = 032;
int hexadecimal = 0x1A;

long population = 1_400_000_000L;  // 下划线提高可读性
double scientific = 1.2e3;         // 1200.0
char newline = '\n';
String path = "C:\\Users\\demo";
```

### 2.5 自动类型提升与强制转换

自动转换关注的是 Java 规定的**拓宽转换关系**，不能只按“占用位数”判断。常见顺序是 `byte → short → int → long → float → double`，`char` 可以转为 `int` 及更宽的数值类型。

```java
int number = 100;
long longNumber = number;
double decimal = longNumber;

System.out.println(decimal);  // 100.0
```

`long` 转 `float` 虽然不需要强制转换，但仍可能丢失精度：

```java
long precise = 16_777_217L;
float rounded = precise;

System.out.println((long) rounded);  // 16777216
```

从大范围转换到小范围需要强制类型转换，可能损失数据：

```java
double price = 19.99;
int integerPrice = (int) price;

int large = 130;
byte small = (byte) large;

System.out.println(integerPrice);  // 19
System.out.println(small);         // -126，发生溢出
```

表达式计算还会发生数值提升。`byte`、`short`、`char` 参与算术运算时会先提升为 `int`：

```java
byte left = 10;
byte right = 20;

// byte result = left + right; // 编译错误：结果类型是 int
int result = left + right;

left += right;  // 合法，复合赋值隐含了强制转换
System.out.println(result);  // 30
System.out.println(left);    // 30
```

字符串与数值互转：

```java
int age = Integer.parseInt("20");
double score = Double.parseDouble("92.5");
String text = String.valueOf(100);

System.out.println(age + 1);  // 21
System.out.println(score);    // 92.5
System.out.println(text);     // 100
```

### 2.6 运算符

| 类别 | 运算符 | 示例 |
|:---|:---|:---|
| 算术 | `+ - * / % ++ --` | `7 % 3` 得到 `1` |
| 比较 | `== != > < >= <=` | `age >= 18` |
| 逻辑 | `&& \|\| !` | `age >= 18 && active` |
| 赋值 | `= += -= *= /= %=` | `count += 1` |
| 三元 | `条件 ? 值1 : 值2` | `score >= 60 ? "及格" : "不及格"` |
| 位运算 | `& \| ^ ~ << >> >>>` | `5 << 1` 得到 `10` |

```java
int a = 7;
int b = 3;

System.out.println(a / b);          // 2，整数除法
System.out.println(a / (double) b); // 2.333...
System.out.println(a % b);          // 1

boolean valid = a > 0 && b > 0;
String result = valid ? "合法" : "不合法";
System.out.println(result);
```

短路逻辑运算符 `&&`、`||` 在结果已经确定时，不再计算右侧表达式：

```java
String text = null;

if (text != null && !text.isEmpty()) {
    System.out.println(text);
}
```

### 2.7 输出与格式化

下面观察 `print`、`println` 和 `printf` 的区别：

```java
String name = "Alice";
double score = 92.567;

System.out.print("不换行输出 ");
System.out.println("换行输出");
System.out.printf("姓名：%s，成绩：%.2f%n", name, score);
```

### 2.8 使用 `Scanner` 输入

**完整示例：保存为 `ScannerDemo.java`，运行后按提示输入姓名和年龄。**

```java
// File: ScannerDemo.java
import java.util.Scanner;

public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("请输入姓名：");
        String name = scanner.nextLine();

        System.out.print("请输入年龄：");
        int age = scanner.nextInt();

        System.out.printf("%s 今年 %d 岁%n", name, age);
        scanner.close();
    }
}
```

如果先调用 `nextInt()` 再调用 `nextLine()`，要先读取遗留的换行符：

```java
int age = scanner.nextInt();
scanner.nextLine();            // 消耗回车
String address = scanner.nextLine();
```

---

## 3. 条件与循环

流程控制决定代码何时执行、执行几次，以及何时提前结束。

### 3.1 `if` / `else if` / `else`

下面把分数映射为三个等级，条件按从严格到宽松的顺序判断：

```java
int score = 85;
String level;

if (score >= 90) {
    level = "优秀";
} else if (score >= 60) {
    level = "及格";
} else {
    level = "不及格";
}

System.out.println(level);  // 及格
```

### 3.2 传统 `switch`

Java 8 的传统 `switch` 支持 `byte`、`short`、`char`、`int` 及对应包装类，也支持 `String` 和枚举；不支持 `long`、`float`、`double`、`boolean`。

**代码片段：放入 `main` 方法。**

```java
int day = 2;
String dayName;

switch (day) {
    case 1:
        dayName = "星期一";
        break;
    case 2:
        dayName = "星期二";
        break;
    case 3:
        dayName = "星期三";
        break;
    default:
        dayName = "未知";
}

System.out.println(dayName);
```

当 `day` 为 `2` 时输出“星期二”。每个分支末尾的 `break` 用于结束 `switch`。

多个分支可以共享同一段逻辑：

```java
char grade = 'B';

switch (grade) {
    case 'A':
    case 'B':
        System.out.println("成绩良好");
        break;
    case 'C':
        System.out.println("成绩一般");
        break;
    default:
        System.out.println("需要继续努力");
}
```

这里 `case 'A'` 没有 `break`，会继续执行 `case 'B'` 的代码，这种行为叫 **case 穿透**。它可以有意合并分支，也可能因为漏写 `break` 造成错误。

### 3.3 `for` 循环

下面用固定次数循环计算 `1` 到 `100` 的和：

```java
int total = 0;

for (int number = 1; number <= 100; number++) {
    total += number;
}

System.out.println(total);  // 5050
```

### 3.4 增强 `for`

增强 `for` 适合只读取数组或集合中的每个元素。

```java
int[] scores = {90, 85, 92};

for (int score : scores) {
    System.out.println(score);
}
```

### 3.5 `while` 与 `do-while`

`while` 先判断再执行，`do-while` 先执行一次再判断：

```java
int count = 3;

while (count > 0) {
    System.out.println(count);
    count--;
}
```

`do-while` 至少执行一次循环体：

```java
int number = 0;

do {
    System.out.println(number);
    number++;
} while (number < 3);
```

### 3.6 `break`、`continue` 与标签

`continue` 跳过本轮剩余语句，`break` 结束整个循环。下面只输出 `1、3、5`：

```java
for (int number = 1; number <= 10; number++) {
    if (number % 2 == 0) {
        continue;
    }
    if (number > 5) {
        break;
    }
    System.out.println(number);  // 1、3、5
}
```

标签可以明确控制外层循环，下面在遇到负数时跳过整行剩余元素：

```java
int[][] values = {{1, 2}, {-1, 3}, {4, 5}};

outer:
for (int[] row : values) {
    for (int value : row) {
        if (value < 0) {
            continue outer;
        }
        System.out.println(value);  // 输出 1、2、4、5
    }
}
```

> **补充：** `break 标签名` 和 `continue 标签名` 能控制指定的外层循环，但实际项目应优先拆分方法，避免标签降低可读性。

---

## 4. 数组

数组用于保存一组相同类型、长度固定的数据；本章重点区分数组内容、数组引用和数组复制。

### 4.1 创建、访问与遍历

**完整示例：保存为 `ArrayBasicsDemo.java`。**

```java
// File: ArrayBasicsDemo.java
import java.util.Arrays;

public class ArrayBasicsDemo {
    public static void main(String[] args) {
        int[] defaults = new int[3];       // [0, 0, 0]
        int[] scores = {90, 85, 92};

        scores[1] = 88;
        System.out.println("长度：" + scores.length);

        for (int index = 0; index < scores.length; index++) {
            System.out.printf("scores[%d] = %d%n", index, scores[index]);
        }

        int total = 0;
        for (int score : scores) {
            total += score;
        }

        System.out.println("默认值：" + Arrays.toString(defaults));
        System.out.println("总分：" + total);
    }
}
```

普通 `for` 可以取得索引，增强 `for` 更适合只读取元素。数组创建后长度固定，越界访问会抛出 `ArrayIndexOutOfBoundsException`。

### 4.2 数组引用与复制

先比较“复制引用”和“复制数组内容”的不同结果：

```java
import java.util.Arrays;

int[] source = {1, 2, 3, 4};
int[] alias = source;  // 只复制引用，仍指向同一数组
alias[0] = 99;
System.out.println(source[0]);  // 99

int[] copy1 = Arrays.copyOf(source, source.length);
int[] copy2 = new int[source.length];
System.arraycopy(source, 0, copy2, 0, source.length);

source[1] = 88;
System.out.println(Arrays.toString(copy1));  // [99, 2, 3, 4]
System.out.println(Arrays.toString(copy2));  // [99, 2, 3, 4]
```

基本类型数组复制后，各数组的元素彼此独立。对象数组使用 `Arrays.copyOf()` 时只复制元素引用，属于浅复制：

```java
StringBuilder[] source = {new StringBuilder("Java")};
StringBuilder[] copy = Arrays.copyOf(source, source.length);

copy[0].append(" Core");
System.out.println(source[0]);  // Java Core
```

### 4.3 二维数组与不规则数组

二维数组的每个元素仍是一个数组，因此各行长度可以不同：

```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6}
};

for (int row = 0; row < matrix.length; row++) {
    for (int column = 0; column < matrix[row].length; column++) {
        System.out.print(matrix[row][column] + " ");
    }
    System.out.println();
}
```

创建不规则二维数组时，可以先创建外层数组，再分别初始化每一行：

```java
int[][] irregular = new int[3][];
irregular[0] = new int[] {1};
irregular[1] = new int[] {2, 3};
irregular[2] = new int[] {4, 5, 6};
```

### 4.4 对象数组

**完整示例：保存为 `ObjectArrayDemo.java`。**

```java
// File: ObjectArrayDemo.java
class ArrayStudent {
    final String name;

    ArrayStudent(String name) {
        this.name = name;
    }
}

public class ObjectArrayDemo {
    public static void main(String[] args) {
        ArrayStudent[] students = new ArrayStudent[2];
        System.out.println(students[0]);  // null

        students[0] = new ArrayStudent("Alice");
        students[1] = new ArrayStudent("Bob");

        for (ArrayStudent student : students) {
            System.out.println(student.name);
        }
    }
}
```

数组对象已经创建并不代表其中的学生对象已经创建；引用类型数组的元素默认是 `null`。

### 4.5 `Arrays` 常用方法

下面依次演示排序、二分查找和批量填充：

```java
import java.util.Arrays;

int[] numbers = {5, 1, 3, 2, 4};

Arrays.sort(numbers);
System.out.println(Arrays.toString(numbers));  // [1, 2, 3, 4, 5]

int index = Arrays.binarySearch(numbers, 3);
System.out.println(index);                     // 2

Arrays.fill(numbers, 0);
System.out.println(Arrays.toString(numbers));  // [0, 0, 0, 0, 0]
```

> **注意：** `binarySearch()` 要求数组已经按相同规则排序。

---

## 5. 方法

方法把一段逻辑封装成可重复调用的单元，考试重点是值传递、重载、递归和变量作用域。

### 5.1 定义与调用

**完整示例：保存为 `MethodDemo.java`。**

```java
// File: MethodDemo.java
public class MethodDemo {
    public static int add(int left, int right) {
        return left + right;
    }

    public static void printMessage(String message) {
        System.out.println(message);
    }

    public static void main(String[] args) {
        int result = add(3, 5);
        printMessage("结果：" + result);
    }
}
```

方法签名由**方法名和参数类型列表**组成，不包含返回值类型。

### 5.2 Java 只有值传递

基本类型参数复制的是值，方法内修改不会影响调用者：

```java
public static void changeNumber(int number) {
    number = 99;
}

int value = 10;
changeNumber(value);
System.out.println(value);  // 10
```

引用类型参数复制的是引用值。方法可以通过这个引用修改同一个对象，但重新给参数赋值不会改变调用者变量：

```java
public static void changeArray(int[] values) {
    values[0] = 99;
}

public static void replaceArray(int[] values) {
    values = new int[] {7, 8, 9};
}

int[] numbers = {1, 2, 3};
changeArray(numbers);
System.out.println(numbers[0]);  // 99

replaceArray(numbers);
System.out.println(numbers[0]);  // 99，调用者变量没有改为新数组
```

### 5.3 方法重载

同一个类中，方法名相同但参数列表不同，称为重载：

```java
public static int add(int a, int b) {
    return a + b;
}

public static double add(double a, double b) {
    return a + b;
}

public static int add(int a, int b, int c) {
    return a + b + c;
}
```

> **注意：** 仅改变返回值类型不能构成重载。

### 5.4 可变参数

可变参数在方法内部按数组处理，并且必须放在参数列表最后：

```java
public static int sum(int... numbers) {
    int total = 0;
    for (int number : numbers) {
        total += number;
    }
    return total;
}

System.out.println(sum());           // 0
System.out.println(sum(1, 2, 3));    // 6
System.out.println(sum(new int[] {4, 5})); // 9
```

### 5.5 递归

下面用阶乘演示“缩小问题规模并在基线条件停止”：

```java
public static long factorial(int number) {
    if (number < 0) {
        throw new IllegalArgumentException("阶乘只接受非负整数");
    }
    if (number == 0 || number == 1) {
        return 1;
    }
    return number * factorial(number - 1);
}

System.out.println(factorial(5));  // 120
```

调用 `factorial(5)` 会依次计算 `5 × 4 × 3 × 2 × 1`，当参数到达 `1` 时停止。递归必须包含终止条件；递归层数过深会导致 `StackOverflowError`。

### 5.6 局部变量、成员变量与生命周期

| 变量 | 声明位置与默认值 | 生命周期 |
|:---|:---|:---|
| 局部变量 | 方法或代码块内部；无默认值 | 执行进入到离开代码块 |
| 实例变量 | 类中、方法外、无 `static`；有默认值 | 对象存在期间 |
| 类变量 | 类中、使用 `static`；有默认值 | 类加载到卸载期间 |

```java
public class VariableLifeCycle {
    private int instanceValue = 10;
    private static int classValue = 20;

    public void show() {
        int localValue = 30;
        System.out.println(instanceValue + classValue + localValue);
    }
}
```

---

## 6. 字符串、包装类与常用工具类

本章重点掌握字符串不可变性、值比较和可变字符串拼接，并补充常用包装类与工具方法。

### 6.1 `String` 不可变

`String` 对象创建后内容不可修改。字符串方法返回的是新字符串：

```java
String original = "Java";
String changed = original.concat(" Core");

System.out.println(original); // Java
System.out.println(changed);  // Java Core
```

频繁拼接字符串时不要在循环中反复使用 `+`；可变字符串的完整示例见 6.4 节 `StringBuilder`。

### 6.2 字符串池、`==` 与 `equals()`

`==` 比较引用是否相同，`equals()` 比较字符串内容。

```java
String first = "Java";
String second = "Java";
String third = new String("Java");

System.out.println(first == second);       // true，指向字符串池中的同一对象
System.out.println(first == third);        // false
System.out.println(first.equals(third));   // true
```

需要安全比较可能为 `null` 的字符串时，把确定非空的值放在前面：

```java
String input = null;
System.out.println("admin".equals(input));  // false，不会抛异常
```

### 6.3 `String` 常用方法

下面对同一个字符串依次演示长度、清理、截取、查找与替换；这些方法都会返回结果，而不会修改原字符串：

```java
String text = "  Hello Java  ";

System.out.println(text.length());                 // 14
System.out.println(text.trim());                   // Hello Java
System.out.println(text.toUpperCase());            //   HELLO JAVA
System.out.println(text.substring(2, 7));          // Hello
System.out.println(text.indexOf("Java"));          // 8
System.out.println(text.contains("Hello"));        // true
System.out.println(text.replace("Java", "World")); //   Hello World
```

分割与拼接：

```java
String line = "Java,Python,Go";
String[] languages = line.split(",");
String joined = String.join(" | ", languages);

System.out.println(joined);  // Java | Python | Go
```

### 6.4 `StringBuilder`

下面的操作都修改同一个 `StringBuilder` 对象，适合循环或分步骤拼接文本：

```java
StringBuilder builder = new StringBuilder("Java");

builder.append(" Core");
builder.insert(0, "Learn ");
builder.replace(6, 10, "Modern Java");

System.out.println(builder);
System.out.println(builder.reverse());
```

`reverse()` 也会原地修改对象。如果后续还需要原顺序，应先保存 `builder.toString()` 或使用新的 `StringBuilder`。

`StringBuffer` 与 `StringBuilder` 的 API 相似；`StringBuffer` 的常用方法带同步保护，单线程普通拼接通常优先使用 `StringBuilder`。

### 6.5 包装类与自动装箱

| 基本类型 | 包装类 |
|:---:|:---:|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

```java
Integer boxed = 100;          // 自动装箱：int -> Integer
int primitive = boxed;        // 自动拆箱：Integer -> int

int number = Integer.parseInt("42");
String text = Integer.toString(42);

System.out.println(primitive + number);
System.out.println(text);
```

包装类可能为 `null`，自动拆箱时会抛出 `NullPointerException`：

```java
Integer value = null;
// int number = value; // 运行时抛出 NullPointerException
```

包装类是对象，比较数值时使用 `equals()`，不要依赖 `==`。装箱缓存会让 `==` 的结果依赖数值与实现：

```java
Integer smallA = 100;
Integer smallB = 100;
Integer largeA = 1000;
Integer largeB = 1000;

System.out.println(smallA == smallB);       // true：100 在规范要求的缓存范围
System.out.println(largeA == largeB);       // 不应依赖具体结果
System.out.println(largeA.equals(largeB));  // true，数值相等
```

规范至少要求缓存 `-128` 到 `127` 范围内的特定装箱常量；JVM 实现可以扩大缓存范围。因此 `==` 不能用于包装类的数值相等判断。

### 6.6 `Math`

`Math` 提供常用数学运算；数组排序、复制、比较和打印统一参见第 4 章。

```java
System.out.println(Math.abs(-10));
System.out.println(Math.max(3, 8));
System.out.println(Math.pow(2, 3));
System.out.println(Math.sqrt(16));
System.out.println(Math.round(3.6));
System.out.println(Math.random());  // [0.0, 1.0)
```

---

## 7. 类与对象

类描述对象的数据与行为。本章用一组连贯示例说明构造、封装、静态成员和对象引用。

### 7.1 类、对象、字段和方法

**完整示例：保存为 `StudentDemo.java`。**

```java
// File: StudentDemo.java
class Student {
    String name;
    int age;
    double score;

    void introduce() {
        System.out.printf("我是 %s，今年 %d 岁，成绩 %.1f%n", name, age, score);
    }
}

public class StudentDemo {
    public static void main(String[] args) {
        Student student = new Student();
        student.name = "Alice";
        student.age = 20;
        student.score = 92.5;

        student.introduce();
    }
}
```

### 7.2 构造方法与 `this`

构造方法与类同名，没有返回值，在 `new` 对象时调用。

**类型定义：** 下面只定义 `Student`，调用代码放入 `main` 方法。

```java
class Student {
    private String name;
    private int age;

    Student() {
        this("未知", 0);  // 调用本类另一个构造方法，必须写在第一行
    }

    Student(String name, int age) {
        this.name = name;  // this.name 是字段，name 是参数
        setAge(age);
    }

    void introduce() {
        System.out.printf("%s，%d 岁%n", this.name, this.age);
    }

    int getAge() {
        return age;
    }

    void setAge(int age) {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("年龄不合法");
        }
        this.age = age;
    }
}
```

```java
Student student = new Student("Alice", 20);
student.introduce();
student.setAge(21);
System.out.println(student.getAge());  // 21
```

如果类中没有写任何构造方法，编译器会提供无参构造方法，并隐式调用 `super()`。一旦自己声明了构造方法，编译器不再自动补充无参构造；如果父类没有可访问的无参构造，子类还必须显式调用合适的 `super(...)`。

### 7.3 封装与 getter/setter

把字段设为 `private`，通过方法控制读取和修改：

```java
class Account {
    private String owner;
    private double balance;

    Account(String owner, double balance) {
        this.owner = owner;
        setBalance(balance);
    }

    public String getOwner() {
        return owner;
    }

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        if (balance < 0) {
            throw new IllegalArgumentException("余额不能为负数");
        }
        this.balance = balance;
    }
}
```

外部代码不能直接把余额改成非法值，只能通过 `setBalance()` 接受校验。

### 7.4 访问修饰符

| 修饰符 | 可访问范围 | 常见用途 |
|:---:|:---|:---|
| `private` | 仅当前类 | 隐藏字段和实现细节 |
| 不写（包访问） | 当前包 | 包内协作类型或成员 |
| `protected` | 当前包，以及不同包的子类继承访问 | 为子类保留扩展点 |
| `public` | 任意位置 | 对外公开的 API |

> **注意：** 不同包的子类访问 `protected` 成员时受继承规则限制，不能借任意父类对象访问。详细权限只保留在本节，13.3 节不再重复整张表。

### 7.5 `static`

`static` 成员属于类，所有对象共享；实例成员属于具体对象。

```java
class User {
    private static int count = 0;
    private final String name;

    User(String name) {
        this.name = name;
        count++;
    }

    static int getCount() {
        return count;
    }

    String getName() {
        return name;
    }
}

User first = new User("Alice");
User second = new User("Bob");

System.out.println(User.getCount());  // 2
```

> **建议：** 使用 `类名.静态成员` 访问静态成员，例如 `User.getCount()`。

### 7.6 `final`

| 使用位置 | 含义 |
|:---|:---|
| `final` 变量 | 只能赋值一次 |
| `final` 方法 | 子类不能重写 |
| `final` 类 | 不能被继承 |

```java
final class Constants {
    static final int MAX_SIZE = 100;

    private Constants() {
    }
}
```

`final` 引用不能指向新对象，但对象自身仍可能是可变的：

```java
final int[] numbers = {1, 2, 3};
numbers[0] = 99;               // 合法：修改数组内容
// numbers = new int[] {4, 5}; // 编译错误：不能修改引用
```

### 7.7 初始化顺序

下面先定义带静态代码块、实例代码块和构造方法的类：

```java
class InitOrder {
    static {
        System.out.println("1. 静态代码块：类首次加载时执行一次");
    }

    {
        System.out.println("2. 实例代码块：每次创建对象时执行");
    }

    InitOrder() {
        System.out.println("3. 构造方法");
    }
}
```

```java
new InitOrder();
new InitOrder();
```

静态代码块只在类首次使用时执行一次；实例代码块和构造方法每创建一个对象执行一次。加入继承后，大致顺序是：父类静态初始化 → 子类静态初始化 → 父类实例初始化与构造 → 子类实例初始化与构造。

### 7.8 栈、堆与对象引用

**接上 7.2 的 `Student` 类型，以下代码放入 `main` 方法。**

```java
Student first = new Student("Alice", 20);
Student second = first;

second.setAge(21);
System.out.println(first.getAge());  // 21，两个变量引用同一对象

second = null;
System.out.println(first.getAge());  // 21，first 仍然引用对象
```

- 局部变量和方法调用信息主要位于线程栈中。
- `new` 创建的对象通常位于堆中。
- 引用变量保存的是访问对象的引用值，不是对象本体。
- 对象不再被任何可达引用指向后，才有资格被垃圾回收；回收时间由 JVM 决定。

---

## 8. 继承、多态与 `Object`

本章解决三个问题：子类怎样复用父类、同一父类引用怎样表现出不同子类行为，以及对象如何比较。

### 8.1 完整示例：继承、重写与多态

Java 类只支持单继承。下面一份程序同时演示 `extends`、`super`、方法重写、父类引用和安全向下转型。

**完整示例：保存为 `PolymorphismDemo.java`。**

```java
// File: PolymorphismDemo.java
class Animal {
    private final String name;

    Animal(String name) {
        this.name = name;
    }

    String getName() {
        return name;
    }

    void speak() {
        System.out.println(name + "：动物叫声");
    }
}

class Dog extends Animal {
    private final String breed;

    Dog(String name, String breed) {
        super(name);  // 先初始化父类部分
        this.breed = breed;
    }

    @Override
    void speak() {
        System.out.println(getName() + "：汪汪");
    }

    void fetch() {
        System.out.println(getName() + "（" + breed + "）捡回了球");
    }
}

class Cat extends Animal {
    Cat(String name) {
        super(name);
    }

    @Override
    void speak() {
        System.out.println(getName() + "：喵喵");
    }
}

public class PolymorphismDemo {
    public static void main(String[] args) {
        Animal[] animals = {
            new Dog("旺财", "柴犬"),
            new Cat("咪咪")
        };

        for (Animal animal : animals) {
            animal.speak();  // 动态绑定到实际对象的方法

            if (animal instanceof Dog) {
                Dog dog = (Dog) animal;
                dog.fetch();
            }
        }
    }
}
```

输出：

```text
旺财：汪汪
旺财（柴犬）捡回了球
咪咪：喵喵
```

`Animal animal = new Dog(...)` 是向上转型；父类引用只能直接调用父类声明的成员。确认实际类型后，再通过 `instanceof` 和强制转换调用 `Dog` 特有的 `fetch()`。

### 8.2 方法重写规则与重载对比

重写时需要同时满足这些边界：

- 构造方法不会被继承，也不能被重写。
- 方法名和参数列表必须一致，返回类型相同或为协变类型。
- 子类方法不能缩小访问权限。
- 子类不能抛出比父类方法更宽的受检异常。
- `final` 方法不能重写；`private` 方法不参与重写；`static` 方法属于隐藏而不是运行时重写。
- `@Override` 能让编译器帮助检查重写是否正确。

| 对比项 | 重载 Overload | 重写 Override |
|:---|:---|:---|
| 发生位置 | 通常在同一个类 | 父子类之间 |
| 参数列表 | 必须不同 | 必须相同 |
| 决定时机 | 编译期 | 运行期 |

### 8.3 转型与 `instanceof`

向上转型自动完成；向下转型只有在对象实际属于目标类型时才安全，否则会抛出 `ClassCastException`。8.1 的循环先用 `instanceof` 判断，再转换为 `Dog`，就是常见写法。

### 8.4 `Object` 常用方法

所有类最终都继承 `Object`。

**完整示例：保存为 `ObjectMethodDemo.java`。**

```java
// File: ObjectMethodDemo.java
import java.util.HashSet;
import java.util.Objects;
import java.util.Set;

class EqualityStudent {
    private final int id;
    private final String name;

    EqualityStudent(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Student{id=" + id + ", name='" + name + "'}";
    }

    @Override
    public boolean equals(Object object) {
        if (this == object) {
            return true;
        }
        if (!(object instanceof EqualityStudent)) {
            return false;
        }
        EqualityStudent other = (EqualityStudent) object;
        return id == other.id && Objects.equals(name, other.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, name);
    }
}

public class ObjectMethodDemo {
    public static void main(String[] args) {
        EqualityStudent first = new EqualityStudent(1, "Alice");
        EqualityStudent second = new EqualityStudent(1, "Alice");

        System.out.println(first);                 // 调用 toString()
        System.out.println(first == second);       // false：不同对象
        System.out.println(first.equals(second));  // true：内容规则相等

        Set<EqualityStudent> students = new HashSet<EqualityStudent>();
        students.add(first);
        students.add(second);
        System.out.println(students.size());       // 1
    }
}
```

`HashSet` 同时使用 `hashCode()` 和 `equals()` 判断元素是否重复，所以两个内容相等的学生只保留一个。

> **注意：** 如果两个对象通过 `equals()` 判断相等，它们的 `hashCode()` 必须相同，因此通常一起重写。

---

## 9. 抽象类、接口、内部类、枚举与注解

这些语法用于描述“必须实现的能力”、组织辅助类型，以及表达一组固定值或元数据。

### 9.1 抽象类

抽象类不能直接创建对象，可以同时包含抽象方法和普通方法。

```java
abstract class Shape {
    abstract double area();

    void printArea() {
        System.out.printf("面积：%.2f%n", area());
    }
}

class Circle extends Shape {
    private final double radius;

    Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * radius * radius;
    }
}
```

**调用片段：放入 `main` 方法。**

```java
Shape shape = new Circle(2.0);  // 抽象类引用指向具体子类
shape.printArea();              // 面积：12.57
```

调用的是 `Shape` 中已经实现的 `printArea()`，而面积计算动态绑定到 `Circle.area()`。

### 9.2 接口

类可以实现多个接口，用接口描述能力或契约：

```java
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

class Duck implements Flyable, Swimmable {
    @Override
    public void fly() {
        System.out.println("鸭子飞行");
    }

    @Override
    public void swim() {
        System.out.println("鸭子游泳");
    }
}
```

```java
Duck duck = new Duck();
duck.fly();
duck.swim();
```

一个类只能直接继承一个父类，但可以实现多个接口。

接口中的字段默认是 `public static final`，抽象方法默认是 `public abstract`。

### 9.3 接口默认方法与静态方法（Java 8）

默认方法为实现类提供可继承的默认行为，静态方法属于接口本身：

```java
interface Greeting {
    default void sayHello() {
        System.out.println("Hello");
    }

    static void showVersion() {
        System.out.println("Greeting 1.0");
    }
}

class Person implements Greeting {
}

Person person = new Person();
person.sayHello();
Greeting.showVersion();
```

输出依次为 `Hello` 和 `Greeting 1.0`。默认方法通过对象调用，静态方法通过接口名调用。

### 9.4 抽象类与接口的选择

| 场景 | 更适合抽象类 | 更适合接口 |
|:---|:---:|:---:|
| 多个类型共享状态和实现 | 是 | 否 |
| 描述一种能力或契约 | 可用 | 是 |
| 需要多实现 | 否 | 是 |
| 需要构造方法 | 是 | 否 |

### 9.5 内部类

成员内部类依赖外部类对象，静态内部类不依赖外部类实例。下面是类型定义和调用片段：

```java
class Outer {
    private int value = 10;

    class Inner {
        void printValue() {
            System.out.println(value);
        }
    }

    static class StaticInner {
        void show() {
            System.out.println("静态内部类");
        }
    }
}

Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
inner.printValue();

Outer.StaticInner staticInner = new Outer.StaticInner();
staticInner.show();
```

输出依次为 `10` 和“静态内部类”。匿名内部类用于一次性实现接口或继承类，14.1 节会把它与 Lambda 并排比较。

### 9.6 枚举

下面的枚举不仅限制状态取值，还为每个状态保存中文说明：

```java
enum OrderStatus {
    CREATED("已创建"),
    PAID("已支付"),
    SHIPPED("已发货");

    private final String description;

    OrderStatus(String description) {
        this.description = description;
    }

    public String getDescription() {
        return description;
    }
}

OrderStatus status = OrderStatus.PAID;
System.out.println(status.getDescription());
```

枚举常量本身是对象，因此可以拥有字段、构造方法和普通方法；上例输出“已支付”。

### 9.7 注解基础

注解为代码提供元数据，本身通常不直接改变业务逻辑。

| 注解 | 用途 | 典型位置 |
|:---|:---|:---|
| `@Override` | 让编译器检查方法是否正确重写 | 子类方法 |
| `@Deprecated` | 标记不再推荐使用的 API | 类、方法、字段 |
| `@SuppressWarnings` | 在明确原因时抑制指定编译警告 | 局部或较小作用域 |

8.1、9.1 和 9.2 的示例已经实际使用 `@Override`：如果方法签名写错，编译器会直接提示“没有重写父类型方法”。

自定义注解还涉及 `@Target`、`@Retention` 和读取方式。这里只保留入口概念，不展开一个无法产生可观察结果的空声明示例。

---

## 10. 异常处理

异常处理的目标不是隐藏错误，而是区分可恢复问题、补充上下文并可靠释放资源。

### 10.1 异常体系

```text
Throwable
├── Error                 通常不由普通业务代码处理
└── Exception
    ├── RuntimeException  非受检异常
    └── 其他 Exception    受检异常
```

| 类型 | 编译器要求处理 | 常见示例 |
|:---|:---:|:---|
| 受检异常 checked | 是 | `IOException`、`SQLException` |
| 非受检异常 unchecked | 否 | `NullPointerException`、`IllegalArgumentException` |

### 10.2 `try` / `catch` / `finally`

下面的方法分别处理格式错误和除零错误；程序正常执行或抛出异常时，`finally` 通常都会执行：

```java
public static int divide(String left, String right) {
    try {
        int first = Integer.parseInt(left);
        int second = Integer.parseInt(right);
        return first / second;
    } catch (NumberFormatException exception) {
        System.out.println("请输入整数");
    } catch (ArithmeticException exception) {
        System.out.println("除数不能为 0");
    } finally {
        System.out.println("计算结束");
    }
    return 0;
}
```

```java
System.out.println(divide("10", "2"));  // 先打印“计算结束”，再打印 5
System.out.println(divide("10", "0"));  // 打印除零提示、计算结束、0
```

多个 `catch` 从上到下匹配，子类异常必须写在父类异常前面；否则后面的子类分支永远无法到达，会产生编译错误。

> **边界：** `System.exit(...)` 直接终止 JVM、进程被强制结束等情况下，`finally` 可能没有机会执行。因此不要把“任何情况下都执行”当作绝对规则。

可以使用多异常捕获处理相同逻辑：

```java
String text = "abc";
int divisor = 0;

try {
    int result = Integer.parseInt(text) / divisor;
    System.out.println(result);
} catch (NumberFormatException | ArithmeticException exception) {
    System.out.println("输入或计算错误：" + exception.getMessage());
}
```

### 10.3 `throw` 与 `throws`

- `throw`：在方法内部主动抛出一个异常对象。
- `throws`：在方法声明中说明可能向调用者传播的异常。

**`throw` 示例：主动拒绝非法参数。**

```java
public static void setAge(int age) {
    if (age < 0 || age > 150) {
        throw new IllegalArgumentException("年龄不合法：" + age);
    }
}
```

**`throws` 示例：把受检异常交给调用者处理。**

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;

public static String readText(String path) throws IOException {
    byte[] data = Files.readAllBytes(Paths.get(path));
    return new String(data, "UTF-8");
}
```

### 10.4 自定义异常与异常链

下面定义一个受检异常，用 `throws` 传播，并用异常链保存最初的格式错误。

**完整示例：保存为 `CustomExceptionDemo.java`。**

```java
// File: CustomExceptionDemo.java
class ScoreException extends Exception {
    ScoreException(String message) {
        super(message);
    }

    ScoreException(String message, Throwable cause) {
        super(message, cause);
    }
}

public class CustomExceptionDemo {
    static void checkScore(int score) throws ScoreException {
        if (score < 0 || score > 100) {
            throw new ScoreException("成绩必须在 0 到 100 之间");
        }
    }

    static int parseScore(String text) throws ScoreException {
        try {
            int score = Integer.parseInt(text);
            checkScore(score);
            return score;
        } catch (NumberFormatException exception) {
            throw new ScoreException("成绩格式错误：" + text, exception);
        }
    }

    public static void main(String[] args) {
        for (String input : new String[] {"120", "abc"}) {
            try {
                System.out.println(parseScore(input));
            } catch (ScoreException exception) {
                System.out.println(exception.getMessage());
                if (exception.getCause() != null) {
                    System.out.println("原始原因：" +
                            exception.getCause().getClass().getSimpleName());
                }
            }
        }
    }
}
```

输入 `120` 会触发成绩范围异常；输入 `abc` 会显示“成绩格式错误”，并保留原始 `NumberFormatException`。

### 10.5 try-with-resources

实现 `AutoCloseable` 的资源可以在语句结束后自动关闭：

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Paths;

public static String readFirstLine(String path) throws IOException {
    try (BufferedReader reader = Files.newBufferedReader(
            Paths.get(path), StandardCharsets.UTF_8)) {
        return reader.readLine();
    }
}
```

> **建议：** 文件、流和数据库连接等资源优先使用 try-with-resources 管理。

---

## 11. 泛型与集合框架

先理解泛型如何保证类型安全，再选择合适的集合保存、查找、去重或排序数据。

**第一部分：泛型与类型安全**

### 11.1 泛型类

泛型把类型作为参数，使同一份代码安全处理不同类型。

先记住三个基础规则：

- 泛型参数不能直接使用基本类型，应写 `List<Integer>`，不能写 `List<int>`。
- Java 7+ 可以使用菱形语法 `new ArrayList<>()`，让编译器推断右侧类型。
- 泛型默认不协变：即使 `Integer` 是 `Number` 的子类，`List<Integer>` 也不是 `List<Number>` 的子类。

**完整示例：保存为 `GenericBoxDemo.java`。**

```java
// File: GenericBoxDemo.java
class Box<T> {
    private T value;

    void set(T value) {
        this.value = value;
    }

    T get() {
        return value;
    }
}

public class GenericBoxDemo {
    public static void main(String[] args) {
        Box<String> textBox = new Box<>();
        textBox.set("Java");

        Box<Integer> numberBox = new Box<>();
        numberBox.set(100);

        System.out.println(textBox.get());
        System.out.println(numberBox.get());
    }
}
```

### 11.2 泛型方法

下面的方法用同一个类型参数约束数组元素与返回值类型：

```java
public static <T> T first(T[] values) {
    if (values.length == 0) {
        return null;
    }
    return values[0];
}

String name = first(new String[] {"Alice", "Bob"});
Integer number = first(new Integer[] {10, 20});

System.out.println(name);    // Alice
System.out.println(number);  // 10
```

限定类型参数：

```java
public static <T extends Number> double doubleValue(T number) {
    return number.doubleValue();
}
```

### 11.3 通配符

**完整示例：保存为 `WildcardDemo.java`。**

```java
// File: WildcardDemo.java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class WildcardDemo {
    static double sum(List<? extends Number> numbers) {
        double total = 0;
        for (Number number : numbers) {
            total += number.doubleValue();
        }
        return total;
    }

    static void addDefaults(List<? super Integer> numbers) {
        numbers.add(0);
        numbers.add(1);
    }

    public static void main(String[] args) {
        List<Integer> integers = Arrays.asList(1, 2, 3);
        System.out.println(sum(integers));  // 6.0

        List<Number> numbers = new ArrayList<>();
        addDefaults(numbers);
        System.out.println(numbers);       // [0, 1]
    }
}
```

- `? extends T`：适合读取 `T` 或其子类，通常不能安全添加具体元素。
- `? super T`：可以安全添加 `T` 及其子类，读取时通常只能当作 `Object`。
- 记忆：**Producer Extends，Consumer Super（PECS）**。

**第二部分：集合框架**

### 11.4 集合框架关系

```text
Iterable
└── Collection
    ├── List   有序、可重复
    ├── Set    不重复
    └── Queue  队列

Map           键值映射，不继承 Collection
```

| 接口 | 常用实现 | 特点 |
|:---|:---|:---|
| `List` | `ArrayList` | 查询快，尾部增删方便 |
| `List` | `LinkedList` | 链表结构，也实现 `Deque` |
| `Set` | `HashSet` | 去重，不保证遍历顺序 |
| `Set` | `TreeSet` | 元素按规则排序 |
| `Map` | `HashMap` | 常用键值映射，允许一个 `null` 键 |
| `Map` | `TreeMap` | 按键排序 |

### 11.5 `ArrayList` 与 `LinkedList`

**`ArrayList` 示例：按索引维护姓名列表。**

```java
import java.util.ArrayList;
import java.util.List;

List<String> names = new ArrayList<String>();
names.add("Alice");
names.add("Bob");
names.add(1, "Tom");

System.out.println(names.get(0));
System.out.println(names.contains("Bob"));
System.out.println(names.remove("Tom"));
System.out.println(names.size());
```

上例依次读取首元素、判断是否包含 `Bob`、删除 `Tom`，最终列表为 `[Alice, Bob]`。

**`LinkedList` 示例：按先进先出方式模拟队列。**

```java
import java.util.LinkedList;

LinkedList<String> queue = new LinkedList<String>();
queue.addLast("任务1");
queue.addLast("任务2");

System.out.println(queue.removeFirst());  // 任务1
```

### 11.6 `HashSet`

`HashSet` 用于去重。重复添加 `Java` 后，集合大小仍为 `2`：

```java
import java.util.HashSet;
import java.util.Set;

Set<String> tags = new HashSet<String>();
tags.add("Java");
tags.add("SQL");
tags.add("Java");

System.out.println(tags.size());          // 2
System.out.println(tags.contains("Java")); // true
```

自定义对象放入 `HashSet` 时，应正确实现 `equals()` 和 `hashCode()`。

### 11.7 `HashMap`

`HashMap` 用键快速定位值。相同键再次 `put()` 会覆盖旧值：

```java
import java.util.HashMap;
import java.util.Map;

Map<String, Integer> scores = new HashMap<String, Integer>();
scores.put("Alice", 90);
scores.put("Bob", 85);
scores.put("Alice", 95);  // 相同键覆盖旧值

System.out.println(scores.get("Alice"));
System.out.println(scores.getOrDefault("Tom", 0));

for (Map.Entry<String, Integer> entry : scores.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

`HashMap` 不保证业务上的遍历顺序；需要插入顺序时使用 `LinkedHashMap`，需要按键排序时使用 `TreeMap`。

### 11.8 遍历与迭代器

遍历中需要删除当前元素时，使用 `Iterator.remove()`，不要直接调用集合的 `remove()`：

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

List<String> names = new ArrayList<String>();
names.add("Alice");
names.add("Bob");
names.add("Tom");

Iterator<String> iterator = names.iterator();
while (iterator.hasNext()) {
    String name = iterator.next();
    if (name.startsWith("T")) {
        iterator.remove();  // 遍历时安全删除当前元素
    }
}

for (String name : names) {
    System.out.println(name);
}
```

删除以 `T` 开头的名字后，输出 `Alice` 和 `Bob`。

### 11.9 `Comparable` 与 `Comparator`

`Comparable` 定义类型唯一的自然顺序；`Comparator` 在外部提供可以替换的排序规则。

**完整示例：保存为 `SortingDemo.java`。**

```java
// File: SortingDemo.java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

class ComparableStudent implements Comparable<ComparableStudent> {
    private final String name;
    private final int score;

    ComparableStudent(String name, int score) {
        this.name = name;
        this.score = score;
    }

    String getName() {
        return name;
    }

    int getScore() {
        return score;
    }

    @Override
    public int compareTo(ComparableStudent other) {
        return Integer.compare(this.score, other.score);
    }

    @Override
    public String toString() {
        return name + "=" + score;
    }
}

public class SortingDemo {
    public static void main(String[] args) {
        List<ComparableStudent> students = new ArrayList<>();
        students.add(new ComparableStudent("Alice", 90));
        students.add(new ComparableStudent("Bob", 85));
        students.add(new ComparableStudent("Amy", 90));

        Collections.sort(students);
        System.out.println("自然顺序：" + students);

        students.sort(Comparator.comparing(ComparableStudent::getName));
        System.out.println("按姓名：" + students);

        students.sort(
            Comparator.comparingInt(ComparableStudent::getScore)
                      .reversed()
                      .thenComparing(ComparableStudent::getName)
        );
        System.out.println("成绩降序、同分按姓名：" + students);
    }
}
```

三次输出分别体现自然顺序、外部比较器和组合比较器。Java 8 的方法引用只是在不改变比较逻辑的前提下减少样板代码。

---

## 12. 文件与 I/O

本章从路径开始，依次介绍字节流、字符流、缓冲和资源关闭；示例统一明确字符编码。

### 12.1 `File` 与 `Path`

`File` 表示文件或目录路径，不代表文件内容已经打开：

**代码片段：放入 `main` 方法。**

```java
import java.io.File;

File file = new File("data/example.txt");

System.out.println(file.exists());
System.out.println(file.isFile());
System.out.println(file.getAbsolutePath());
System.out.println(file.length());
```

Java 7+ 推荐使用 `Path` 和 `Files`：

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

Path directory = Paths.get("data");
Files.createDirectories(directory);

Path file = directory.resolve("example.txt");
System.out.println(file.toAbsolutePath());
System.out.println(Files.exists(file));
```

第一段只查询路径信息；第二段会实际创建 `data` 目录。新代码通常优先选择 `Path` 与 `Files`。

### 12.2 字节流

字节流适合图片、音频、压缩包等二进制数据。

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public static void copyBinary(String source, String target) throws IOException {
    try (FileInputStream input = new FileInputStream(source);
         FileOutputStream output = new FileOutputStream(target)) {

        byte[] buffer = new byte[8192];
        int length;
        while ((length = input.read(buffer)) != -1) {
            output.write(buffer, 0, length);
        }
    }
}
```

调用 `copyBinary("photo.jpg", "photo-copy.jpg")` 时，每次最多读取 8192 字节，直到 `read()` 返回 `-1`；try-with-resources 会自动关闭两个流。

### 12.3 字符流与缓冲流

字符流适合文本，并应明确字符编码：

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

public static void writeAndReadText() throws IOException {
    Path path = Paths.get("data", "users.txt");
    Files.createDirectories(path.getParent());

    try (BufferedWriter writer = Files.newBufferedWriter(
            path, StandardCharsets.UTF_8)) {
        writer.write("Alice,90");
        writer.newLine();
        writer.write("Bob,85");
    }

    try (BufferedReader reader = Files.newBufferedReader(
            path, StandardCharsets.UTF_8)) {
        String line;
        while ((line = reader.readLine()) != null) {
            System.out.println(line);
        }
    }
}
```

调用 `writeAndReadText()` 会创建 `data/users.txt`，写入两行 UTF-8 文本，再逐行输出 `Alice,90` 和 `Bob,85`。

### 12.4 `Files` 的常用操作

下面的完整程序创建目录和源文件、复制到目标路径，然后读取并输出两行文本。

**完整示例：保存为 `FilesDemo.java`。**

```java
// File: FilesDemo.java
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.StandardCopyOption;
import java.util.Arrays;

public class FilesDemo {
    public static void main(String[] args) throws IOException {
        Path source = Paths.get("data", "source.txt");
        Path target = Paths.get("data", "target.txt");

        Files.createDirectories(source.getParent());
        Files.write(
                source,
                Arrays.asList("第一行", "第二行"),
                StandardCharsets.UTF_8
        );
        Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);

        for (String line : Files.readAllLines(
                target, StandardCharsets.UTF_8)) {
            System.out.println(line);
        }
    }
}
```

### 12.5 对象序列化

Java 原生序列化使用 `ObjectOutputStream` 写对象、`ObjectInputStream` 读对象。类型需要实现 `Serializable`，通常声明 `serialVersionUID`；`transient` 字段不会写入默认序列化结果。

下面在内存中完成一次最小的写入与读取，用来观察 `transient` 字段：

```java
// File: SerializationDemo.java
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;

class SerializableUser implements Serializable {
    private static final long serialVersionUID = 1L;

    private final String name;
    private transient String password;

    SerializableUser(String name, String password) {
        this.name = name;
        this.password = password;
    }

    String getName() {
        return name;
    }

    String getPassword() {
        return password;
    }
}

public class SerializationDemo {
    public static void main(String[] args) throws Exception {
        ByteArrayOutputStream bytes = new ByteArrayOutputStream();
        try (ObjectOutputStream output = new ObjectOutputStream(bytes)) {
            output.writeObject(new SerializableUser("Alice", "secret"));
        }

        try (ObjectInputStream input = new ObjectInputStream(
                new ByteArrayInputStream(bytes.toByteArray()))) {
            SerializableUser user = (SerializableUser) input.readObject();
            System.out.println(user.getName());      // Alice
            System.out.println(user.getPassword());  // null
        }
    }
}
```

> **选学说明：** 原生序列化不是基础语法主线，而且反序列化不可信数据存在安全风险。实际业务应根据接口约定选择明确格式，绝不能直接反序列化不可信来源的数据。

---

## 13. 包、导入与工程组织

包用于组织类并控制可见性；classpath 则告诉编译器和 JVM 去哪里查找这些类。

### 13.1 `package` 与目录结构

包声明通常写在源文件第一条非注释语句，并与目录结构对应：

```text
src/
└── com/
    └── example/
        ├── app/
        │   └── Main.java
        └── model/
            └── Student.java
```

```java
// File: src/com/example/model/Student.java
package com.example.model;

public class Student {
    private final String name;

    public Student(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

### 13.2 `import` 与全限定类名

下面的 `Main.java` 导入上一小节定义的 `Student`：

```java
// File: src/com/example/app/Main.java
package com.example.app;

import com.example.model.Student;
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<Student>();
        students.add(new Student("Alice"));
        System.out.println(students.get(0).getName());
    }
}
```

发生同名类冲突时，可以对其中一个使用全限定类名：

```java
java.util.Date utilDate = new java.util.Date();
java.sql.Date sqlDate = new java.sql.Date(System.currentTimeMillis());
```

`java.lang` 包中的类会自动导入，例如 `String`、`System`、`Math`。

### 13.3 包与访问权限

四种成员访问权限的完整对比见 7.4 节。本章只补充顶层类型规则：顶层类或接口的**访问修饰符**只能是 `public` 或包访问（不写）；它仍然可以按语义使用 `abstract`、`final` 等其他修饰符。

同一源文件最多只有一个 `public` 顶层类，并且文件名必须与这个公共类名一致。没有 `public` 的顶层类型只在当前包内可见。

### 13.4 编译包结构与 classpath

在项目根目录编译到 `out` 目录：

```bash
javac -d out src/com/example/model/Student.java src/com/example/app/Main.java
java -cp out com.example.app.Main
```

- `-d out`：按包结构输出 `.class` 文件。
- `-cp out`：把 `out` 加入 classpath。
- 运行时使用全限定类名，不写 `.class` 后缀。

classpath 告诉编译器和 JVM 去哪里查找类与资源。实际项目通常由 IDE 或构建工具管理，本篇只需理解这个作用。

---

## 14. Lambda、方法引用、Stream 与 Optional（Java 8）

本章把匿名行为当作数据传递，并使用 Stream 描述集合数据的筛选、转换和汇总过程。

### 14.1 函数式接口

函数式接口只有一个需要实现的抽象方法，因此可以用 Lambda 表达式创建实现对象。它仍然可以包含默认方法、静态方法；与 `Object` 公共方法等价的声明不计入这个唯一抽象方法。

先看匿名内部类与 Lambda 的对比：

**完整示例：保存为 `LambdaDemo.java`。**

```java
// File: LambdaDemo.java
public class LambdaDemo {
    @FunctionalInterface
    interface Calculator {
        int calculate(int left, int right);
    }

    public static void main(String[] args) {
        Calculator oldStyle = new Calculator() {
            @Override
            public int calculate(int left, int right) {
                return left + right;
            }
        };

        Calculator add = (left, right) -> left + right;
        Calculator multiply = (left, right) -> left * right;

        System.out.println(oldStyle.calculate(3, 5));
        System.out.println(add.calculate(3, 5));
        System.out.println(multiply.calculate(3, 5));
    }
}
```

三次输出分别为 `8、8、15`。Lambda 省略的是单抽象方法接口的样板代码，并不是一种独立的函数类型。

常用函数式接口：

| 接口 | 抽象方法形状 | 常见用途 |
|:---|:---|:---|
| `Predicate<T>` | `T → boolean` | 判断条件 |
| `Function<T, R>` | `T → R` | 转换数据 |
| `Consumer<T>` | `T → void` | 消费数据 |
| `Supplier<T>` | `() → T` | 提供数据 |

**完整示例：保存为 `FunctionInterfacesDemo.java`。**

```java
// File: FunctionInterfacesDemo.java
import java.util.function.Function;
import java.util.function.Predicate;

public class FunctionInterfacesDemo {
    public static void main(String[] args) {
        Predicate<Integer> positive = number -> number > 0;
        Function<String, Integer> length = text -> text.length();

        System.out.println(positive.test(10));  // true
        System.out.println(length.apply("Java"));  // 4
    }
}
```

### 14.2 方法引用

方法引用是特定 Lambda 的简写：

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

List<String> names = Arrays.asList("Alice", "Bob");

names.forEach(System.out::println);  // 对象::实例方法

List<Integer> lengths = names.stream()
        .map(String::length)         // 类::实例方法
        .collect(Collectors.toList());

System.out.println(lengths);         // [5, 3]
```

常见形式：`类名::静态方法`、`对象::实例方法`、`类名::实例方法`、`类名::new`。

### 14.3 Stream 基本流程

Stream 不直接保存数据，而是描述对数据的处理流水线。

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

List<String> names = Arrays.asList("Tom", "Alice", "Bob", "Alex");

List<String> result = names.stream()
        .filter(name -> name.startsWith("A"))
        .map(String::toUpperCase)
        .sorted()
        .collect(Collectors.toList());

System.out.println(result);  // [ALICE, ALEX]
```

`filter`、`map` 等中间操作是惰性的；只有遇到 `collect`、`sum` 等终止操作，流水线才真正执行。一个 Stream 执行终止操作后不能再次使用。

常用操作：

| 类型 | 操作 | 作用 |
|:---:|:---|:---|
| 中间操作 | `filter`、`map`、`sorted`、`distinct` | 返回新 Stream，可继续连接 |
| 终止操作 | `collect`、`forEach`、`count`、`reduce` | 触发实际计算 |

聚合示例：

```java
int total = Arrays.asList(1, 2, 3, 4)
        .stream()
        .filter(number -> number % 2 == 0)
        .mapToInt(Integer::intValue)
        .sum();

System.out.println(total);  // 6
```

### 14.4 `Optional`

`Optional` 用来显式表示“可能没有值”，常用于方法返回值。

**完整示例：保存为 `OptionalDemo.java`。**

```java
// File: OptionalDemo.java
import java.util.Optional;

public class OptionalDemo {
    static Optional<String> findName(int id) {
        if (id == 1) {
            return Optional.of("Alice");
        }
        return Optional.empty();
    }

    public static void main(String[] args) {
        String first = findName(1)
                .map(String::toUpperCase)
                .orElse("UNKNOWN");
        String second = findName(2)
                .map(String::toUpperCase)
                .orElse("UNKNOWN");

        System.out.println(first);   // ALICE
        System.out.println(second);  // UNKNOWN
    }
}
```

> **注意：** `Optional` 主要用于返回值，不建议把它滥用为实体字段或方法参数。

---

## 15. 现代 Java 语法选学

本章只保留常见新语法的入口和版本要求，不把它们混入 Java 8 基础主线。

> **版本说明：** 本章不属于 Java 8 主线。复制代码前先确认课程、编译器和运行环境版本。

### 15.1 `var` 局部变量类型推断（Java 10）

下面两个局部变量的静态类型分别被推断为 `String` 和 `ArrayList<Integer>`：

```java
var name = "Alice";                    // 推断为 String
var scores = new java.util.ArrayList<Integer>();
scores.add(90);
```

`var` 不是动态类型，变量类型仍在编译期确定。它只能用于可推断类型的局部变量，不能用于字段、方法参数、返回类型，也不能仅用 `null` 初始化。

### 15.2 switch 表达式（Java 14）

switch 表达式直接产生一个值，不再依赖先声明变量再逐分支赋值：

```java
int day = 2;

String result = switch (day) {
    case 1, 2, 3, 4, 5 -> "工作日";
    case 6, 7 -> {
        System.out.println("周末");
        yield "休息日";
    }
    default -> throw new IllegalArgumentException("无效日期");
};

System.out.println(result);  // 工作日
```

箭头分支不会发生传统 `switch` 的穿透；分支需要多条语句时，用 `yield` 返回表达式结果。

### 15.3 文本块（Java 15）

下面的多行 JSON 保留清晰缩进，不需要逐行拼接字符串：

```java
String json = """
        {
          "name": "Alice",
          "age": 20
        }
        """;
```

文本块适合 JSON、SQL 和多行模板，可以减少大量 `\n` 与引号转义。

### 15.4 record（Java 16）

record 适合表达数据载体，编译器自动生成构造方法、访问方法、`equals()`、`hashCode()` 和 `toString()`。

```java
record RecordStudent(String name, int score) {
    RecordStudent {
        if (score < 0 || score > 100) {
            throw new IllegalArgumentException("成绩不合法");
        }
    }
}

RecordStudent student = new RecordStudent("Alice", 95);
System.out.println(student.name());
System.out.println(student);
```

record 的组件引用不能重新赋值，但这不等于深度不可变：如果组件引用的是可变集合，集合内容仍然可能被修改。

### 15.5 `instanceof` 模式匹配（Java 16）

模式变量把类型判断和安全转换合并为一步：

```java
Object value = "Java";

if (value instanceof String text) {
    System.out.println(text.toUpperCase());
}
```

条件成立时，`text` 已经是 `String`，不再需要单独强制转换；上例输出 `JAVA`。

### 15.6 switch 模式匹配（Java 21）

模式匹配可以根据引用对象的实际类型选择分支并直接绑定变量：

```java
static String describe(Object value) {
    return switch (value) {
        case null -> "空值";
        case Integer number -> "整数：" + number;
        case String text -> "字符串：" + text;
        default -> "其他类型";
    };
}

System.out.println(describe(10));      // 整数：10
System.out.println(describe("Java"));  // 字符串：Java
```

---

**复习建议：** 先看第 2～6 章恢复语法手感，再重点复习第 7～11 章，最后按需要阅读 I/O、函数式语法和现代 Java。
