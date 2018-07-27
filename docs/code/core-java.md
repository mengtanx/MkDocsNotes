# Java 核心技术 第 10 版笔记

学习 Java 提供的所有高级特性：

* 面向对象程序设计
* 反射与代理
* 接口与内部类
* 异常处理
* 泛型程序设计
* 集合框架
* 并行操作

## 第 1 章 Java 程序设计概述

Java 的设计者编写了颇有影响力的 “ 白皮书”，用来解释设计的初衷以及完成的情况，并且发布了一个简短的摘要。这个摘要可以用以下 11 个关键术语进行组织：

1. 简单性：Java 语法是 C++ 语法的一个“纯净”版本，没有头文件、指针运算（甚至指针语法）、结构、联合、操作符重载、虚基类。简单的另一个方面是小，Java 具有一个独立的具有较小类库的 Java 微型版（Java Micro Edition），适用于嵌入式设备。
2. 面向对象：Java 的面向对象特性与 C++ 旗鼓相当，主要不同点在于多重继承，在 Java 中，取而代之的是更简单的接口概念。并且 Java 提供了更丰富的运行时自省功能。
3. 分布式
4. 健壮性：Java 编译器能够检测许多在其他语言中仅在运行时才能检测出来的问题。
5. 安全性：使用 Java 可以构建防病毒、防篡改的系统，Java 设计成能够防范各种攻击。
6. 体系结构中立
7. 可移植性：Java 规范中对基本数据类型的大小以及有关运算都做了明确的说明，没有“依赖具体实现”的地方。如 Java 中的 int 永远为 32 位整数，二进制数据以固定格式进行存储和传输，消除了字节顺序的困扰。字符串是用标准的 Unicode 格式存储的。
8. 解释型：Java 解释器可以在任何移植了解释器的机构上执行 Java 字节码，开发过程可以变得更加快捷，更加具有探索性。
9. **高性能**
10. 多线程：多线程可以带来更好的交互响应和实时行为。
11. 动态性：从各种角度看，Java 与 C 或 C++ 相比更加具有动态性。它能够适应不断发展的环境。库中可以自由地添加新方法和实例变量，而对客户端却没有任何影响。

注释：写这本书时,白皮书可以在 www.oracle.com/technetwork/java/langenv-140151.html 上找对于 11 个关键术语的论述请参看 http://horstmann.com/corejava/java-an-interview/7Gosling.pdf。


## 第 2 章 Java 程序设计环境
* Java 术语

## 第 3 章 Java 的基本程序设计结构

### 3.1 一个简单的 Java 应用程序

* 关键字 public 称为访问修饰符 access modifier，用于控制程序的其他部分对这段代码的访问级别
* 关键字 class 表明 Java 程序中的全部内容都包含在类中。这里只是将类作为一个加载程序逻辑的容器。
* Java 应用程序中的全部内容都必须放置在类中。关键字 class 后面紧跟类名，类名遵循驼峰命名法。
* 源代码的文件名必须与公共类的名字相同，并用 .java 作为扩展名。并且区分大小写。
* 需要记住：每个 Java 应用程序都必须有一个 main 方法，且必须是静态的。

### 3.2 注释

1. 行注释 //
2. 块注释 /* */
3. 文档注释 /** */

注意：在 Java 中，/* */ 不能嵌套

### 3.3 数据类型

Java 一共有 8 种基本数据类型（primitive type），包括 4 种整型，2 种浮点类型，1 中用于表示 Unicode 编码的字符单元的字符类型 char 和 1 种用于表示真值的 boolean 类型。

#### 整型

在 Java 中，整型的范围与运行 Java 代码的机器无关，解决了软件在不同平台或操作系统间移植的问题。**注意：Java 没有任何无符号（unsigned）形式的 int、long、short 或 byte 类型。**

* int 4 字节 ≈20亿
* short 2 字节 -32768-32767
* long 8 字节
* byte 1 字节 -128-127

