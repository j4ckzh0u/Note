以ArrayList为例啊！！！！

`modcount`是`AbstractArrayList`里面的一个变量。当List的结构发生变化的时候，count++。例如：添加删除插入排序等等

对于集合的一些操作，我们希望是在操作的整个过程中，集合的数据内容不发生变化，比如说我要sort、遍历等等。
使用modcount就是在这些操作开始的时候，去记录当前的modcount的值，然后在操作的过程中去检查这个值有没有被别人动过（也就是检查集合数据是否发生了变化），如果变化了就直接抛出ConcurrentModifyException。

因为arrayList本身就是为了单线程程序服务的，因此你在我遍历的时候，修改数组当然不被允许。与之对应的CoyOnWriteArrayList，是在iterator中先复制一下副本，然后遍历，就不存在这个问题了。

另外在ArrayList的Iterator中，可以再遍历的时候删除数据，具体的操作是，把当前的指针，移动到上一个位置，然后修改exceptionmodcount。





特殊情况：

```Java
		List<String> list=new ArrayList<>(Arrays.asList("1","2"));
        Iterator<String> iterator=list.iterator();
        while (iterator.hasNext()){
            String item=iterator.next();
            if(item.equals("1")){
                list.remove(item);
            }
        }
        System.out.println(list);
输出【2】
```

这个理论上是要抛出异常的，但是呢由于数组长度只有2，然后在remove的时候，是不会调整iterator的指针的，指针值为1 的时候，list的长度也是1，hasNext的时候就直接退出。

还有有趣的

```Java
   		List<String> list=new ArrayList<>(Arrays.asList("1","1","1"));
        for (int i = 0; i < list.size(); i++) {
            if (list.get(i).equals("1")){
                list.remove(i);
            }
        }
        System.out.println(i);
```

由于是list.remove()，没有改变i，所以呢。每次会有一个数据从中间漏过去。。



最后给出iterator的代码

````Java
private class Itr implements Iterator<E> {
        int cursor;       // index of next element to return
        int lastRet = -1; // index of last element returned; -1 if no such
        int expectedModCount = modCount;

        Itr() {}

        public boolean hasNext() {
            return cursor != size;
        }

        @SuppressWarnings("unchecked")
        public E next() {
            checkForComodification();
            int i = cursor;
            if (i >= size)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i + 1;
            return (E) elementData[lastRet = i];
        }

        public void remove() {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                ArrayList.this.remove(lastRet);
                cursor = lastRet;   //关键
                lastRet = -1;
                expectedModCount = modCount;  //关键
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }

        @Override
        @SuppressWarnings("unchecked")
        public void forEachRemaining(Consumer<? super E> consumer) {
            Objects.requireNonNull(consumer);
            final int size = ArrayList.this.size;
            int i = cursor;
            if (i >= size) {
                return;
            }
            final Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length) {
                throw new ConcurrentModificationException();
            }
            while (i != size && modCount == expectedModCount) {
                consumer.accept((E) elementData[i++]);
            }
            // update once at end of iteration to reduce heap write traffic
            cursor = i;
            lastRet = i - 1;
            checkForComodification();
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
````









