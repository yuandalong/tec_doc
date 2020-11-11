# 面试题
## 基础知识

### Q1  var，val和def三个关键字之间的区别？

var是变量声明关键字，类似于Java中的变量，变量值可以更改，但是变量类型不能更改。
val常量声明关键字。
def 关键字用于创建方法（注意方法和函数的区别）
还有一个lazy val（惰性val）声明，意思是当需要计算时才使用，避免重复计算


代码示例：
```scala
var x = 3 //  x是Int类型
x = 4      // 
x = "error" // 类型变化，编译器报错'error: type mismatch'

val y = 3
y = 4        //常量值不可更改，报错 'error: reassignment to val'

def fun(name: String) = "Hey! My name is: " + name
fun("Scala") // "Hey! My name is: Scala"

//注意scala中函数式编程一切都是表达式
lazy val x = {
  println("computing x")
  3
}
val y = {
  println("computing y")
  10
}
x+x  //
y+y  // x 没有计算, 打印结果"computing y" 
```

### Q2 trait（特质）和abstract class（抽象类）的区别？
（1）一个类只能集成一个抽象类，但是可以通过with关键字继承多个特质；
（2）抽象类有带参数的构造函数，特质不行（如 trait t（i：Int）{} ，这种声明是错误的）


### Q3 object和class的区别？

object是类的单例对象，开发人员无需用new关键字实例化。如果对象的名称和类名相同，这个对象就是伴生对象（深入了解请参考问题Q7）


```scala
//声明一个类
class MyClass(number: Int, text: String) {
  def classMethod() = println(text)
}
//声明一个对象
object MyObject{
  def objectMethod()=println("object")
}
new MyClass(3,"text").classMethod() //打印结果test，需要实例化类
Myclass.classMethod()  //无法直接调用类的方法
MyObject.objectMethod() //打印结果object，对象可以直接调用方法
```

### Q4 case class （样本类）是什么？

样本类是一种不可变且可分解类的语法糖，这个语法糖的意思大概是在构建时，自动实现一些功能。样本类具有以下特性：
（1）自动添加与类名一致的构造函数（这个就是前面提到的伴生对象，通过apply方法实现），即构造对象时，不需要new；
（2）样本类中的参数默认添加val关键字，即参数不能修改；
（3）默认实现了toString，equals，hashcode，copy等方法；
（4）样本类**可以通过==比较两个对象，并且不在构造方法中定义的属性不会用在比较上**。

```scala
//声明一个样本类
case class MyCaseClass(number: Int, text: String, others: List[Int]){
 println(number)
}
//不需要new关键字，创建一个对象
val dto = MyCaseClass(3, "text", List.empty) //打印结果3

//利用样本类默认实现的copy方法
dto.copy(number = 5) //打印结果5

val dto2 = MyCaseClass(3, "text", List.empty)
pringln(dto == dto2) // 返回true，两个不同的引用对象
class MyClass(number: Int, text: String, others: List[Int]) {}
val c1 = new MyClass(1, "txt", List.empty)
val c2 = new MyClass(1, "txt", List.empty)
println(c1 == c2 )// 返回false,两个不同的引用对象,因为不是case class
```

### Q5 Java和Scala 异步计算的区别？