长整型有一个后缀 L 或 l，十六进制数有一个前缀 0X 或 0x，八进制数有一个前缀 0，如 010 对应八进制中的 8。由于八进制表示法比较容易混淆，建议最好不要使用八进制常熟。

从 Java 7 开始，加上前缀 0b 或 0B 就可以写二进制数，如 0b1001 表示 9；还可以为数字字面量加下划线，如 1_000_000 表示一百万，这些下划线可以提升可读性。Java 编译器会去除这些下划线。

#### 浮点型

* float 4 字节，有效数字精确到 6-7 位
* double 8 字节，有效数字精确到 15 位

float 类型的数值需要有一个后缀 F 或 f，没有后缀的浮点数默认为 double 类型。所有的浮点数值计算都遵循 IEEE 754 规范，具体来说，有三个用于表示移除和出错情况的三个特殊的浮点数值：

* 正无穷大：`Double.POSITIVE_INFINITY`
* 负无穷大：`Double.NEGATIVE_INFINITY`
* NaN（不是一个数字）：`Double.NaN`。可以用 Double.isNaN() 方法来检测一个特定值是否等于 Double.NaN。如计算 0/0 或者负数的平方根结果为 NaN
* **浮点数不适用于无法接受舍入误差的金融计算中。**如果数值计算中不允许有任何舍入误差，就应该使用 BigDecimal 类。

```java
// is never true
if (x == Double.NaN)

// 所有“非数值”的值都认为是不相同的，可以使用 Double.isNaN 方法
// check whether x is "Not a Number"
if (Double.isNaN(x))
```

#### char 类型

* 需要注意的是，Java 中的 char 类型原本用于表示单个字符，但现在 Unicode 字符可以用一个 char 值描述，另外一些 Unicode 字符则需要两个 char 值。
* char 类型的字面量值要用**单引号**括起来。如 `'A'` 是编码值为 65 所对应的字符常量，它与字符串 `"A"` 不同。
* char 类型的值可以表示为十六进制值，其范围从 `\u0000` 到 `\Uffff`。
* Java 中用于表示特殊字符的转义序列包括以下，这些都可以出现在加引号的字符字面量或字符串中。
    * `\b` 退格
    * `\t` 制表
    * `\n` 换行
    * `\r` 回车
    * `\"` 双引号
    * `\'` 单引号
    * `\\` 反斜杠
* **特殊的，转移序列 `\u` 还可以出现在加引号的字符常量或字符串之外，而其他转义序列不可以。** 字符需要一对代码单元表示。
* char 类型是一个采用 UTF-16 编码表示 Unicode 码点的代码单元。大多数的常用 Unicode 字符使用一个代码单元就可以表示，而辅助字符需要一对代码单元表示。

```java
// 以下写法完全符合语法规则
public static void main String \u005B \u005D args)
```

#### Unicode 和 char 类型

> 详细解释 Java 中如何解决 Unicode 编码问题的

在 Java 中，char 类型描述了 UTF-16 编码中的一个代码单元。
**我们强烈建议不要在程序中使用 char 类型，除非确实需要处理 UTF-16 代码单元。最好将字符串作为抽象数据类型处理。**

#### boolean 类型

**整数值和布尔值之间不能进行相互转换。**

### 3.4 变量

* 声明变量的语句必须以分号结束。
* 可以在一行中声明多个变量，但不提倡使用这种风格，建议逐一声明每一个变量以提升程序的可读性。
* 变量名必须是一个以字母开头并由字母或数字构成的序列。变量名对大小写敏感的，在对两个不同的变量进行命名时，最好不要只存在大小写上的差异。
* **需要注意的是，与大多数程序设计语言相比，Java 中的字母和数字的范围更大。**
    * 字母包括 `'A'`~ `'Z'`, `'a'` ~ `'z'`, `'_'`, `'$'` 或在某种语言中表示字母的任何 Unicode 字符。*可以使用 Character 类的 isJavaIdentifierStart 和 isJavaIdentifierPart 方法来检查。*
    * 数字包括 `'0'` ~ `'9'` 和在某种语言中表示数字的任何 Unicode 字符
    * 但 `'+'` 和 `©`这样的符号不能出现在变量名中，空格也不行。
