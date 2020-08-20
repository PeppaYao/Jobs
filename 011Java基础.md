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



### 9、Java八大基本数据类型

- int 4字节
- long 8字节
- float 4字节
- double 8字节
- char **2字节**
- byte 1字节
- boolean 1字节
- short   2字节

### 10、==和equals

==判断地址

equals判断内容



### 11、深浅拷贝

clone



### 12、红黑树

红黑树特性:

- 所有红色节点的儿子节点都是黑色
- 每条路径上的黑色节点数相同
- 根是黑色



红黑树相对于AVL树快在哪儿？



### 13、KMP



### 14、快排

```c++
#include <iostream>
#include <algorithm>
using namespace std;
int arr[] ={1,23,2,3,23,4,5};
void quick_sort(int l,  int r){
    if(l >= r) return;
    int pivot = arr[l+r>>1];
    int i = l-1, j = r+1;
    while(i < j){
        while(arr[++i] < pivot);
        while(arr[--j] > pivot);
        if(i < j){
            swap(arr[i], arr[j]);
        }
    }
    quick_sort(l, j);
    quick_sort(j+1, r);
}
int main(){
    int n = sizeof(arr)/sizeof(int);
    quick_sort(0, n-1);
    for(int i =0; i < n; i++){
        cout<<arr[i]<<" ";
    }
    return 0;
}
```

### 15、堆排

```c++
#include <iostream>
#include <algorithm>
using namespace std;
int arr[] ={1,23,2,3,23,4,5};
int n = sizeof(arr)/sizeof(int);
void heapify(int cur, int n){
    if(cur >= n) return;
    int left = 2*cur +1, right = 2*cur+2;
    int m_min = cur;
    while(left < n && arr[m_min] < arr[left]) m_min = left;
    while(right < n && arr[m_min] < arr[right]) m_min = right;
    if(m_min != cur){
        swap(arr[m_min], arr[cur]);
        heapify(m_min, n);
    }
}
void heap_sort(){
    //先变成堆
    for(int i = n-1; i >= 0; i--){
        heapify(i,n);
    }
    //然后交换
    for(int i = n-1; i >= 0; i--){
        swap(arr[0], arr[i]);
        heapify(0, i);
    }
}
int main(){
    heap_sort();
    for(int i =0; i < n; i++){
        cout<<arr[i]<<" ";
    }
    return 0;
}
```

### 16、归并排

```c++
#include <iostream>
#include <cstring>
using namespace std;
int arr[] ={1,23,2,3,23,4,5};
void merge_sort(int l, int r){
    if(l >= r) return;
    //先递归再处理
    int mid = l+r>>1;
    merge_sort(l, mid);
    merge_sort(mid+1, r);
    int temp[r-l+1];
    memset(temp, 0, sizeof temp);
    int k = 0;
    int i = l, j = mid+1;
    while(i <= mid && j <= r){
        if(arr[i] < arr[j]) temp[k++] = arr[i++];
        else temp[k++] = arr[j++];
    }
    while(i <= mid) temp[k++] = arr[i++];
    while(j <= r) temp[k++] = arr[j++];
    for(int i = 0; i < k; i++){
        arr[i+l] = temp[i];
    }
}

int main(){
    int n = sizeof(arr)/sizeof(int);
    merge_sort(0, n-1);
    for(int i =0; i < n; i++){
        cout<<arr[i]<<" ";
    }
    return 0;
}
```

### 17、this

通过谁调用的方法，谁就是this

this关键字可以解决同名冲突，如果不加this，则优先使用局部变量。

### 18、字符串常量池

对于基本类型，==进行的是数值的比较

对于引用类型，==进行的是地址值的比较

双引号直接写的字符串在常量池，new的对象不在池中

字符串的底层是byte数组，常量池中存的只是byte数组的地址

### 19、抽象类和接口

相同点：

- 都不能被实例化

不同点：

- 接口只有定义，不能有方法的实现。抽象类可以有定义与实现。
- 一个类可以实现多个接口，但是只能继承一个抽象类。
- 接口中方法必须被public abstract修饰，可全省。抽象类可以被private protected private修饰，抽象方法被abstrac修饰，不能被privat、static、synchronized和native等修饰，**必须以分号结尾**，**不带花括号**。 
- 接口中常量必须被public static final修饰，**必须赋初值**，不能被修改 。
- 抽象类比接口速度快

### 20、抽象类

- 抽象方法所在的类必须是抽象类，抽象类不一定有抽象方法。
- 抽象类可以有构造方法

### 21、多态

父类引用指向子类对象

成员变量：编译看等号左边，运行也看等号左边

成员方法：编译看左边，运行看右边。

父子都有的同名成员，多态时，调用的时父类的成员变量

```java
MyInterface myInterface = new MyImpl();
System.out.println(myInterface.number); //输出父类的数值
```

### 22、final关键字



<<<<<<< HEAD
不了解
对于不想进行序列化的变量，使用`transient`关键字修饰

### 9、String&StringBuilder&StringBuffer
可变性
简单的来说：`String`类中使用`final`关键字字符数组保存字符串，`private final char value[]`，所以`String`对象是不可变的。而`StringBuilder`与`StringBuffer`都继承自`AbstractStringBuilder`类，在`AbstractStringBuilder`中也是使用字符数组保存字串`char[]value`但是没有用`final`关键字修饰，所以这两种对象都是可变的。
线程安全性
`String`中的对象是不可变的，也就可以理解为常量，线程安全。
`AbstractStringBuilder`是`StringBuilder`与`StringBuffer`的公共父类，定义了一些字符串的基本操作，如`expandCapacity、append、insert、indexOf`等公共方法。`StringBuffer`对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的`StringBuilder`并没有对方法进行加同步锁，所以是非线程安全的。
性能
每次对`String`类型进行改变的时候，都会生成一个新的`String`对象，然后将指针指向新的`String`对象。`StringBuffer`每次都会对`StringBuffer`对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用`StringBuilder`相比使用`StringBuffer`仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。
使用总结
- 操作少量的数据 = String
- 单线程操作字符串缓冲区下操作大量数据 = StringBuilder
- 多线程操作字符串缓冲区下操作大量数据 = StringBuffer

### 10、 Java闭包的理解


### 11、 finnal关键字总结
final 关键字主要用在三个地方：变量、方法、类。
- 对于一个 final 变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。
- 当用 final 修饰一个类时，表明这个类不能被继承。final 类中的所有成员方法都会被隐式地指定为 final 方法
- 使用 final 方法的原因有两个。第一个原因是把方法锁定，以防任何继承类修改它的含义；第二个原因是效率。在早期的 Java 实现版本中，会将final 方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升（现在的 Java 版本已经不需要使用 final 方法进行这些优化了）。类中所有的 private 方法都隐式地指定为 final

=======
>>>>>>> 6e197a41debf68ebee61aa11f16a4fabb522bbbf
