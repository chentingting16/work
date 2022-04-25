### 基本数据类型及包装类型

#### 包装类型常量池

Java 基本类型的包装类的大部分都实现了常量池技术。

`Byte`,`Short`,`Integer`,`Long` 这 4 种包装类默认创建了数值 **[-128，127]** 的相应类型的缓存数据，`Character` 创建了数值在 **[0,127]** 范围的缓存数据，`Boolean` 直接返回 `True` or `False`。

```java
public static Character valueOf(char c) {
    if (c <= 127) { // must cache
      return CharacterCache.cache[(int)c];
    }
    return new Character(c);
}

private static class CharacterCache {
    private CharacterCache(){}
    static final Character cache[] = new Character[127 + 1];
    static {
        for (int i = 0; i < cache.length; i++)
            cache[i] = new Character((char)i);
    }
}
```

```java
//超出范围的不适用常量池
Integer i1 = 33;
Integer i2 = 33;
System.out.println(i1 == i2);// 输出 true

//浮点数没有实现常量池
Float i11 = 333f;
Float i22 = 333f;
System.out.println(i11 == i22);// 输出 false

Double i3 = 1.2;
Double i4 = 1.2;
System.out.println(i3 == i4);// 输出 false

Integer i1 = 40; //相当于 Integer.valueOf(40)  i1 直接使用的是常量池中的对象。
Integer i2 = new Integer(40); //创建新的对象
System.out.println(i1==i2);   //false
```

#### 自动拆箱与自动装箱

拆箱：包装类型->基本数据类型

装箱：基本数据类型->包装类型

注：装箱其实就是调用了 包装类的`valueOf()`方法，拆箱其实就是调用了 `xxxValue()`方法。

- `Integer i = 10` 等价于 `Integer i = Integer.valueOf(10)`
- `int n = i` 等价于 `int n = i.intValue()`;

### 面向过程vs 面向对象

* 面向过程：将问题拆解为一个一个步骤(方法)，通过执行方法解决问题；
* 面向对象：抽象出对象，用对象执行方法的方式解决问题。->更容易**维护、复用、扩展**

**性能比较：** 面向过程和对象是一种编程思想，“直接比较语言性能本来就是不恰当的”；语言执行性能应该从他们最终执行方式上讨论。直接编译成机器码，然后执行的语言（比如C，编译器一次性编译为平台相关的机器码），从过程复杂度上，肯定比解释执行的语言（HTML，JavaScript，浏览器的解释器，把代码一行行解释为机器码）和 Java这种中间码（编译）+虚拟机（解释成机器码）的方式，要性能高的多。

### 成员变量vs局部变量

* **存储形式：**成员变量属于对象(堆)、局部变量属于方法(栈)。
* **生命周期：**从变量在内存中的生存时间上看，成员变量是对象的一部分，它随着对象的创建而存在，而局部变量随着方法的调用而自动生成，随着方法的调用结束而消亡。
* **默认值：**从变量是否有默认值来看，成员变量如果没有被赋初始值，则会自动以类型的默认值而赋值（一种情况例外:被 `final` 修饰的成员变量也必须显式地赋值），而局部变量则不会自动赋值。
* **语法：**成员变量可以被 `public`,`private`,`static` 等修饰符所修饰，而局部变量不能被访问控制修饰符及 `static` 所修饰；但是，成员变量和局部变量都能被 `final` 所修饰。

### 引用拷贝 vs 浅拷贝 vs 深拷贝