* 变量名中所有的字符都是有意义的，并且大小写敏感。
* 变量名的长度基本上没有限制。
* **尽管 `$` 是一个合法的 Java 字符，但不要在自己的代码中使用这个字符。它只用在 Java 编译器或其他工具生成的名字中。**
* 另外，不能使用 Java 保留字作为变量名。

#### 变量初始化

* 声明一个变量之后，必须用赋值语句对变量进行显示初始化。千万不要使用未初始化的变量，编译时出错。如 `int vacationDays = 12;`
* 虽然在 Java 中可以将声明放在代码的任何地方，但是建议在变量的声明尽可能地靠近变量第一次使用的地方，这是一种良好的程序编写风格。**Java 中不区分变量的声明与定义，在 C 和 C++ 中区分变量的声明与定义。

#### 常量

* 在  Java 中，利用关键字 `final` 指示常量,关键字 `final` 表示这个变量只能被赋值一次，一旦被赋值之后，就不能再更改了。*习惯上，常量名使用全大写。*
* 在 Java 中，经常希望某个常量可以在一个类中的多个方法中出使用，通常将这些常量成为类常量。可以使用关键字 `static final` 设置一个类常量。
* 需要注意，类常量的定义位于 main 方法的外部。因此，在同一个类的其他方法中也可以使用这个常量。而且如果一个常量被声明为 public，那么其他类的方法也可以使用这个常量。

```java
public class Constants2
{
    public static final double CM_PER_INCH = 2.54;
    public static void main(String[] args)
    {
        double paperWidth = 8.5;
        double paperHeight = 11;
        System.out.println("Paper size in centimeters: "
            + paperWidth * CM_PER_INCH + " by " + paperHeight * CM_PER_INCH);
    }
}
```

### 3.5 运算符

* 需要注意，整数被 0 除将会产生一个异常，而浮点数被 0 除将会得到无穷大或 NaN 结果。
* 可以将 main 方法标记为 `public static strictfp void main(String[] args)`，使得 main 方法中的所有指令都将使用严格的浮点计算。如果一个类标记为 stricfp，这个类中的所有方法都要使用严格的浮点计算。

#### 数学函数与常量

* 常用数学函数
    * Math.sqrt
    * Math.pow
    * Math.round
    * Math.floorMod
    * Math.sin
    * Math.cos
    * Math.tan
    * Math.atan
    * Math.atan2
    * Math.exp
    * Math.log
    * Math.log10
* 常量
    * Math.PI
    * Math.E
* 自由发布的 Math 库（fdlibm）提供 StrictMath 类，可以确保在所有平台上得到相同的结果和完全可预测的结果。

#### 数值类型之间的转换

数值类型之间的合法转换
![-w376](media/15298102493952/15311359208360.jpg)

#### 强制类型转换
* 在 Java 中，可以通过强制类型转换（cast）将浮点值的小数部分截断并转换为整型。
* 如果试图将一个数值从一种类型强制转换为另一种类型，而又超出了目标类型的表示范围，结果就会截断成一个完全不同的值。如 `(byte)300` 的实际值为 44。
* **不要在 boolean 类型与任何数值类型之间进行强制类型转换，这样可以防止发生错误。只有极少数的情况下才需要将布尔类型转换为数值类型，这时候可以使用条件表达式 `b ? 1 : 0`
#### 结合赋值和运算符
* `+=`, `-=`, `*=`, `/=`, `%=`

#### 自增与自减运算符
* `n++` 先代入表达式计算，再加一
* `n--` 先代入表达式计算，再减一
* `++n` 先加一，再代入表达式计算
* `--n` 先减一，再代入表达式计算

