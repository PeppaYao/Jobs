## Java基础

### 1、集合

![1597567995402](C:\Users\Yao\AppData\Local\Temp\1597567995402.png)

### 2、HashMap

HashMap数组+链表或红黑树存储数据，**线程不安全**

**数组容量默认时16，负载因子默认是0.75**

Java 8以后，链表中的元素超过8后，hashmap会将链表结构转换为红黑树的结构提高查询效率。

并发修改异常

### 3、ConcurrentHashMap

分段锁实现并发操作，线程安全。

ConcurrentHashMap由多个segment组成，segment的数量也是锁的并发度，每个segment均继承自Reentrantlock并单独加锁，所以每次进行加锁操作时锁住的都是一个segment，这样只要保证每个segment都是线程安全的，也就实现了全局的线程安全。

### 4、HashTable

线程安全，并发性不如ConcurrentHashMap

### 5、反射机制

在程序运行时，动态地获取一个类的所有属性和方法。

```java
Person person = new Student();
```

person对象的编译时类型为Person，运行时类型为Student，因此无法在编译时获取在Student类中定义的方法。

获取class对象的3种方式：

- 某个对象的getclass
- 类的class属性
- Class.forName("fullpathname")

```java
Class class1 = person.getClass();
Class class2 = Person.class;
Class class3 = Class.forName("com.bytedance.Person");
```

创建对象实例两种方式

- Class对象的newInstance方法
- Class对象获取指定Constructor对象，再使用该对象调用newInstance方法

```java
Person instance1 = (Person) class1.newInstance();
Constructor constructor = class1.getConstructor(String.class, String.class);
Person instance2 = (Person)constructor.newInstance("姚军", "25");
```

Method的invoke方法，通过反射动态生成类的对象并调用其方法。

```java
Method method = class2.getMethod("setName", String.class); //获取class对象中的setname方法
//获取constructor对象
Constructor constructor1 = class2.getConstructor();
//获取class实例
Object object = constructor1.newInstance("姚军","24");
//使用method的invoke调用方法
method.invoke(object,"alex");
```

### 6、注解

没看懂

### 7、内部类

不了解

### 8、序列化

不了解