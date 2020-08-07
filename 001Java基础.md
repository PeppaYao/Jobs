## Java面试基础问题

1、实习做的项目介绍
 2、对于springboot的了解
 3、springmvc原理
 4、java内存结构
 5、java锁，sychronized
 6、violate底层实现
 7、ArrayList和LinkedList的区别

1. 说说JVM的理解
2. 新生代的对象什么时候会进入老年代
3. 让你设计，你会如何设计一个Reentrantlock。
4. 简单问下JDK1.7 和 JDK 1.8 ConcurrentHashMap 的底层数据结构相关的吧
5. Thread中 wait（）和sleep（）哪个是会有释放锁的操作？ 
6. 看过AQS源码么，说说你的理解吧。

## 面试相关

1. solar和Es的底层都是lunch，如何理解luence工作原理

2. zookeeper的watcher机制
3. kafka的rebalance机制
4. rocketmq的原理
5. ES的聚合和mangoDB的聚合区别
6. redis的主从同步哨兵缓存穿透和击穿
7. mysql主从不一致
8. 分布式锁
9. 服务注册和发现
10. 鉴权如何实现



reactor实现了响应式编程的思想reactive programing，符合reactive streams规范。

反应式编程框架主要采用了观察者模式。

mono和flux是发布者也可以理解为被观察者 publisher   ----> 主动推送数据给subscriber 订阅者也可以叫观察者

如果publisher发布消息太快，超过了subscriber的处理速度，这时候就出现了背压backpressure。 

背压---指在异步场景中，被观察者发送事件速度远远超过观察者的处理速度的情况下，一种告诉上游的被观察者降低发送速度的策略。

Mono 实现了 org.reactivestreams.Publisher 接口，代表0到1个元素的发布者（Publisher）。

使用Mono以后，

Reactor 框架主要有两个主要的模块：reactor-core 和 reactor-ipc。前者主要负责 Reactive Programming 相关的核心 API 的实现，后者负责高性能网络通信的实现，目前是基于 Netty 实现的。





reactor用于创建高效的响应式系统。

Java的函数式API  completableFuture stream duration

具有背压的控制能力



出A/B test 方案  提升点击率 团体自发需求 共建TAM 数据上报

303和307是对302的细化



301 move permanently

302 found POST方法的重定向在未询问用户的情况下就变成GET

303 see other  POST重定向为GET

307 temporary redirect  当客户端的POST请求收到服务端307状态码响应时，需要跟用户询问是否应该在新URI上发起POST方法，也就是说，307是不会把POST转为GET的。



## 面试题

1、实习做的项目介绍
 2、对于springboot的了解
 3、springmvc原理
 4、java内存结构
 5、java锁，sychronized
 6、violate底层实现
 7、ArrayList和LinkedList的区别

1. 说说JVM的理解
2. 新生代的对象什么时候会进入老年代
3. 让你设计，你会如何设计一个Reentrantlock。
4. 简单问下JDK1.7 和 JDK 1.8 ConcurrentHashMap 的底层数据结构相关的吧
5. Thread中 wait（）和sleep（）哪个是会有释放锁的操作？ 
6. 看过AQS源码么，说说你的理解吧。



## 回顾

1. lambda表达式只能用于函数式接口：小括号 箭头 花括号。supplier consumer andThen

2. stream流，函数式编程，代码简洁。map filter limit count。

3. Java五大内存区：

   ```java
   方法区 
   heap 
   程序计数器 
   虚拟机栈 
   本地方法栈
   ```

   线程共享：

   ```java
   方法区 
   heap
   ```

   线程私有：

   ```
   程序计数器 
   虚拟机栈 
   本地方法栈
   ```

4. 反射：在运行时，动态的获取某个类的属性和方法。

5. 获取class对象的三种方式：

   ```
   类名.class   
   对象.getClass()         
   全类名获取 Class.forName(全类名)
   ```

6. junit返回为空，参数为空。

7. Mono异步流。