```java
int m = 7;
int n = 7;
int a = 2 * ++m; // now a = 16, m = 8
int b = 2 * n++; // now b is 14, n = 8
```
#### 关系和 boolean 运算符
* 关系运算符：`==`, `<`, `>`, `<=`, `>=`
* 逻辑运算符：**短路运算**
    * `&&` 逻辑与
    * `||` 逻辑或
    * `!` 逻辑非
* 三元操作符 `?:`: `condition ? expression1 : expression2`

#### 位运算符

* 处理整数类型时，可以直接对组成整型数值的各个位完成操作。这意味着可以使用掩码技术得到整数中的各个位。位运算符包括：
    * `&` and
    * `|` or
    * `^` xor
    * `~` not
    * `>>` 和 `<<` 将位模式左移或右移。在需要建立位模式来完成位掩码时，这两个运算符会很方便。
    * `>>>` 运算符会用 0 填充高位，与 `>>` 不同，它会用符号位填充高位，不存在 `<<<` 运算符。
    * **移位运算符的右操作数要完成模 32 的运算（除非左操作数是 long 类型，在这种情况下需要对右操作数模 64）。如 `1<<35` 的值等同于 `1<<3` 或 `8`。
* 位运算符应用在布尔值上时，`&` 和 `|` 也会得到一个布尔值，与 `&&` 和 `||` 运算符类似，不过不采用短路方式来运算。

#### 括号和运算符级别

运算符 优先级

| 运算符 | 结合性 |
| --- | --- |
| `[].()` 方法调用 | 从左向右 |
| `!` `~` `++` `--` `+`（一元运算）`-`（一元运算）`()`（强制类型转换） | 从右向左 |
| `*` `/` `%` | 从左向右 |
| `+` `-` | 从左向右 |
| `<<` `>>` `>>>` | 从左向右 |
| `<` `<=` `>` `>=` `instanceof` | 从左向右 |
| `==` `!=` | 从左向右 |
| `&` | 从左向右 |
| `^` | 从左向右 |
| `|` | 从左向右 |
| `&&` | 从左向右 |
| `||` | 从左向右 |
| `?:` | 从右向左 |
| `=` `+=` `-=` `*=` `/=` `&=` `|=` `^=` `<<=` `>>=` `>>>=` | 从右向左 |

#### 枚举类型

* 当变量的取值只在一个有限的集合内时，可以自定义枚举类型。枚举类型包括有限个命名的值，如可以这样定义 `enum Size { SMALL, MEDIUM, LARGE, EXTRA_LARGE };`，之后可以声明这种类型的变量 `Size s = Size.MEDIUM;`
* Size 类型的变量智能存储这个类型声明中给定的某个枚举值，或者 null 值，null 表示这个变量没有设置任何值。

### 3.6 字符串

* 从概念上将，Java 字符串就是 Unicode 字符序列。如字符串 `"Java\u2122"` 是由 5 个 Unicode 字符 J, a, v, a 和™️ 组成的。
* Java 没有内置的字符串类型，而是在标准 Java 类库中提供了一个预定义类 String。每个用双引号括起来的字符串都是 String 类的一个实例。

#### 子串

* String 类的 substring 方法可以从一个较大的字符串提取出一个子串。
* substring 方法可以很容易计算出子串的长度，如字符串 s.substring(a, b) 的长度为 b-a。

#### 拼接

* Java 语言允许使用 `+` 连接（拼接）两个字符串。
* 当将一个字符串与一个非字符串的值进行拼接时，后者被转换为字符串。**实际上，任何一个 Java 对象都可以被转换成字符串。**
* 如果需要把多个字符串放在一起，用一个定界符分割，可以使用静态 join 方法。如 `String all = String.join(" / ", "S", "M", "L", "XL");`

#### 不可变字符串

* String 类没有提供用于修改字符串的方法。由于不能修改 Java 字符串中的字符，在 Java 中将 String 类对象称为不可变字符串。
* **不可变字符串的优点是编译器可以让字符串共享。**Java 设计者认为共享带来的高效率远远胜过于提取、拼接字符串所带来的低效率。
* Java 为来自于文件或键盘的单个字符或较短的字符串汇集成字符串的操作提供了一个独立的类。

