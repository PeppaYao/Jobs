 ### ArrayList和LinkedList的区别★★★

-   Arraylist内存地址是连续的，底层是数组。随机访问的速度比较快，是常数时间，删除是线性的复杂度。

-   LinkedList内存地址是不连续的，是单链表。随机访问比较差，是线性的，插入删除比较快是常数时间。

    

### HashMap原理★★★

HashMap数组+链表或红黑树存储数据，**线程不安全**

**数组容量默认时16，负载因子默认是0.75**

Java 8以后，链表中的元素超过8后，hashmap会将链表结构转换为红黑树的结构提高查询效率。

线程安全版：HashTable和ConcurrentHashMap

HashTable并发性不如ConcurrentHashMap，基本弃用了。

ConcurrentHashMap分段锁实现并发操作，线程安全。

ConcurrentHashMap由多个segment组成，segment的数量也是锁的并发度，每个segment均继承自Reentrantlock并单独加锁，所以每次进行加锁操作时锁住的都是一个segment，这样只要保证每个segment都是线程安全的，也就实现了全局的线程安全。

put，get和resize的过程？



### Java的基本类型★★

java有8种基本类型

- Integer 4字节

- Long 8字节

- Double 8字节

- Boolean 1字节

- Short 2字节

- Float 4字节

- Char 2字节

- Byte 1字节  **这个类型容易被忘**

    

### 什么是反射？★★

反射机制：在运行时动态地创建类的对象，获取一个类的所有成员变量和方法。

反射的作用：

- 得到一个对象所属的类
- 获取一个类的所有成员变量和方法
- 在运行时动态地创建类的对象
- 在运行时动态地调用对象的方法

获取class对象的3种方式：

- 某个对象的getclass
- 类的class属性
- Class.forName("fullpathname")



### 抽象类和接口★★

相同点：

- 都不能被实例化

不同点：

- 接口只有定义，不能有方法的实现。抽象类可以有定义与实现。
- 一个类可以实现多个接口，但是只能继承一个抽象类。
- 接口中方法必须被public abstract修饰，可全省。抽象类可以被private protected private修饰，抽象方法被abstrac修饰，不能被privat、static、synchronized和native等修饰，**必须以分号结尾**，**不带花括号**。 
- 接口中常量必须被public static final修饰，**必须赋初值**，不能被修改 。
- 抽象类比接口速度快



### 多态★★

父类引用指向子类对象

成员变量：编译看等号左边，运行也看等号左边

成员方法：编译看左边，运行看右边。

父子都有的同名成员，多态时，调用的时父类的成员变量



### String、StringBuilder和StringBuffer★★

**可变性**
简单的来说：`String`类中使用`final`关键字字符数组保存字符串，`private final char value[]`，所以`String`对象是不可变的。而`StringBuilder`与`StringBuffer`都继承自`AbstractStringBuilder`类，在`AbstractStringBuilder`中也是使用字符数组保存字串`char[]value`但是没有用`final`关键字修饰，所以这两种对象都是可变的。
**线程安全性**
`String`中的对象是不可变的，也就可以理解为常量，线程安全。
`AbstractStringBuilder`是`StringBuilder`与`StringBuffer`的公共父类，定义了一些字符串的基本操作，如`expandCapacity、append、insert、indexOf`等公共方法。`StringBuffer`对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的`StringBuilder`并没有对方法进行加同步锁，所以是非线程安全的。
**性能**
每次对`String`类型进行改变的时候，都会生成一个新的`String`对象，然后将指针指向新的`String`对象。`StringBuffer`每次都会对`StringBuffer`对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用`StringBuilder`相比使用`StringBuffer`仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。
**使用总结**

- 操作少量的数据 = String
- 单线程操作字符串缓冲区下操作大量数据 = StringBuilder
- 多线程操作字符串缓冲区下操作大量数据 = StringBuffer

### 


### finnal关键字总结★★

final 关键字主要用在三个地方：变量、方法、类。

- 对于一个 final 变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。
- 当用 final 修饰一个类时，表明这个类不能被继承。final 类中的所有成员方法都会被隐式地指定为 final 方法
- 使用 final 方法的原因有两个。第一个原因是把方法锁定，以防任何继承类修改它的含义；第二个原因是效率。在早期的 Java 实现版本中，会将final 方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升（现在的 Java 版本已经不需要使用 final 方法进行这些优化了）。类中所有的 private 方法都隐式地指定为 final

### equals和==★

==判断地址

equals判断内容