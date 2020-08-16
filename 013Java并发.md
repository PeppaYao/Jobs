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
2.  synchronized会自动释放锁，lock需要手动释放。
3. synchronized线程获取方式是阻塞等待，而lock可以尝试获取锁。
4. synchronized是非公平锁，Lock可以设置公平与非公平
5. synchronized适合锁少量的同步代码，lock适合锁大量的同步代码。

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

condition的优势：精准的通知和唤醒线程。