#### 检测字符串是否相等

* 可以使用 `equals` 方法检测两个字符串是否相等。如 `s.equals(t)` 或 `"Hello".equals("greeting")`
* 可以使用 `equalsIgnoreCase` 方法不区分大小写的检测两个字符串是否相等
* 一定不要使用 `==` 运算符来检测两个字符串是否相等，这个运算符只能确定两个字符串是否放置在同一个位置上。
* Java 中的 `compareTo` 方法与 C 中的 `strcmp` 完全类似，如 `if (greeting.compareTo("Hello") == 0)`

#### 空串与 null 串

* 空串 `""` 是长度为 0 的字符串，可以调用以下代码检查一个字符串是否为空：`if (str.length() == 0)` 或 `if (str.equals(""))`
    * 空串是一个 Java 对象，有自己的串长度（0）和内容（空）。
* String 类型的变量还可以存放一个特殊的值：null，这表示目前没有任何对象与该变量关联。
    * 要检查一个字符串是否为 null，可以使用：`if (str == null)`
    * 检查一个字符串既不是 null 也不为空串，可以使用：`if (str != null) && (str.length() != 0)`

#### 码点与代码单元

Java 字符串是由 char 值序列组成。char 类型是一个采用 UTF-16 编码表示 Unicode 码点的代码单元。大多数的常用 Unicode 字符使用一个代码单元就可以表示，而辅助字符需要一对代码单元表示。

* length 方法将返回采用 UTF-16 编码表示的给定字符串所需要的代码单元数量
* codePointCount 方法可以得到实际的长度，即码点数量。`int cpCount = greeting.codePointCount(0, greeting.length());`
* 调用 s.charAt(n) 将返回位置 n 的代码单元，n 介于 0 ~ s.length() - 1 之间，`char first = greeting.charAt(0); // first is 'H'`
* 要想得到第 i 个码点，可以使用：`int index = greeting.offsetByCodePoints(0, i); int cp = greeting.codePointAt(index);`

```java
// 遍历一个字符串，依次查看每一个码点
int cp = sentence.codePointAt(i);
if (Character.isSupplementaryCodePoint(cp))
    i += 2;
else
    i++;
// 回退操作
i--;
if (Character.isSurrogate(sentence.charAt(i)))
    i--;
int cp = sentence.codePointAt(i);

// 直接使用 codePoints 方法，生成一个 int 值的流，每个 int 值对应一个码点
// 可以将它转换为一个数组，再完成遍历
int[] codePoints = str.codePoints().toArray();
// 反之，要把一个码点数组转换为一个字符串，可以使用构造函数
String str = new String(codePoints, 0, codePoints.length);
```

#### String API

Java 中的 String 类包含了 50 多个方法。令人惊讶的是绝大多数都很有用，使用也非常频繁。一下列出 `java.lang.String` 的常用方法：

