```java
final static Thread.UncaughtExceptionHandler uncaughtExceptionHandler = new 
    Thread.UncaughtExceptionHandler() {
        @Override
        public void uncaughtException(Thread t, Throwable e) {
            System.out.println("from " + t.getName()+e);
        }
    };

    final static Runnable r=()->{int a=1/0;};

    public static void main(String[] args) {

        Thread t=new Thread(r,"测试");
        t.setUncaughtExceptionHandler(uncaughtExceptionHandler);
        t.start();

        Thread.setDefaultUncaughtExceptionHandler(uncaughtExceptionHandler);
        new Thread(r,"测试").start();
    }
```

一个线程中抛出的异常是不能被别的线程catch并处理的,再往上抛就是虚拟机了   
Thread.UncaughtExceptionHandler接口就是异常处理器，我们需要自定义类来实现这个接口，覆盖它的uncaughtException来处理异常

Thread中静态方法setDefaultUncaughtExceptionHandler为每一个线程设置同一个异常处理器
Thread的setUncaughtExceptionHandler，自定义设置异常处理器


防止指令的重排序
1. 加锁
  在获取锁的时候，它前边的操作必须已经执行完成，不能和同步代码块重排序；在释放锁的时候，同步代码块中的代码必须全部执行完成，不能和同步代码块后边的代码重排序
2. volatile
  volatile写之前的操作不会被重排序到volatile写之后。
  volatile读之后的操作不会被重排序到volatile读之前。
  前边是volatile写，后边是volatile读，这两个操作不能重排序。
3. final
  在构造方法内对一个final字段的写入，与随后把这个被构造对象的引用赋值给一个引用变量，这两个操作之间不能重排序。
  初次读一个包含final字段对象的引用，与随后初次读这个final字段，这两个操作不能重排序.


interrupt() # 将线程的中断状态设置为true
isInterrupted()   # 返回中断状态
static interrupted # 静态方法，返回中断状态。如果中断状态为true,则设置为false

一个线程给另一个线程发送中断信号只是一厢情愿的事儿，如果接收中断信号的线程对于这个信号置之不理，并不会有任何影响

但是因为调用了Thread类的 join、sleep方法或者Object类的wait方法线程发生了阻塞，这些方法都有一个InterruptedException的异常说明，一个线程在调用这些方法之前或者阻塞过程中都会监测自己的中断状态是否为true，如果为true，立即返回并且抛出一个InterruptedException的异常，而且还会清除该线程的中断状态，也就是把中断状态再次修改为false。



Lock lock = new ReentrantLock();
try {   //第1个try块

```java
lock.lockInterruptibly();   //可中断的锁

try {   //第2个try块
    // ... 具体代码
} finally {
    lock.unlock();  //释放锁
}
```

} catch (InterruptedException e) {
​    // ... 在被中断的时候的处理代码
}


一个线程因为调用lockInterruptibly方法在等待获取锁的过程中发生阻塞，此时另一个线程向该线程发送中断信号的话，则lockInterruptibly方法会立即返回，并且抛出一个InterruptedException异常，并且清除线程的中断状态


Lock lock = new ReentrantLock();

try {
​    boolean result = lock.tryLock(1000L, TimeUnit.MILLISECONDS);
​    if (result) {   //在指定时间内获取锁成功
​        try {
​            // ... 获取到锁的代码
​        } finally {
​            lock.unlock();  //释放锁
​        }

```java
} else {   //在指定时间内获取锁失败
    // ... 获取锁失败的代码

}
```
} catch (InterruptedException e) {
​    // ... 被中断时的异常处理代码
}


与lockInterruptibly一样，这个定时获取锁的方法也可能因为别的线程发送中断信号而从阻塞状态中返回并且抛出InterruptedException异常，我们需要做好异常处理工作。

默认是非公平锁
因为唤醒等待的进程需要时间，甚至有些进程由于太久时间没有被唤醒，甚至被放到交换内存[硬盘]里面了


AtomicIntegerFieldUpdater，原子方式更新某个对象的int型字段
AtomicLongFieldUpdater，原子方式更新某个对象的long型字段
AtomicReferenceFieldUpdater，原子方式更新引用类型指向对象里的字段

这些类都是抽象类，每次使用的时候必须用newUpdater()静态方法创建一个对象，调用这个方法的时候需要更新的类和字段名，需要注意的是，更新的字段必须使用public volatile修饰。我们以AtomicIntegerFieldUpdater为例来看一下：

import java.util.concurrent.atomic.AtomicIntegerFieldUpdater;

public class AtomicIntegerFieldUpdaterDemo {

```java
private static class MyObj {
    public volatile int i;

    public MyObj(int i) {
        this.i = i;
    }
}

private static AtomicIntegerFieldUpdater<MyObj> updater = AtomicIntegerFieldUpdater.newUpdater(MyObj.class, "i");

public static void main(String[] args) {
    MyObj myObj = new MyObj(5);
    System.out.println("更新后的值是：" +  updater.incrementAndGet(myObj));
}
```
}