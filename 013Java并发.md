### 1、Synchronized

[视频链接](https://www.bilibili.com/video/BV1B7411L7tE?p=9)

队列，锁

卖票系统：ABC三个线程在抢票，不能出现超卖或者其他数据不一致问题。

```java
public class SaleTicket {
    public static void main(String[] args) {
        //把资源类放入到线程的接口中
        Ticket ticket = new Ticket();
        new Thread(()->{
            for(int i = 0; i < 200; i++) ticket.sale();
        }, "线程A").start();
        new Thread(()->{
            for(int i = 0; i < 200; i++) ticket.sale();
        }, "线程B").start();
        new Thread(()->{
            for(int i = 0; i < 200; i++) ticket.sale();
        }, "线程C").start();
    }

}

public class Ticket {
    private int ticket = 50;
    public synchronized void sale(){
        if( ticket > 0){
            System.out.println(Thread.currentThread().getName()+"卖出了"+(ticket--)+"张票，剩余票数为"+ticket);
        }
    }
}
```

### 2、Reentrantlock

卖票系统：ABC三个线程抢票。

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Reentrant {
    public static void main(String[] args) {
        Ticket2 ticket = new Ticket2();
        new Thread(()->{ for(int i = 0; i < 200; i++) ticket.sale(); }, "线程A").start();
        new Thread(()->{ for(int i = 0; i < 200; i++) ticket.sale(); }, "线程B").start();
        new Thread(()->{ for(int i = 0; i < 200; i++) ticket.sale(); }, "线程C").start();
    }
}

class Ticket2 {
    private int ticket = 50;
    Lock lock = new ReentrantLock();
    public void sale(){
        lock.lock();
        try {
            if( ticket > 0){
                System.out.println(Thread.currentThread().getName()+"卖出了"+(ticket--)+"张票，剩余票数为"+ticket);
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
```

### 3、Synchronized和lock的区别

1. synchronized时Java内置的关键字，lock是一个接口
2. synchronized会自动释放锁，lock需要手动释放。
3. synchronized线程获取方式是阻塞等待，而lock可以尝试获取锁。
4. synchronized是非公平锁，Lock可以设置公平与非公平
5. synchronized适合锁少量的同步代码，lock适合锁大量的同步代码。

**Lock的主要特性**

- 尝试非阻塞地获取锁
- 能被中断地获取锁
- 超时获取锁

### 4、synchronized版的生产者消费者模式

关键字：

- synchronized 
- wait
- notify

生产者消费者模式：一个线程增加1，一个线程减小1

```java
public class TwoThread {
    public static void main(String[] args) {
        Data data = new Data();
        new Thread(()->{
            try {
                for(int i = 0; i < 100; i++)data.increment();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"线程A").start();
        new Thread(()->{
            try {
                for(int i = 0; i < 100; i++)data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"线程B").start();
        new Thread(()->{
            try {
                for(int i = 0; i < 100; i++)data.increment();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"线程C").start();
        new Thread(()->{
            try {
                for(int i = 0; i < 100; i++)data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"线程D").start();
    }
}
class Data{
    private int number = 0;
    //+1
    synchronized void increment() throws InterruptedException {
        while(number!=0){
            this.wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName()+" "+number);
        this.notifyAll();
    }
    //-1
    synchronized void decrement() throws InterruptedException {
        while(number==0){
            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName()+" "+number);
        this.notifyAll();
    }
}
```

虚假唤醒就是一些obj.wait()会在除了obj.notify()和obj.notifyAll()的其他情况被唤醒，而此时是不应该唤醒的。 

解决方式：将if替换为while

### 5、Lock版的生产者消费者模式

关键字

- lock
- await
- signal

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class LockThread {
    public static void main(String[] args) {
        Data2 data = new Data2();
        new Thread(()->{
            try {
                for(int i = 0; i < 100; i++)data.increment();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"线程A").start();
        new Thread(()->{
            try {
                for(int i = 0; i < 100; i++)data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"线程B").start();
        new Thread(()->{
            try {
                for(int i = 0; i < 100; i++)data.increment();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"线程C").start();
        new Thread(()->{
            try {
                for(int i = 0; i < 100; i++)data.decrement();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"线程D").start();
    }
}

class Data2 {
    private int number = 0;
    Lock lock = new ReentrantLock();
    Condition condition = lock.newCondition();

    //+1
    void increment() throws InterruptedException {
        lock.lock();
        try {
            while (number != 0) {
                condition.await();
            }
            number++;
            System.out.println(Thread.currentThread().getName() + " " + number);
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    //-1
    void decrement() throws InterruptedException {
        lock.lock();
        try {
            while (number == 0) {
                condition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName() + " " + number);
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

### 6、Condition

condition的优势：精准的通知和唤醒线程。

三个线程顺序执行

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ConditionThread {
    public static void main(String[] args) {
        Data3 data = new Data3();
        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) data.printA();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "线程A").start();
        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) data.printB();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "线程B").start();
        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) data.printC();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "线程C").start();
    }
}

class Data3 {
    private int number = 1;
    Lock lock = new ReentrantLock();
    Condition condition1 = lock.newCondition();
    Condition condition2 = lock.newCondition();
    Condition condition3 = lock.newCondition();

    //+1
    void printA() throws InterruptedException {
        lock.lock();
        try {
            while (number != 1) {
                condition1.await();
            }
            number = 2;
            System.out.println(Thread.currentThread().getName() + " AAA " + number);
            condition2.signal(); //精准通知线程2
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    //-1
    void printB() throws InterruptedException {
        lock.lock();
        try {
            while (number != 2) {
                condition2.await();
            }
            number = 3;
            System.out.println(Thread.currentThread().getName() + " BBB " + number);
            condition3.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    void printC() throws InterruptedException {
        lock.lock();
        try {
            while (number != 3) {
                condition3.await();
            }
            number = 1;
            System.out.println(Thread.currentThread().getName() + " CCC " + number);
            condition1.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

```

### 7、List线程不安全

并发修改异常错误

```java
public class SafeList {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        for (int i = 1; i <= 100; i++) {
            new Thread(()->{list.add(UUID.randomUUID().toString().substring(0,5));System.out.println(list);}, String.valueOf(i)).start();

        }
    }
}
```

如何改为线程安全的呢？

- vector
- synchronizedList
- CopyOnWriteArrayList 写入时复制

```
List<String> list = new Vector<>();
List<String> list = Collections.synchronizedList(new ArrayList<>());
List<String> list = new CopyOnWriteArrayList<>();
```

### 8、HashSet线程不安全

并发修改异常

```java
public class SafeList {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();
        for (int i = 1; i <= 100; i++) {
            new Thread(()->{set.add(UUID.randomUUID().toString().substring(0,5));System.out.println(set);}, String.valueOf(i)).start();

        }
    }
}
```

线程安全版

- Collections.synchronizedSet
- CopyOnWriteArraySet

```java
Set<String> set = Collections.synchronizedSet(new HashSet<>());
Set<String> set = new CopyOnWriteArraySet();
```

hashset的本质是Hashmap，key不重复，value放入了一个固定的常量

```java
public HashSet() {
   map = new HashMap<>();
}
```

### 9、HashMap线程不安全

解决方案

- ConcurrentHashMap

### 10、Callable

创建线程的第三种方式

- 有返回值，会阻塞
- 可抛出异常
- 有缓存

```java
public class CallableImpl {
    public static void main(String[] args) throws Exception {
        // new Thread(new Runnable).start();
        // new Thread(new FutureTask<V>(Callable)).start();
        //其中FutureTask是Runnable的实现类，而FutureTask的构造方法可以调用Callable
        myCall myCall = new myCall();
        FutureTask futureTask = new FutureTask(myCall);
        new Thread(futureTask,"线程A").start();
        new Thread(futureTask,"线程B").start();
        String o = (String)futureTask.get(); //会阻塞
        System.out.println(o);
    }
}
class myCall implements Callable{
    @Override
    public String call() throws Exception {
        System.out.println("my call is running...");
        return "hello world";
    }
}
```

### 11、CountDownLatch

计数，等待完成

```java
public class UseCountDown {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                countDownLatch.countDown();
                System.out.println(Thread.currentThread().getName().toString());
            }, String.valueOf(i)).start();
        }
        countDownLatch.await();
        System.out.println("close door");
    }
}
```

### 12、CyclicBarrier

加法计数器

```java
public class Barrier {
    public static void main(String[] args) throws Exception{
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> System.out.println("召唤神龙成功!"));
        for (int i = 1; i <= 7; i++) {
            new Thread(()->{
                try {
                    System.out.println(Thread.currentThread().getName().toString());
                    cyclicBarrier.await(); //可以理解为增加1
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
        System.out.println("over..."); //不会阻塞后面的执行
    }
}
```

### 13、Semaphore

信号量 限流

```java

public class SemaphoreT {
    //用在限流 抢车位
    public static void main(String[] args) throws InterruptedException {
        Semaphore semaphore = new Semaphore(3); //3个可用的位置
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                try {
                    semaphore.acquire(); //获取
                    System.out.println(Thread.currentThread().getName().toString()+"获得");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName().toString()+"释放");
                }catch (Exception e){
                    e.printStackTrace();
                }finally {
                    semaphore.release();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```

### 14、Volatile

- 保证可见性
- 不保证原子性
- 禁止指令重排

volatile是如何保证的可见性？

- 线程会拷贝内存中的值到工作内存中。
- 读取：read / load/ use/ assign
- 写入：store/write/lock/unlock

线程B修改了值，但是线程A不能立即可见。需要让线程A知道内存中的值发生了变化

```java
public class VolatileT {
    public volatile static int number = 0; //验证 volatile保证内存的可见性
    public static void main(String[] args) throws Exception {
        new Thread(()->{
            while(number == 0){

            }
        }).start();
        TimeUnit.SECONDS.sleep(1);
        number = 1;
        System.out.println(number);

    }
}
```

### 16、ThreadLocal原理和应用场景

作用：提供线程内的局部变量，不同的线程之间不会相互干扰，这种变量在线程的生命周期内起作用，减少同一个线程内多个函数或组件之间一些公共变量传递的复杂性。

总结：

- 线程并发：在多线程并发的场景下
- 传递数据：我们可以通过ThreadLocal在同一线程，不同组件中传递公共变量
- 线程隔离：**每个线程的变量都是独立的，不会相互影响**



```java
/*线程隔离：线程A只能获取到线程A设置的变量*/
public class LocalT {
    private String content;
    ThreadLocal<String> tl = new ThreadLocal<>();
    public String getContent() {
       return tl.get();
    }

    public void setContent(String content) {
        tl.set(content);
    }

    public static void main(String[] args) {
        LocalT localT = new LocalT();
        for (int i = 0; i < 5; i++) {
            new Thread(()->{
                localT.setContent(Thread.currentThread().getName()+"的数据");
                try {
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName()+"获取到"+localT.getContent());
            },String.valueOf(i+1)).start();
        }
    }
}
```

### 17、BlockingQueue

阻塞队列

- 写入：满了，阻塞
- 取出：空了，阻塞

使用场景：多线程并发处理，线程池

**四组API**

1. 抛出异常
2. 不抛出异常
3. 阻塞等待
4. 超时等待

| 方式·        | 抛出异常    | 有返回值，不抛出异常 | 阻塞等待· | 等待超时退出      |
| ------------ | ----------- | -------------------- | --------- | ----------------- |
| 添加         | boolean add | boolean offer(数值)  | void put  | offer(数值，时间) |
| 移除         | T remove    | T poll(数值)         | void take | poll(数值，时间)  |
| 检测队首元素 | T element   | T peek               | -         | -                 |

第一组API：抛出异常

```java
public static void test01(){
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(3);
        System.out.println(queue.add(1));
        System.out.println(queue.add(2));
        System.out.println(queue.add(3));
//        如果满了 抛出异常 java.lang.IllegalStateException: Queue full
//        System.out.println(queue.add(4));
        
        System.out.println(queue.element()); //获取队首元素
        
        System.out.println(queue.remove());
        System.out.println(queue.remove());
        System.out.println(queue.remove());
//        如果空了 抛出异常 java.util.NoSuchElementException
//        System.out.println(queue.remove());

    }
```

第二组API：有返回值，不抛出异常

```java
public static void test02(){
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(3);
        System.out.println(queue.offer(1));
        System.out.println(queue.offer(2));
        System.out.println(queue.offer(3));
//        队列满了 不抛出异常 返回false
//        System.out.println(queue.offer(4));

        System.out.println(queue.peek()); //获取队首元素

        System.out.println(queue.poll());
        System.out.println(queue.poll());
        System.out.println(queue.poll());
//        队列为空 不抛出异常 返回null
//        System.out.println(queue.poll());
    }
```

第三组API：阻塞等待

```java
public static void test03() throws Exception {
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(3);
        queue.put(1);
        queue.put(2);
        queue.put(3);
//        队列为满了 一直阻塞等待
//        queue.put(4);
        queue.take();
        queue.take();
        queue.take();
//        队列为空 一直阻塞等待
//        queue.take();
    }
```

第四组API：等待超时退出

```java
public static void test04() throws Exception {
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(3);
        System.out.println(queue.offer(1, 2, TimeUnit.SECONDS));
        System.out.println(queue.offer(2, 2, TimeUnit.SECONDS));
        System.out.println(queue.offer(3, 2, TimeUnit.SECONDS));
//        队列满了 等待2秒 超时后退出
//        System.out.println(queue.offer(3, 2, TimeUnit.SECONDS));
        queue.poll(2,TimeUnit.SECONDS);
        queue.poll(2,TimeUnit.SECONDS);
        queue.poll(2,TimeUnit.SECONDS);
//        队列空了 等待2秒 超时后退出
//        queue.poll(2,TimeUnit.SECONDS);
    }
```

### 18、SynchronousQueue

同步队列：最多放一个元素

```java
public class BlockingQ {
    public static void main(String[] args) throws Exception {
        SynchronousQueue<String> queue = new SynchronousQueue<>();
        new Thread(()->{
            try {
                System.out.println(Thread.currentThread().getName().toString() + " put 1");
                queue.put("1");
                System.out.println(Thread.currentThread().getName().toString() + " put 2");
                queue.put("2");
                System.out.println(Thread.currentThread().getName().toString() + " put 3");
                queue.put("3");
            }catch (Exception e){
                e.printStackTrace();
            }
        }, "线程1").start();
        new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName().toString() + " => "+ queue.take());
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName().toString() + " => "+ queue.take());
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName().toString() + " => "+ queue.take());
            }catch (Exception e){
                e.printStackTrace();
            }
        }, "线程2").start();
    }
}
```

### 19、线程池

[视频链接](https://www.bilibili.com/video/BV1B7411L7tE?p=23)

线程池，连接池，内存池，对象池

线程池的好处：降低资源效率，提高相应速度，方便管理

- 线程复用
- 可以控制最大并发数
- 管理线程

线程池不允许使用Executors去创建，而是通过ThreadPoolExecutor的方式，这样的处理方式让写的同学更加明确线程池的运行规则，避免资源耗尽的风险。

说明：Executors创建线程池对象的弊端如下

- FixedThreadPool和SingleThreadPoll：允许的请求队列长度为Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM
- CachedThreadPool和ScheduledThreadPool：允许的创建线程数量为Integer.MAX_VALUE，可能会创建大量的线程，从而导致OOM

3大方法，7个参数，4种拒绝策略

```java
public class Execute {
    public static void main(String[] args) {
        //Executors工具类 3大方法
        ExecutorService executorService = Executors.newSingleThreadExecutor(); //单线程
        ExecutorService executorService1 = Executors.newFixedThreadPool(5); //固定线程
        ExecutorService executorService2 = Executors.newCachedThreadPool(); //可伸缩
        try {
            for(int i = 0; i < 10; i++){
                executorService2.execute(()->{
                    System.out.println(Thread.currentThread().getName().toString()+" ok");
                });
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            executorService.shutdown();
            executorService1.shutdown();
            executorService2.shutdown();
        }
    }
}
```

本质是：ThreadPoolExecutor()

**7大线程参数**

1. 核心线程数
2. 最大线程数
3. 超时时间
4. 超时单位
5. 阻塞队列
6. 线程工厂
7. 拒绝策略

```java
new ThreadPoolExecutor(1, 1,0L, TimeUnit.MILLISECONDS,new LinkedBlockingQueue<Runnable>())
```

**自定义线程池**

```java
public class MyPool {
    public static void main(String[] args) {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(2, //核心线程大小
                Runtime.getRuntime().availableProcessors(), //最大线程数 本机是4核
                3, //超时时间 超过这个设定时间都没有人调用 就会自动释放
                TimeUnit.SECONDS, //超时时间单位
                new LinkedBlockingDeque<>(3), //阻塞队列
                Executors.defaultThreadFactory(), //线程工厂 创建线程
                new ThreadPoolExecutor.DiscardOldestPolicy()); //队列满了 尝试去和最早的线程竞争 不会抛出异常

        try {
            // 最大承载数：最大线程数 + 阻塞队列容量 = 4 + 3 = 7 超过过7个以后，就开始拒绝
            for(int i = 1; i <= 8; i++){
                threadPoolExecutor.execute(()->{
                    System.out.println(Thread.currentThread().getName().toString()+" ok");
                });
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            threadPoolExecutor.shutdown();
        }
    }
}
```



**４种拒绝策略**

- AbortPolicy：直接抛出异常
- CallerRunsPolicy：调用者线程来运行该任务
- DiscardPolicy：不处理，直接丢弃。
- DiscardOldPolicy：尝试与最早的任务任务竞争

```java
/*
* new ThreadPoolExecutor.AbortPolicy()); //银行满了，还有人进来，不处理这个人的，抛出异常 
* new ThreadPoolExecutor.CallerRunsPolicy()); //哪儿来的去哪里 不会抛出异常 main线程执行的
* new ThreadPoolExecutor.DiscardPolicy()); //队列满了 丢掉任务 不会抛出异常
* new ThreadPoolExecutor.DiscardOldestPolicy()); //队列满了 尝试去和最早的线程竞争 不会抛出异常
* */
```

**最大的线程如何去设置**

- CPU密集型：与电脑核数相同
- IO密集型：大于耗时IO的数量即可，通常设为耗时IO的两倍。

向线程池提交任务：

- execute()用于提交不需要返回值的任务，无法判断任务是否被线程池执行成功。
- submit()用于提交需要返回值的任务，线程池会返回一个future类型的对象，通过这个future对象可以判断任务是否执行成功，并可以通过get方法获取返回值。



### 20、创建线程的三种方式

1. 继承Thread
2. 实现Runnable
3. 实现Callable

```java
public class CreateThread  extends Thread{
    @Override
    public void run() {
        for (int i = 0; i < 200; i++) {
            System.out.println("我在学习..."+i);
        }
    }

    public static void main(String[] args) {
        CreateThread createThread = new CreateThread();
        createThread.start(); //继承Thread 重写run方法 启动start方法
        for (int i = 0; i < 200; i++) {
            System.out.println("我在学多线程..."+i);
        }
    }
}
```

```java
public class CreateThread implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 200; i++) {
            System.out.println("我在学习..."+i);
        }
    }

    public static void main(String[] args) {
        CreateThread createThread = new CreateThread();
        new Thread(createThread).start(); //实现Runnable 重写run方法 启动start方法
        for (int i = 0; i < 200; i++) {
            System.out.println("我在学多线程..."+i);
        }
    }
}
```

```java
public class CreateThread{

    public static void main(String[] args) {
        MyCall myCall = new MyCall();
        FutureTask task = new FutureTask(myCall);
        new Thread(task).start(); //实现Callable 重写run方法 启动start方法
        for (int i = 0; i < 200; i++) {
            System.out.println("我在学多线程..."+i);
        }
    }
}

class MyCall implements Callable {
    @Override
    public String call() throws Exception {
        for (int i = 0; i < 200; i++) {
            System.out.println("我在学习..." + i);
        }
        return "hello world";
    }
}
```

### 21、AQS同步器

同步器是实现Lock锁的关键。许多同步类的实现都依赖于AQS，如Reentrantlock, Semaphore, CountDownLatch

锁是面向使用者，同步器是面向锁的实现者。

AQS为每个共享资源都设置了一个共享资源锁，线程在需要访问共享资源时首先需要获取**共享资源锁**，如果获取到了共享资源锁，便可以在当前线程中使用该共享资源，如果获取不到，则将线程放入线程等待队列，等待下一次调度。

AQS维护了一个**volatile int类型的变量state**，用于表示当前的**同步状态**。
state的访问方式有三种：getState() setState() compareAndSetState()均是原子操作。

AQS共享资源的方式：

- 独占式
- 共享式

AQS只是一个框架，自定义同步器需要重写**获取和释放**方法

- tryAcquire() 
- tryRelease() 
- isHeldExclusively() 
- tryAcquireShared() 
- tryReleaseShared() 

### 22、锁的四种状态和升级过程

**并且四种状态会随着竞争的情况逐渐升级，而且是不可逆的过程，即不可降级，也就是说只能进行锁升级（从低级别到高级别），不能锁降级（高级别到低级别）** 

- 无锁
- 偏向锁(默认锁)：适用于只有一个线程访问同步块场景 
- 轻量级锁(有线程竞争时，自旋10次后升级)
- 重量级锁(synchronized)

### 23、Fork/Join

Fork/Join框架是Java 7提供的一个用于并行执行任务的框架，是一个把大任务分割成若干个小任务，最终汇总每个小任务结果得到大任务结果的框架。

工作窃取work-stealing：是指某个线程从其他线程队列中窃取任务来执行。

### 24、happens-before

- as-if-serial语义保证单线程内程序的执行结果不改变
- happens-before关系保证正确同步的多线程程序的执行结果不改变

如果A happens-before B，那么Java内存模型向程序员保证，A操作的结果将对B可见，且A的执行顺序排在B之前。

as-if-serial语义和happens-before这样做的目的，都是为了在不改变程序执行结果的前提下，尽可能地提高程序执行的并行度。

### 25、四种引用

- 强引用：把一个对象赋值给一个引用变量时，这个引用变量就是强引用。有强引用的对象一定为可达性状态，所以不会被垃圾回收机制回收。
- 软引用：如果一个对象只有软引用，则在系统内存空间不足时该对象将被回收
- 弱引用：如果一个对象只有弱引用，则在垃圾回收时一定会被回收
- 虚引用：虚引用和引用队列联合使用，主要用于跟踪对象的垃圾回收状态。