* `char charAt(int index)` 返回给定位置的代码单元，*除非对底层的代码单元感兴趣，否则不需要调用这个方法。
* `int codePointAt(int index)` 5.0 返回从给定位置开始的码点。
* `int offsetByCodePoints(int startIndex, int cpCount)` 5.0 返回从 startIndex 代码点开始，位移 cpCount 后的码点索引。
* `int compareTo(String other)` 按照字典顺序，如果字符串位于 other 之前，返回一个附属；反之返回一个整数；若相等，返回 0。
* `IntStream codePoints()` 8.0 将这个字符串的码点作为一个流返回。调用 toArray 将它们放在一个数组中。
* `new String(int[] codePoints, int offset, int count)` 5.0 用数组中从 offset 开始的 count 个码点构造一个字符串。
* `boolean equals(Object other)` 如果字符串与 other 相等，返回 true。
* `boolean equalsIgnoreCase(String other)` 如果字符串与 other 相等（忽略大小写），返回 true。
* `boolean startsWith(String prefix)`
* `boolean endsWith(String suffix)` 如果字符串以 prefix 开头或 suffix 结尾，则返回 true。
* `int indexOf(String str)`
* `int indexOf(String str, int fromIndex)`
* `int indexOf(int cp)`
* `int indexOf(int cp, int fromIndex)` 返回与字符串 str 或代码点 cp 匹配的第一个子串的开始位置。这个位置从索引 0 或 fromIndex 开始计算。如果在原始串中不存在 str，返回 -1。
* `int lastIndexOf(String str)`
* `int lastIndexOf(String str, int fromIndex)`
* `int lastIndexOf(int cp)`
* `int lastIndexOf(int cp, int fromIndex)` 返回与字符串 str 或代码点 cp 匹配的最后一个子串的开始位置。这个位置从原始串尾端或 fromIndex 开始计算。
* `int length()` 返回字符串的长度。
* `int codePointCount(int startIndex, int endIndex)` 5.0 返回 startIndex 和 endIndex - 1 之间的代码点数量。没有配成对的代用字符将计入代码点。
* `String replace(CharSequence oldString, CharSequence newString)` 返回一个新字符串。这个字符串用 newString 代替原始字符串中所有的 oldString。可以用 String 或 StringBuilder 对象作为 CharSequence 参数。
* `String substring(int beginIndex)`
* `String substring(int beginIndex, int endIndex)` 返回一个新字符串。这个字符串包含原始字符串中从 beginIndex 到串尾或 endIndex - 1 的所有代码单元。
* `String toLowerCase()`
* `String toUpperCase()` 返回一个新字符串。这个字符串将原始字符串中的大写/小写字母改为小写/大写字母。
* `String trim()` 返回一个新字符串。这个字符串将删除了原始字符串头部和尾部的空格。
* `String join(CharSequence delimiter, CharSequence... elements)` 8.0 返回一个新字符串，用给定的定界符连接所有元素。

注：CharSequence 类型的参数是一种接口类型，所有字符串都属于这个接口。

#### 构建字符串

在 JDK5.0 中，引入了 StringBuilder 类，可以将许多小段的字符串构建成一个字符串。另外 StringBuffer 类与 StringBuilder 类相同，但其效率稍有些低，允许采用多线程的方式执行添加或删除字符的操作。**若所有字符串在一个单线程中编辑（通常都是这样），则建议使用 StringBuilder 类。**

```java
// 构建一个空的字符串构建器
StringBuilder builder = new StringBuilder();
// 当每次需要添加一部分内容时，就调用 append 方法
builder.append(ch); // appends a single character
builder.append(str); // appends a string
// 当需要构建字符串时调用 toString 方法就可以得到一个 String 对象，其中包含了构建器中的字符序列
String completedString = builder.toString();
```

StringBuilder 类中的重要方法：

* `StringBuilder()` 构造一个空的字符串构建器。
* `int length()` 返回构建器或缓冲器中的代码单元数量。
* `StringBuilder append(String str)` 追加一个字符串并返回 this。
* `StringBuilder append(char c)` 追加一个代码单元并返回 this。
* `StringBuilder appendCodePoint(int cp)` 追加一个代码点，并将其转换为一个或两个代码单元并返回 this。
* `void setCharAt(int i, char c)` 将第 i 个代码单元设置为 c。
* `StringBuilder insert(int offset, String str)` 在 offset 位置插入一个字符串并返回 this。
* `StringBuilder insert(int offset, Char c)` 在 offset 位置插入一个代码单元并返回 this。
* `StringBuilder delete(int startIndex, int endIndex)` 删除偏移量从 startIndex 到 endIndex - 1 的代码单元并返回 this。
* `String toString()` 返回一个与构建器或缓冲器内容相同的字符串。

### 3.7 输入输出

#### 读取输入

