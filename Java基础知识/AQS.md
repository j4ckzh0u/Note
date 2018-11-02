AbstractQueuedSynchronizer是一个抽象类[AQS].抽象队列同步器

## State
AQS中最为重要的一个字段就是state,表示当前的同步状态。甚至可以进一步认为，state的值就是各个线程需要争夺的资源，  
就是锁的本体。但是具体如何竞争，竞争规则是什么。就看子类的具体实现了
```Java
private volatile int state;
```
通过state实现独占模式和共享模式  
在使用AQS的时候，需要自定义获取同步状态与释放同步状态的方式，也就是说获取当前同步状态的方式是自定义实现的。
```Java
protected boolean tryAcquire(int arg);  //尝试独占获取同步状态
protected boolean tryRelease(int arg);
protected int tryAcquireShared(int arg);
protected boolean tryReleaseShared(int arg);
protected boolean isHeldExclusively(); //判断当前线程是否获得了独占的同步的状态。
```

例如在独占模式下我们可以这样定义一个AQS子类
``` Java
public class Sync extends AbstractQueuedSynchronizer {

    @Override
    protected boolean tryAcquire(int arg) {
        return compareAndSetState(0, 1);
    }

    @Override
    protected boolean tryRelease(int arg) {
        setState(0);
        return true;
    }

    @Override
    protected boolean isHeldExclusively() {
        return getState() == 1;
    }
}
```


# 同步队列
就是线程的等待队列，当线程tryAcquire失败之后，就会放到等待队列中。当同步状态被释放之后，会从队列中取出节点[线程]执行
```Java
static final class Node {
    //表示当前节点的下一个节点的状态。因此头节点的状态为-1，尾节点的状态为0
    volatile int waitStatus; 
    volatile Node prev;
    volatile Node next;
    volatile Thread thread;
    Node nextWaiter;

    static final int CANCELLED =  1;
    static final int SIGNAL    = -1;
    static final int CONDITION = -2;
    static final int PROPAGATE = -3;
}
```


## 主要的方法




下面通过acquire的流程来梳理一下代码的结构。。

## public final void acquire(int arg) 方法

尝试独占式的获取同步的状态，如果成功则返回，否则将线程加入到等待队列中，并且阻塞这个线程
```Java
public final void acquire(int arg) {

    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}
```
主要是acquireQueued(addWaiter(Node.EXCLUSIVE), arg)这个方法的调用
首先看addWaiter(Node.EXCLUSIVE)方法   
将一个线程作为一个新的节点放到等待队列中，并且返回新节点
```Java
private Node addWaiter(Node mode) {

    Node node = new Node(Thread.currentThread(), mode);  //构造一个新节点
    Node pred = tail;
    if (pred != null) { 			
        node.prev = pred;
	// 尾巴节点不是空，直接插入到尾部就可以了。这里尝试插入一次，插入失败的话就走enq()函数
        if (compareAndSetTail(pred, node)) {      
            pred.next = node;
            return node;
        }
    }
    enq(node);
    return node;
}

private Node enq(final Node node) {
    for (;;) {
        Node t = tail;
        if (t == null) {    //tail节点为空，初始化队列
            if (compareAndSetHead(new Node()))  //设置head节点
                tail = head;
        } else {  	    //tail节点不为空，开始真正插入节点
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```
在看acquireQueued()方法，也是尝试获得独占式的同步状态，否则写入到队列中
```Java
final boolean acquireQueued(final Node node, int arg) {

    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();  //获取前一个节点

	    // 前一个节点是头节点再次尝试获取同步状态
            // 前一个节点是头节点意味着是下一个可能获得同步状态的点，因此尝试获得tryAcquire一下
	    // 如果成功了就直接返回，并且从队列中拿掉[setHead(node)实际上就是相当于从队列中拿掉]
            if (p == head && tryAcquire(arg)) { 
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}

检查一下当前节点的前面的节点是不是SIGNAL状态，如果是返回true。  
否则将前面的所有的处于CANCELLED状态的点移除掉，放回false  
实际上就是将当前的点之前的CANCELLED状态的点移除掉
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    int ws = pred.waitStatus;   //前一个节点的状态
    if (ws == Node.SIGNAL)  //Node.SIGNAL的值是-1
        return true;
    if (ws > 0) {   //当前线程已被取消操作，把处于取消状态的节点都移除掉
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {    //设置前一个节点的状态为-1
        // 这里很重要，一个新的点的初始的waitStatus是0，当这个点后面有新的点的时候，这个点的状态就变为-1。
	// 就是说只有队列为尾部的点的waitstatus才可能是0	
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}
移除掉CANCELLED的点之后，就阻塞当前线程
private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);
    return Thread.interrupted();
}

// 立即阻塞线程
public static void park(Object blocker) {
    Thread t = Thread.currentThread();
    setBlocker(t, blocker);
    UNSAFE.park(false, 0L);     //调用底层方法阻塞线程
    setBlocker(t, null);
}

```