这里作者的意思是他大概也不清楚，请阅读这个 [really clean and simple answer on StackOverflow](https://link.jianshu.com/?t=http://stackoverflow.com/a/31368177/4398050)，我个人理解还不到位后续补上。


### Q6 unapply 和apply方法的区别， 以及各自使用场景？


先讲一个概念——提取器，它实现了构造器相反的效果，构造器从给定的参数创建一个对象，然而提取器却从对象中提取出构造该对象的参数，scala标准库预定义了一些提取器，如上面提到的样本类中，会自动创建一个伴生对象（包含apply和unapply方法）。
为了成为一个提取器，unapply方法需要被伴生对象。
apply方法是为了自动实现样本类的对象，无需new关键字。


### Q7  伴生对象是什么？

前面已经提到过，伴生对象就是与类名相同的对象，伴生对象可以访问类中的私有变量，类也可以访问伴生对象中的私有方法，类似于Java类中的静态方法。伴生对象必须和其对应的类定义在相同的源文件。


```scala
//定义一个类
class MyClass(number: Int, text: String) {

  private val classSecret = 42

  def x = MyClass.objectSecret + "?"  // MyClass.objectSecret->在类中可以访问伴生对象的方法，在类的外部则无法访问
}

//定义一个伴生对象
object MyClass { // 和类名称相同
  private val objectSecret = "42"

  def y(arg: MyClass) = arg.classSecret -1 // arg.classSecret -> 在伴生对象中可以访问类的常量
}

MyClass.objectSecret // 无法访问
MyClass.classSecret // 无法访问

new MyClass(-1, "random").objectSecret // 无法访问
new MyClass(-1, "random").classSecret // 无法访问
```

### Q8 Scala类型系统中Nil, Null, None, Nothing四个类型的区别？

先看一幅Scala类型图

![Scala类型图](media/scala1.png)



Null是一个trait（特质），是所以引用类型AnyRef的一个子类型，null是Null唯一的实例。
Nothing也是一个trait（特质），是所有类型Any（包括值类型和引用类型）的子类型，它不在有子类型，它也没有实例，实际上为了一个方法抛出异常，通常会设置一个默认返回类型。
Nil代表一个List空类型，等同List[Nothing]
None是Option monad的空标识（深入了解请参考问题Q11）

### Q9 Unit类型是什么？

Unit代表没有任何意义的值类型，类似于java中的void类型，他是anyval的子类型，仅有一个实例对象"( )"


### Q10 call-by-value和call-by-name求值策略的区别？

（1）call-by-value是在调用函数**之前**计算；
（2）call-by-name是在需要时计算


```scala
//声明第一个函数
def func(): Int = {
  println("computing stuff....")
  42 // return something
}
//声明第二个函数，scala默认的求值就是call-by-value
def callByValue(x: Int) = {
  println("1st x: " + x)
  println("2nd x: " + x)
}
//声明第三个函数，用=>表示call-by-name求值
def callByName(x: => Int) = {
  println("1st x: " + x)
  println("2nd x: " + x)
}

//开始调用

//call-by-value求值
callByValue(func())   
//输出结果
//computing stuff....  
//1st x: 42  
//2nd x: 42

//call-by-name求值
callByName(func())   
//输出结果
//computing stuff....  
//1st x: 42  
//此时func会再运行一次
//computing stuff....
//2nd x: 42
```

### Q11 Option类型的定义和使用场景？

在Java中，null是一个关键字，不是一个对象，当开发者希望返回一个空对象时，却返回了一个关键字，为了解决这个问题，Scala建议开发者返回值是空值时，使用Option类型，在Scala中null是Null的唯一对象，会引起异常，Option则可以避免。Option有两个子类型，Some和None（空值）


```scala
val person: Person = getPersonByIdOnDatabaseUnsafe(id = 4) // 如果没有id=4的person时，返回null对象
println(s"This person age is ${person.age}") //如果是null，抛出异常

val personOpt: Option[Person] = 
getPersonByIdOnDatabaseSafe(id = 4) // 如果没有id=4的person时，返回None类型

personOpt match {
  case Some(p) => println(s"This person age is ${p.age}")
  case None => println("There is no person with that id")
}
```

### Q12 yield如何工作？

yield用于循环迭代中生成新值，yield是comprehensions的一部分，是多个操作（foreach, map, flatMap, filter or withFilter）的composition语法糖。（深入了解请参考问题Q14）


```scala
// <-表示循环遍历
scala> for (i <- 1 to 5) yield i * 2 
res0: scala.collection.immutable.IndexedSeq[Int] = Vector(2, 4, 6, 8, 10)
```

### Q13 解释隐示参数的优先权

在Scala中implicit的功能很强大。当编译器寻找implicits时，如果不注意隐式参数的优先权，可能会引起意外的错误。因此编译器会按顺序查找隐式关键字。顺序如下：
（1）当前类声明的implicits ；
（2）导入包中的 implicits；
（3）外部域（声明在外部域的implicts）；
（4）inheritance
（5）package object
（6）implicit scope like companion objects
一个参考文章：set of examples can be found here.
个人推荐一篇文档：Where do Implicits Come From?

### Q14 comprehension（推导式）的语法糖是什么操作？


comprehension（推导式）是若干个操作组成的替代语法。如果不用yield关键字，comprehension（推导式）可以被forech操作替代，或者被map/flatMap，filter代替。

```scala
//三层循环嵌套
for {
  x <- c1
  y <- c2
  z <- c3 if z > 0
} yield {...}

//上面的可转换为
c1.flatMap(x => c2.flatMap(y => c3.withFilter(z => z > 0).map(z => {...})))
```

更多例子 [More examples by Loïc Descotte.](https://link.jianshu.com/?t=https://gist.github.com/loicdescotte/4044169)

### Q15 Streams：当使用Scala Steams时需要考虑什么？Scala的Streams内部使用什么技术？

还没有理解，暂时不翻译，后续补上。

### Q16 什么是vaule class？

开发时经常遇到这个的问题，当你使用integer时，希望它代表一些东西，而不是全部东西，例如，一个integer代表年龄，另一个代表高度。由于上述原因，我们考虑包裹原始类型生成一个新的有意义的类型（如年龄类型和高度类型）。
Value classes 允许开发者安全的增加一个新类型，避免运行时对象分配。有一些 必须进行分配的情况 and 限制,但是基本的思想是：在编译时，通过使用原始类型替换值类实例，删除对象分配。更多细节More details can be found on its SIP.

### Q17 Option ，Try 和 Either 三者的区别？

这三种monads允许我们显示函数没有按预期执行的计算结果。
Option表示可选值，它的返回类型是Some（代表返回有效数据）或None（代表返回空值）。
Try类似于Java中的try/catch，如果计算成功，返回Success的实例，如果抛出异常，返回Failure。
Either可以提供一些计算失败的信息，Either有两种可能返回类型：预期/正确/成功的 和 错误的信息。


```scala
//返回一个Either类型
def personAge(id: Int): Either[String, Int] = {
  val personOpt: Option[Person] = DB.getPersonById(id) //返回Option类型，如果为null返回None，否则返回Some

  personOpt match {
    case None => Left(s"Could not get person with id: $id")  //Left 包含错误或无效值
    case Some(person) => Right(person.age)                    //Right包含正确或有效值
  }
```


### Q18 什么是函数柯里化？

柯里化技术是一个接受多个参数的函数转化为接受其中几个参数的函数。经常被用来处理高阶函数。

```scala
def add(a: Int)(b: Int) = a + b

val add2 = add(2)(_)  //_ 表示不只一个的意思

scala> add2(3)
res0: Int = 5
```

### Q19 什么是尾递归？

正常递归，每一次递归步骤，需要保存信息到堆栈里面，当递归步骤很多时，导致堆栈溢出。
尾递归就是为了解决上述问题，在尾递归中所有的计算都是在递归之前调用，
编译器可以利用这个属性避免堆栈错误，尾递归的调用可以使信息不插入堆栈，从而优化尾递归。
使用 @tailrec 标签可使编译器强制使用尾递归。

```scala
def sum(n: Int): Int = { // 求和计算
  if(n == 0) {
    n
  } else {
    n + sum(n - 1)
  }
}

@tailrec  //告诉编译器
def tailSum(n: Int, acc: Int = 0): Int = {
  if(n == 0) {
    acc
  } else {
    tailSum(n - 1, acc + n)
  }
}

sum(5)
5 + sum(4) // 暂停计算 => 需要添加信息到堆栈
5 + (4 + sum(3))
5 + (4 + (3 + sum(2)))
5 + (4 + (3 + (2 + sum(1))))
5 + (4 + (3 + (2 + 1)))
15

tailSum(5) // tailSum(5, 0) 默认值是0
tailSum(4, 5) // 不需要暂停计算
tailSum(3, 9)
tailSum(2, 12)
tailSum(1, 14)
tailSum(0, 15)
15
```
### Q20 什么是高阶函数？

高阶函数指能接受或者返回其他函数的函数，scala中的filter map flatMap函数都能接受其他函数作为参数。


翻译结束

个人总结
1 monads概念的需要进一步理解
2.Scala Steams使用的内部技术
3 Scala中隐形参数的使用
4 高阶函数的灵活运用

作者：IIGEOywq
链接：https://www.jianshu.com/p/ace2bb24dc11
來源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

---

2.Scala数据类型有哪些？

|数据类型|描述|
| --- | --- |
|Byte	|8位有符号补码整数。数值区间为 -128 到 127|
|Short|	16位有符号补码整数。数值区间为 -32768 到 32767|
|Int| 	32位有符号补码整数。数值区间为 -2147483648 到 2147483647|
|Long|	64位有符号补码整数。数值区间为<br> -9223372036854775808 到 9223372036854775807|
|Float|	32位IEEE754单精度浮点数|
|Double|	64位IEEE754单精度浮点数|
|Char|	16位无符号Unicode字符, 区间值为 U+0000 到 U+FFFF|
|String|	字符序列|
|Boolean|	true或false|
|Unit|	表示无值，和其他语言中void等同。<br>用作不返回任何结果的方法的结果类型。Unit只有一个实例值，写成()。|
|Null|	null 或空引用|
|Nothing|Nothing类型在Scala的类层级的最低端；<br>它是任何其他类型的子类型。|
|Any|	Any是所有其他类的超类|
|AnyRef|	AnyRef类是Scala里所有引用类(reference class)的基类|
上表中列出的数据类型都是对象，也就是说scala没有java中的原生类型。在scala是可以对数字等基础类型调用方法的。


3.String 对象是可变还是不可变？假如要创建一个可以修改的字符串，应该使用哪个类？

在 Scala 中，字符串的类型实际上是 Java String，它本身没有 String 类。

在 Scala 中，String 是一个不可变的对象，所以该对象不可被修改。这就意味着你如果修改字符串就会产生一个新的字符串对象。但其他对象，如数组就是可变的对象。

创建字符串

创建字符串实例如下：

var greeting = "Hello World!";

或

var greeting:String = "Hello World!";
你不一定为字符串指定 String 类型，因为 Scala 编译器会自动推断出字符串的类型为 String。

可修改字符串String Builder 类

String 对象是不可变的，如果你需要创建一个可以修改的字符串，可以使用 String Builder 类，如下实例:

object Test {
   def main(args: Array[String]) {
      val buf = new StringBuilder;
      buf += 'a'
      buf ++= "bcdef"
      println( "buf is : " + buf.toString );
   }
}
皮皮blog



String 方法

java.lang.String 中常用的方法

序号	方法及描述
1	
char charAt(int index)
返回指定位置的字符
2	
int compareTo(Object o)
比较字符串与对象
3	
int compareTo(String anotherString)
按字典顺序比较两个字符串
4	
int compareToIgnoreCase(String str)
按字典顺序比较两个字符串，不考虑大小写
5	
String concat(String str)
将指定字符串连接到此字符串的结尾。 同样你也可以使用加号(+)来连接。
6	
boolean contentEquals(StringBuffer sb)
将此字符串与指定的 StringBuffer 比较。
7	
static String copyValueOf(char[] data)
返回指定数组中表示该字符序列的 String
8	
static String copyValueOf(char[] data, int offset, int count)
返回指定数组中表示该字符序列的 String
9	
boolean endsWith(String suffix)
测试此字符串是否以指定的后缀结束
10	
boolean equals(Object anObject)
将此字符串与指定的对象比较
11	
boolean equalsIgnoreCase(String anotherString)
将此 String 与另一个 String 比较，不考虑大小写
12	
byte getBytes()
使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中
13	
byte[] getBytes(String charsetName
使用指定的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中
14	
void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)
将字符从此字符串复制到目标字符数组
15	
int hashCode()
返回此字符串的哈希码
16	
int indexOf(int ch)
返回指定字符在此字符串中第一次出现处的索引
17	
int indexOf(int ch, int fromIndex)
返返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索
18	
int indexOf(String str)
返回指定子字符串在此字符串中第一次出现处的索引
19	
int indexOf(String str, int fromIndex)
返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始
20	
String intern()
返回字符串对象的规范化表示形式
21	
int lastIndexOf(int ch)
返回指定字符在此字符串中最后一次出现处的索引
22	
int lastIndexOf(int ch, int fromIndex)
返回指定字符在此字符串中最后一次出现处的索引，从指定的索引处开始进行反向搜索
23	
int lastIndexOf(String str)
返回指定子字符串在此字符串中最右边出现处的索引
24	
int lastIndexOf(String str, int fromIndex)
返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索
25	
int length()
返回此字符串的长度
26	
boolean matches(String regex)
告知此字符串是否匹配给定的正则表达式
27	
boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)
测试两个字符串区域是否相等
28	
boolean regionMatches(int toffset, String other, int ooffset, int len)
测试两个字符串区域是否相等
29	
String replace(char oldChar, char newChar)
返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的
30	
String replaceAll(String regex, String replacement
使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串
31	
String replaceFirst(String regex, String replacement)
使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串
32	
String[] split(String regex)
根据给定正则表达式的匹配拆分此字符串
33	
String[] split(String regex, int limit)
根据匹配给定的正则表达式来拆分此字符串
34	
boolean startsWith(String prefix)
测试此字符串是否以指定的前缀开始
35	
boolean startsWith(String prefix, int toffset)
测试此字符串从指定索引开始的子字符串是否以指定前缀开始。
36	
CharSequence subSequence(int beginIndex, int endIndex)
返回一个新的字符序列，它是此序列的一个子序列
37	
String substring(int beginIndex)
返回一个新的字符串，它是此字符串的一个子字符串
38	
String substring(int beginIndex, int endIndex)
返回一个新字符串，它是此字符串的一个子字符串
39	
char[] toCharArray()
将此字符串转换为一个新的字符数组
40	
String toLowerCase()
使用默认语言环境的规则将此 String 中的所有字符都转换为小写
41	
String toLowerCase(Locale locale)
使用给定 Locale 的规则将此 String 中的所有字符都转换为小写
42	
String toString()
返回此对象本身（它已经是一个字符串！）
43	
String toUpperCase()
使用默认语言环境的规则将此 String 中的所有字符都转换为大写
44	
String toUpperCase(Locale locale)
使用给定 Locale 的规则将此 String 中的所有字符都转换为大写
45	
String trim()
删除指定字符串的首尾空白符
46	
static String valueOf(primitive data type x)
返回指定类型参数的字符串表示形式
（注：引用自http://blog.csdn.net/pipisorry/article/details/52902348）



4.转义字符用什么符号？

Scala 转义字符

下表列出了常见的转义字符：

转义字符	Unicode	描述
\b	\u0008	退格(BS) ，将当前位置移到前一列
\t	\u0009	水平制表(HT) （跳到下一个TAB位置）
\n	\u000a	换行(LF) ，将当前位置移到下一行开头
\f	\u000c	换页(FF)，将当前位置移到下页开头
\r	\u000d	回车(CR) ，将当前位置移到本行开头
\"	\u0022	代表一个双引号(")字符
\'	\u0027	代表一个单引号（'）字符
\\	\u005c	代表一个反斜线字符 '\'
0 到 255 间的 Unicode 字符可以用一个八进制转义序列来表示，即反斜线‟\‟后跟 最多三个八进制。

在字符或字符串中，反斜线和后面的字符序列不能构成一个合法的转义序列将会导致 编译错误。

以下实例演示了一些转义字符的使用：

object Test {
   def main(args: Array[String]) {
      println("Hello\tWorld\n\n" );
   }
} 
$ scalac Test.scala
$ scala Test
Hello    World
（注：引用自菜鸟教程）



5.IF...ELSE 语法是什么？

if(布尔表达式){
   // 如果布尔表达式为 true 则执行该语句块
}else{
   // 如果布尔表达式为 false 则执行该语句块
}



6.循环语句哪三种，分别语法是什么？怎样退出循环？

while(condition)
{
   statement(s);
}

在这里，statement(s) 可以是一个单独的语句，也可以是几个语句组成的代码块。
condition 可以是任意的表达式，当为任意非零值时都为 true。当条件为 true 时执行循环。 当条件为 false 时，退出循环，程序流将继续执行紧接着循环的下一条语句。



do {
   statement(s);
} while( condition );



for( var x <- Range ){
   statement(s);
}

以上语法中，Range 可以是一个数字区间表示 i to j ，或者 i until j。左箭头 <- 用于为变量 x 赋值。

for( var x <- List ){
   statement(s);
}

以上语法中， List 变量是一个集合，for 循环会迭代所有集合的元素。

for( var x <- List
      if condition1; if condition2...
   ){
   statement(s);
}

以上是在 for 循环中使用过滤器的语法。

var retVal = for{ var x <- List
     if condition1; if condition2...
}yield x

你可以将 for 循环的返回值作为一个变量存储。

大括号中用于保存变量和条件，retVal 是变量， 循环中的 yield 会把当前的元素记下来，保存在集合中，循环结束后将返回该集合。



当在循环中使用 break 语句，在执行到该语句时，就会中断循环并执行循环体之后的代码块。

// 导入以下包
import scala.util.control._
// 创建 Breaks 对象
val loop = new Breaks;
// 在 breakable 中循环
loop.breakable{
    // 循环
    for(...){
       ....
       // 循环中断
       loop.break;
   }
}



7.函数中 Unit是什么意思？

Scala中的Unit类型类似于java中的void，无返回值。主要的不同是在Scala中可以有一个Unit类型值，也就是（），然而java中是没有void类型的值的。除了这一点，Unit和void是等效的。一般来说每一个返回void的java方法对应一个返回Unit的Scala方法。



8.Scala怎样定义一个不带入参，不返回值的函数

def functionName  = {
   function body
   return [expr]
}



9.Scala怎样定义一个带入参，返回值的函数

def functionName ([参数列表]) : [return type] = {
   function body
   return [expr]
}



10.什么是闭包？（******************）

闭包是一个函数，返回值依赖于声明在函数外部的一个或多个变量。

var factor = 3  
val multiplier = (i:Int) => i * factor 

闭包的实质就是代码与用到的非局部变量的混合，即：

闭包 = 代码 + 用到的非局部变量


11.val a = 10，怎样将a转为double类型、String类型？

a.toString 

a.toDouble



12.Scala函数中是把方法体的最后一行作为返回值，需不需要显示调用return？

不需要



13.怎样定义一个字符串数组？下标是从1开始的吗？

从0开始val numArr = new Array[Int](10)
从0开始val a=Array("a","b")  

14.1 to 10 ==> 1.to(10)，10包含不包含？

包含



15.Range(1, 10)，10包含不包含？for( a <- 1 until 10){ println( "Value of a: " + a );  }，10包含不包含？

都不包含



16.Scala 模式匹配语法是什么？

   def matchTest(x: Int): String = x match {
      case 1 => "one"
   }




17.异常报错的语法？

import java.io.FileReader
import java.io.FileNotFoundException
import java.io.IOException

object Test {
   def main(args: Array[String]) {
      try {
         val f = new FileReader("input.txt")
      } catch {
         case ex: FileNotFoundException => {
            println("Missing file exception")
         }
         case ex: IOException => {
            println("IO Exception")
         }
      } finally {
         println("Exiting finally...")
      }
   }
}



18.Array、ArrayBuffer，谁是定长？谁是变长？

Array是定长、ArrayBuffer是变长



19.什么是隐式转换函数？什么场景下用？怎样定义？

我们经常引入第三方库，但当我们想要扩展新功能的时候通常是很不方便的，因为我们不能直接修改其代码。scala提供了隐式转换机制和隐式参数帮我们解决诸如这样的问题。

implicit def file2Array(file: File): Array[String] = file.lines



20.Scala面向对象,三大特性是什么？什么区别？

继承：父和子的关系
封装：属性、方法
多态：父类引用指向子类对象 



21.Scala 基本语法需要注意点？

1.类名 - 对于所有的类名的第一个字母要大写吗？

是的 class MyFirstScalaClass

2.方法名称 - 所有的方法名称的第一个字母用小写吗？

是的 def myMethodName()



22.对象是什么？类是什么？怎样在IDEA创建文件？

类是对象的抽象，而对象是类的具体实例。类是抽象的，不占用内存，而对象是具体的，占用存储空间。类是用于创建对象的蓝图，它是一个定义包括在特定类型的对象中的方法和变量的软件模板。

new->scala class



23.变长数组ArrayBuffer的系列问题

 import scala.collection.mutable.ArrayBuffer
var c = new ArrayBuffer[Int]();
1. 在尾部添加一个元素
c += 2
2. 在尾部添加多个元素
c += (3,4,5) 
3. 追加集合
c ++= Array(6,7,8,9) 
4. 指定位置添加元素
c.insert(3, 33)  //在下标3之前插入元素  
5. 移除尾部n个元素
c.trimEnd(n)
6. 移除开头n个元素
c.trimStart(n)
7. 移除某个位置的元素
c.remove(3) 
8. 移除从下标为n开始（包括n）的count个元素
c.remove(n, count)
9. ArrayBuffer 转 Array
c.toArray
10. Array 转 ArrayBuffer
c.toBuffer
--------------------- 
作者：leofionn 
来源：CSDN 
原文：https://blog.csdn.net/qq_36142114/article/details/79461189 
版权声明：本文为博主原创文章，转载请附上博文链接！


## 特质（trait）
### 将trait作为接口使用
Scala中的Triat是一种特殊的概念

首先我们可以将Trait作为接口来使用，此时的Triat就与Java中的接口非常类似

在triat中可以定义抽象方法，就与抽象类中的抽象方法一样，**只要不给出方法的具体实现即可**

类可以使用extends关键字继承trait，注意，这里不是implement，而是extends，在scala中没有implement的概念，无论继承类还是trait，统一都是extends

类继承trait后，必须实现其中的抽象方法，实现时**不需要使用override关键字**

scala**不支持对类进行多继承**，但是支持多重继承trait，使用with关键字即可
```scala
trait HelloTrait {
  def sayHello(name: String)
}
trait MakeFriendsTrait {
  def makeFriends(p: Person)
}
class Person(val name: String) extends HelloTrait with MakeFriendsTrait with Cloneable with Serializable {
  def sayHello(name: String) = println("Hello, " + name)
  def makeFriends(p: Person) = println("Hello, my name is " + name + ", your name is " + p.name)
}
```

### 在Trait中定义具体方法

Scala中的Triat可以不是只定义抽象方法，还可以定义具体方法，此时trait更像是包含了通用工具方法的东西有一个专有的名词来形容这种情况，就是说trait的功能混入了类
举例来说，trait中可以包含一些很多类都通用的功能方法，比如打印日志等等，spark中就使用了trait来定义了通用的日志打印方法

```scala
trait Logger {
  def log(message: String) = println(message)
}

class Person(val name: String) extends Logger {
  def makeFriends(p: Person) {
    println("Hi, I'm " + name + ", I'm glad to make friends with you, " + p.name)
    log("makeFriends methdo is invoked with parameter Person[name=" + p.name + "]")
  }
}
```

### 在Trait中定义具体字段
Scala中的Triat可以定义具体field，此时继承trait的类就自动获得了trait中定义的field
但是这种获取field的方式与继承class是不同的：如果是继承class获取的field，实际是定义在父类中的；而**继承trait获取的field，就直接被添加到了类中**

```scala
trait Person {
  val eyeNum: Int = 2
}

class Student(val name: String) extends Person {
  def sayHello = println("Hi, I'm " + name + ", I have " + eyeNum + " eyes.")
}
```

### 在Trait中定义抽象字段
Scala中的Triat可以定义抽象field，而trait中的具体方法则可以基于抽象field来编写
但是继承trait的类，则必须覆盖抽象field，提供具体的值


```scala
trait SayHello {
  val msg: String
  def sayHello(name: String) = println(msg + ", " + name)
}

class Person(val name: String) extends SayHello {
  val msg: String = "hello"
  def makeFriends(p: Person) {
    sayHello(p.name)
    println("I'm " + name + ", I want to make friends with you!")
  }
}
```

### 为实例混入trait
有时我们可以在创建类的对象时，指定该对象混入某个trait，这样，**就只有这个对象混入该trait的方法，而类的其他对象则没有**

```scala
trait Logged {
  def log(msg: String) {}
}
trait MyLogger extends Logged {
  override def log(msg: String) { println("log: " + msg) }
}  
class Person(val name: String) extends Logged {
    def sayHello { println("Hi, I'm " + name); log("sayHello is invoked!") }
}

val p1 = new Person("leo")
p1.sayHello
val p2 = new Person("jack") with MyLogger
p2.sayHello
```

### trait调用链
Scala中支持让类继承多个trait后，依次调用多个trait中的同一个方法，只要让多个trait的同一个方法中，在**最后都执行super.方法**即可
类中调用多个trait中都有的这个方法时，首先会从**最右边**的trait的方法开始执行，然后依次往左执行，形成一个调用链条
这种特性非常强大，其实就相当于设计模式中的责任链模式的一种具体实现依赖

```scala
trait Handler {
  def handle(data: String) {}
}
trait DataValidHandler extends Handler {
  override def handle(data: String) {
    println("check data: " + data)
    super.handle(data)
  } 
}
trait SignatureValidHandler extends Handler {
  override def handle(data: String) {
    println("check signature: " + data)
    super.handle(data)
  }
}
class Person(val name: String) extends SignatureValidHandler with DataValidHandler {
  def sayHello = { println("Hello, " + name); handle(name) }
}
```

### 在trait中覆盖抽象方法
在trait中，是可以覆盖父trait的抽象方法的
**但是覆盖时，如果使用了super.方法的代码，则无法通过编译**。因为super.方法就会去调用父trait的抽象方法，**此时子trait的该方法还是会被认为是抽象的**
此时如果要通过编译，就得给子trait的方法加上abstract override修饰

```scala
trait Logger {
  def log(msg: String)
}

trait MyLogger extends Logger {
  abstract override def log(msg: String) { super.log(msg) }
}
```

### 混合使用trait的具体方法和抽象方法
在trait中，可以混合使用具体方法和抽象方法
可以让具体方法依赖于抽象方法，而抽象方法则放到继承trait的类中去实现
这种trait其实就是设计模式中的模板设计模式的体现

```scala
trait Valid {
  def getName: String
  def valid: Boolean = {
    getName == "leo"    
  }
}
class Person(val name: String) extends Valid {
  println(valid)
  def getName = name
}
```

### trait的构造机制
在Scala中，**trait是没有接收参数的构造函数的，这是trait与class的唯一区别**，但是如果需求就是要trait能够对field进行初始化，该怎么办呢？
可以使用Scala中非常特殊的一种高级特性——**提前定义**，另外一种方式就是使用lazy value

```scala
trait SayHello {
  val msg: String
  println(msg.toString)
}

class Person
val p = new {
  val msg: String = "init"
} with Person with SayHello

class Person extends {
  val msg: String = "init"
} with SayHello {}

// 另外一种方式就是使用lazy value
trait SayHello {
  lazy val msg: String = null
  println(msg.toString)
}
class Person extends SayHello {
  override lazy val msg: String = "init"
}
```

### trait继承class
在Scala中，trait也可以继承自class，此时这个class就会成为**所有继承该trait的类的父类**

```scala
class MyUtil {
  def printMessage(msg: String) = println(msg)
}

trait Logger extends MyUtil {
  def log(msg: String) = printMessage("log: " + msg)
}

class Person(val name: String) extends Logger {
  def sayHello {
    log("Hi, I'm " + name)
    printMessage("Hi, I'm " + name)
  }
}
```

# 知识点
## Any, AnyRef, AnyVal的区别

### Any

Any是abstract类，它是Scala类继承结构中最底层的。所有运行环境中的Scala类都是直接或间接继承自Any这个类，它就是其它语言（.Net，Java等）中的Object。

### AnyVal

AnyVal 所有值类型的基类， 它描述的是值，而不是代表一个对象。 
它包括 9 个 AnyVal 子类型：
Scala.Double 
scala.Float 
scala.Long 
scala.Int 
scala.Char 
scala.Short 
scala.Byte 
上面是数字类型。

scala.Unit 和 scala.Boolean 是非数字类型。

Scala 2.10 之前， AnyVal 是一个密封的 trait，不能被继承。 从 Scala 2.10开始，我们可以自定义一个从 AnyVal继承下来的类型。

对于这些基本类型的描述，和我们其它语言是相通的

### AnyRef

是所有引用类型的基类。除了值类型，所有类型都继承自AnyRef 。


## final和val
final是一个关键字，用于防止超类成员继承为派生类。也可以声明final变量，方法和类。
在scala中，经常会出现final val 这种用法，val 代表的是常量，不能被修改。那为什么还要加final呢？原因是final代表的是子类不能重载这个值。

### final变量示例

不能覆盖子类中的final变量，我们来看下面一个例子。
Scala单继承示例

```scala
class Vehicle{  
     final val speed:Int = 60  
}  
class Bike extends Vehicle{  
   override val speed:Int = 100  
    def show(){  
        println(speed)  
    }  
}  
 
object Demo{  
    def main(args:Array[String]){  
        var b = new Bike()  
        b.show()  
    }  
}

```

将上面代码保存到源文件：Demo.scala中，使用以下命令编译并执行代码 

```
D:\software\scala-2.12.3\bin>scalac Demo.scala
Demo.scala:5: error: overriding value speed in class Vehicle of type Int;
 value speed cannot override final member
   override val speed:Int = 100
                ^
one error found

```

### final方法

在父类中的final方法声明不能被覆盖。 如果不想让它被覆盖，则可以把方法定义成为final。尝试覆盖final方法将导致编译时错误。

Scala final方法示例

```scala
class Vehicle{  
     final def show(){  
         println("vehicle is running")  
     }  
}  
class Bike extends Vehicle{  
   //override val speed:Int = 100  
    override def show(){  
        println("bike is running")  
    }  
}  
object Demo{  
    def main(args:Array[String]){  
        var b = new Bike()  
        b.show()  
    }  
}

```

将上面代码保存到源文件：Demo.scala中，使用以下命令编译并执行代码

```
D:\software\scala-2.12.3\bin>scalac Demo.scala
Demo.scala:8: error: overriding method show in class Vehicle of type ()Unit;
 method show cannot override final member
    override def show(){
                 ^
one error found

```

### final类示例

也可以定义final类，final类不能继承。 如果定义了一个类为final类，那就不能进一步扩展了。

```scala
final class Vehicle{  
     def show(){  
         println("vehicle is running")  
     }  
 
}  
 
class Bike extends Vehicle{  
       override def show(){  
        println("bike is running")  
    }  
}  
 
object Demo{  
    def main(args:Array[String]){  
        var b = new Bike()  
        b.show()  
    }  
}

```

将上面代码保存到源文件：Demo.scala中，使用以下命令编译并执行代码

```
D:\software\scala-2.12.3\bin>scalac Demo.scala
Demo.scala:8: error: illegal inheritance from final class Vehicle
class Bike extends Vehicle{
                   ^
one error found

```