```java
import java.util.*;

/**
 * This program demonstrates console input.
 * @version
 * @author
 * @date
 */
 public class InputTest
 {
    public static void main(String[] args)
    {
        // 需要构造一个 Scanner 对象，并与标准输入流 System.in 关联
        Scanner in = new Scanner(System.in);

        // get first input
        System.out.print("What is your name? ");
        String name = in.nextLine();

        // get second input
        System.out.print("How old are you? ");
        int age = in.nextInt(); // 读取浮点数调用 nextDouble 方法

        // display output on console
        System.out.println("Hello, " + name + ", Next year, you'll be " + (age + 1));
    }
 }
```

Scanner 类的输入是可见的，不适用于从控制台读取密码。Java SE 6 引入了 Console 类来实现这个目的。*为了安全起见，返回的密码存放在一维字符数组中，而不是字符串中。在对密码进行处理后，应该马上用一个填充值覆盖数组元素。*采用 Console 对象处理输入不如 Scanner 方便，每次只能读取一行输入，而没有能够读取一个单词或一个数值的方法。

```java
Console cons = System.console();
String username = cons.readLine("User name: ");
char[] password = cons.readPassword("Password: ");
```

* 了解 java.util.Scanner 类 5.0
* 了解 java.lang.System 类 1.0
* 了解 java.io.Console 类 6.0

#### 格式化输出

#### 文件输入与输出

### 3.8 控制流程

#### 块作用域

#### 条件语句

#### 循环

#### 确定循环

#### 多重选择：switch 语句

#### 中断控制流程语句 break

### 3.9 大数值

### 3.10 数组

#### for each 循环

#### 数组初始化以及匿名数组

#### 数组拷贝

#### 命令行参数

#### 数组排序

#### 多维数组

#### 不规则数组

---

## 第 4 章 对象与类

### 4.1 面向对象程序设计概述

### 4.2 使用预定义类

### 4.3 用户自定义类

### 4.4 静态域与静态方法

### 4.5 方法参数
 
### 4.6 对象构造

### 4.7 包

### 4.8 类路径

### 4.9 文档注释

### 4.10 类设计技巧

---

## 第 5 章 继承

### 5.1 类、超类和子类

### 5.2 Object：所有类的超类

### 5.3 泛型数组列表

### 5.4 对象包装器与自动装箱

### 5.5 参数数量可变的方法

### 5.6 枚举类

### 5.7 反射

### 5.8 继承的设计技巧

---
## 第 6 章 接口、lambda 表达式与内部类

### 6.1 接口

### 6.2 接口示例

### 6.3 lambda 表达式

### 6.4 内部类

### 6.5 代理

---
## 第 7 章 异常、断言和日志

### 7.1 处理错误

### 7.2 捕获异常

### 7.3 使用异常机制的技巧

### 7.4 使用断言

### 7.5 记录日志

### 7.6 调试技巧

---
## 第 8 章 泛型程序设计

### 8.1 为什么要使用泛型程序设计

### 8.2 定义简单泛型类

### 8.3 泛型方法

### 8.4 类型变量的限定

### 8.5 泛型代码和虚拟机

### 8.6 约束与局限性

### 8.7 泛型类型的集成规则

### 8.8 通配符类型

### 8.9 反射和泛型

---
## 第 9 章 集合

### 9.1 Java 集合框架

### 9.2 具体的集合

### 9.3 映射

### 9.4 视图与包装器

### 9.5 算法

### 9.6 遗留的集合

---
## 第 13 章 部署 Java 应用程序

### 13.1 JAR 文件

### 13.2 应用首选项的存储

### 13.3 服务加载器

### 13.4 applet

### 13.5 Java Web Start

---
## 第 14 章 并发

### 14.1 什么事线程

### 14.2 中断线程

### 14.3 线程状态

### 14.4 线程属性

### 14.5 同步

### 14.6 阻塞队列

### 14.7 线程安全的集合

### 14.8 Callable 与 Future

### 14.9 执行器

### 14.10 同步器

### 14.11 线程与 Swing

---

## 附录 A Java 关键字