### AQS的使用。。
自定义一个AQS的子类之后。就获得了一个独占/共享获得同步状态的机制，也就实现了锁的操作。。

一个锁的实现
```Java
public class PlainLock {

    private static class Sync extends AbstractQueuedSynchronizer {

        @Override
        protected boolean tryAcquire(int arg) {
            return compareAndSetState(0, 1);
        }

        @Override
        protected boolean tryRelease(int arg) {
            setState(0);
            return true;
        }

        @Override
        protected boolean isHeldExclusively() {
            return getState() == 1;
        }
    }

    private Sync sync = new Sync();


    public void lock() {
        sync.acquire(1);
    }

    public void unlock() {
        sync.release(1);
    }
}
```

通过共享访问的方式，同时允许两个线程进入锁
```Java
public class DoubleLock {


    private static class Sync extends AbstractQueuedSynchronizer {

        public Sync() {
            super();
            setState(2);    //设置同步状态的值
        }

        @Override
        protected int tryAcquireShared(int arg) {
            while (true) {
                int cur = getState();
                int next = getState() - arg;
                if (compareAndSetState(cur, next)) {
                    return next;
                }
            }
        }

        @Override
        protected boolean tryReleaseShared(int arg) {
            while (true) {
                int cur = getState();
                int next = cur + arg;
                if (compareAndSetState(cur, next)) {
                    return true;
                }
            }
        }
    }

    private Sync sync = new Sync();

    public void lock() {
        sync.acquireShared(1);     
    }

    public void unlock() {
        sync.releaseShared(1);
    }
}
```

## ReentrantLock的内部实现










1.  ThreadLocalMap是在Thread类中的一个变量，相当于每一个线程中有一个ThreadLocalMap的实例
2.  如果set值的时候，ThreadLocalMap不存在，就进行创建
3.  ThreadLocalMap的key是this，val是具体的值。也就是说，我们会有很多ThreadLocal的变量，每一个变量其实就是ThreadLocalMap中的一个Entry.【原因是ThreadLocalMap是ThreadLocal的内部类，所有对于ThreadLocalMap的操作都是在ThreadLocal类中进行的，因此this就代表当前这个ThreadLocal类本身】
4.  总结：每个线程Thread内部有一个ThreadLocal.ThreadLocalMap类型的成员变量threadLocals，这个threadLocals就是用来存储实际的变量副本的，键值为当前ThreadLocal变量，value为变量副本
    ThreadLocal 本身并不存储值，它只是作为一个 key 来让线程从 ThreadLocalMap 获取 value
5.  ThreadLocalMap 是使用 ThreadLocal 的弱引用作为 Key 的，弱引用的对象在 GC 时会被回收。
    这样的话，如果ThreadLocal对象的没有引用了，存在于map中的key就是弱引用的，很快就会被回收i
6.  protected T initialValue()方法可重写，如果get的时候没有值，会返回默认值