![img](https://javaguide.cn/assets/shallow&deep-copy.08611c43.png)

- **浅拷贝**：浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点），不过，如果原对象内部的属性是引用类型的话，浅拷贝会直接复制内部对象的引用地址，也就是说拷贝对象和原对象共用同一个内部对象。
- **深拷贝** ：深拷贝会完全复制整个对象，包括这个对象所包含的内部对象。

### Object常用方法

```java
public final native Class<?> getClass()//native方法，用于返回当前运行时对象的Class对象，使用了final关键字修饰，故不允许子类重写。

public native int hashCode() //native方法，用于返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap。
public boolean equals(Object obj)//用于比较2个对象的内存地址是否相等，String类对该方法进行了重写用户比较字符串的值是否相等。

protected native Object clone() throws CloneNotSupportedException//naitive方法，用于创建并返回当前对象的一份拷贝。一般情况下，对于任何对象 x，表达式 x.clone() != x 为true，x.clone().getClass() == x.getClass() 为true。Object本身没有实现Cloneable接口，所以不重写clone方法并且进行调用的话会发生CloneNotSupportedException异常。

public String toString()//返回类的名字@实例的哈希码的16进制的字符串。建议Object所有的子类都重写这个方法。

public final native void notify()//native方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。

public final native void notifyAll()//native方法，并且不能重写。跟notify一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。

public final native void wait(long timeout) throws InterruptedException//native方法，并且不能重写。暂停线程的执行。注意：sleep方法没有释放锁，而wait方法释放了锁 。timeout是等待时间。

public final void wait(long timeout, int nanos) throws InterruptedException//多了nanos参数，这个参数表示额外时间（以毫微秒为单位，范围是 0-999999）。 所以超时的时间还需要加上nanos毫秒。

public final void wait() throws InterruptedException//跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念

protected void finalize() throws Throwable { }//实例被垃圾回收器回收的时候触发的操作
```

### String vs StringBuilder vs StringBuffer

#### String 不可变类

* 不可变类：该类的内容不能改变

  1. 成员变量都为private 
  2. 没有set或改变成员变量的方法
  3. 类为final或所有方法为final
  4. 初始化和get使用clone

  ```java
  public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
      private final char value[];
  	//...
  }
  ```

  注：在 Java 9 之后，`String` 、`StringBuilder` 与 `StringBuffer` 的实现改用 byte 数组存储字符串。

#### StringBuilder与 StringBuffer

都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串，不过没有使用 `final` 和 `private` 关键字修饰，最关键的是这个 `AbstractStringBuilder` 类还提供了很多修改字符串的方法比如 `append` 方法。

**线程安全性**： `String` 中的对象是不可变的，也就可以理解为常量，线程安全。`AbstractStringBuilder` 是 `StringBuilder` 与 `StringBuffer` 的公共父类，定义了一些字符串的基本操作，如 `expandCapacity`、`append`、`insert`、`indexOf` 等公共方法。`StringBuffer` 对方法加了同步锁或者对调用的方法加了**同步锁**，所以是线程安全的。`StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的。

**性能**：每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象。`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

**对于三者使用的总结：**

1. 操作少量的数据: 适用 `String`
2. 单线程操作字符串缓冲区下操作大量数据: 适用 `StringBuilder`
3. 多线程操作字符串缓冲区下操作大量数据: 适用 `StringBuffer`

#### 字符串拼接

对象引用和“+”的字符串拼接方式，实际上是通过 `StringBuilder` 调用 `append()` 方法实现的，拼接完成之后调用 `toString()` 得到一个 `String` 对象 。

不过，在循环内使用“+”进行字符串的拼接的话，存在比较明显的缺陷：**编译器不会创建单个 `StringBuilder` 以复用，会导致创建过多的 `StringBuilder` 对象**。

`StringBuilder` 对象是在循环内部被创建的，这意味着每循环一次就会创建一个 `StringBuilder` 对象。

### 泛型

* Java5引入的新特性；
* 提供编译时类型检查机制；**安全、易读**
* 本质是参数化类型，即所操作的数据类型被指定为一个参数；
* 伪泛型，运行期间泛型信息被擦除。

#### 泛型类型：

1. 泛型类：相当于普通类的工厂

```java
class Pair<T>
{
    T first;
    T second;

    Pair(T f, T s)
    {
        first = f;
        second = s;
    }
}
```

2. 泛型方法

```java
//普通类中定义泛型方法
//类型变量放在修饰符和返回值之间
class A 
{
	public static <T> T getMiddle(T... a)
    {
        return a[a.length/2];
    }
}

//调用
A.<String>getMiddle("john","q","public");
```

3. 泛型接口

 #### 类型变量的限定

* X super A：A及其父类->限定下界为A
* X extends A：A及其子类->限定上界为A

#### 类型擦除

* T无限定变量：Object
* super: Object
* extends A: A

#### 通配符 ？

### 反射



### 注解



### 异常

![image-20220324151854466](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220324151854466.png)

* `Error`：程序无法处理的错误，有虚拟机错误如`OutOfMemoryError`、`StackOverflowError`等
* `Exception`：程序可以处理的异常，可以通过 `catch` 来进行捕获。`Exception` 又可以分为 Checked Exception (受检查异常，必须处理) 和 Unchecked Exception (不受检查异常，可以不处理)。
* **Checked Exception** 即 **必检异常** ，Java 代码在编译过程中，如果异常没有被 `catch`/`throw` 处理的话，就没办法通过编译 。常见的受检查异常有： IO 相关的异常、`ClassNotFoundException` 、`SQLException`...。
* **Unchecked Exception** 即 **免检异常** ，Java 代码在编译过程中 ，即使不处理异常也可以正常通过编译。`RuntimeException` 及其子类都统称为非受检查异常，例如：`NullPointerException`、`NumberFormatException`（字符串转换为数字）、`ArrayIndexOutOfBoundsException`（数组越界）、`ClassCastException`（类型转换错误）、`ArithmeticException`（算术错误）等。

#### Throwable 类常用方法

- `String getMessage()`: 返回异常发生时的简要描述
- `String toString()`: 返回异常发生时的详细信息
- `void printStackTrace()`: 在控制台上打印 `Throwable` 对象封装的异常信息

#### try-catch-finally

- **`try`块：** 用于捕获异常。其后可接零个或多个 `catch` 块，如果没有 `catch` 块，则必须跟一个 `finally` 块。
- **`catch`块：** 用于处理 try 捕获到的异常。
- **`finally` 块：** 无论是否捕获或处理异常，`finally` 块里的语句都会被执行。当在 `try` 块或 `catch` 块中遇到 `return` 语句时，`finally` 语句块将在方法返回之前被执行。
- **不要在 finally 语句块中使用 return!** 当 try 语句和 finally 语句中都有 return 语句时，try 语句块中的 return 语句会被忽略。这是因为 try 语句中的 return 返回值会先被暂存在一个本地变量中，当执行到 finally 语句中的 return 之后，这个本地变量的值就变为了 finally 语句中的 return 返回值。

#### finally一定会被执行么？

不一定的！在某些情况下，finally 中的代码不会被执行。

就比如说 `finally` 之前虚拟机被终止运行的话，finally 中的代码就不会被执行。

```java
try {
    System.out.println("Try to do something");
    throw new RuntimeException("RuntimeException");
} catch (Exception e) {
    System.out.println("Catch Exception -> " + e.getMessage());
    // 终止当前正在运行的Java虚拟机
    System.exit(1);
} finally {
    System.out.println("Finally");
}
```

另外，在以下 2 种特殊情况下，`finally` 块的代码也不会被执行：

1. 程序所在的线程死亡。
2. 关闭 CPU。

#### try-with-resourse

* 适用范围：实现了 `java.lang.AutoCloseable`或者 `java.io.Closeable` 的对象

### Java参数传递：值传递

Java 中将实参传递给方法（或函数）的方式是 **值传递** ：

- 如果参数是基本类型的话，很简单，传递的就是基本类型的字面量值的拷贝，会创建副本。
- 如果参数是引用类型，传递的就是实参所引用的对象在堆中地址值的拷贝，同样也会创建副本。

![引用数据类型参数2](https://javaguide.cn/assets/java-value-passing-03.51825fbb.png)

### I/O