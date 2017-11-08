
Callabl和runable类似的，只是有返回值（通过泛型指定类型）；  
callable的返回值的接收使用 的是FutureTask。  
通过FutureTask的get方法得到返回值。  
get方法是阻塞的，在对象的线程没有返回之前，get就会一直在等待，后续的代码不会继续执行    
这一个特性，与countdownLatch有相似之处  
public V get(long timeout, TimeUnit unit)   
由于get会阻塞，因此可以使用这个函数，设置最长能够容忍度等待时间，时间到了就会抛出TimeoutException

```
 public static void main(String[] args) {


        Callable<Integer> task=()->{
            TimeUnit.SECONDS.sleep(1);
            return 1;
        };

        FutureTask<Integer> future=new FutureTask<>(task);

        new Thread(future).start();

        try {
            System.out.println(future.get());
            System.out.println("end");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
```
