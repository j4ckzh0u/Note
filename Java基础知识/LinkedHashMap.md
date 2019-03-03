`LinkedHashMap`有一个双向的链表,把Entry连接起来.

其中存在一个重要的field `accessOrder`,默认为`false`,表示的是按照key的插入顺序连接链表.

可以通过构造函数`public LinkedHashMap(int initialCapacity,float loadFactor,boolean accessOrder)`来指定为true,表示的是按照最近访问的顺序来连接链表,即最新访问到的元素放在链表的尾部.

`removeEldestEntry(Map.Entry eldest)`方法在`put`方法执行之后触发,当返回为`true`时,会将链表头元素删除

```
public class LRU {

    private LinkedHashMap<Integer, Integer> map;


    public LRU(int capacity) {
        map = new LinkedHashMap<Integer, Integer>(capacity + 1, 1, true) {

            //这里的参数只的就是当前accessOrder下的最老的元素本身
            //     * Returns true if this map should remove its eldest entry.
            //     * This method is invoked by {@code put} and {@code putAll} after
            //     * inserting a new entry into the map.
            @Override
            protected boolean removeEldestEntry(Map.Entry eldest) {
                return this.size() > capacity;
            }
        };
    }

    public int get(int key) {
        Integer val = map.get(key);
        return val == null ? -1 : val;
    }

    public void put(int key, int value) {
        map.put(key,value);
    }
}
```

根据此思路,使用双向链表加上HashMap给出另外一种写法

```Java
public class LRUCache {

    class Node {
        int key;
        int val;
        Node pre;
        Node next;

        public Node(int key, int val) {
            this.key = key;
            this.val = val;
        }
    }

    private int cap;
    private int size;
    private Node head, tail;
    Map<Integer, Node> map;

    public LRUCache(int capacity) {
        this.cap = capacity;
        this.map = new HashMap<>();
    }

    public int get(int key) {
        Node node = map.get(key);
        if (node == null) {
            return -1;
        }
        if (size > 1 && tail != node) {
            if (node == head) {
                tail.next = head;
                head.pre = tail;
                head = head.next;
                head.pre = null;
                tail = tail.next;
                tail.next = null;
            } else {
                node.pre.next = node.next;
                node.next.pre = node.pre;
                tail.next = node;
                node.pre = tail;
                tail = tail.next;
                tail.next = null;
            }
        }
        return node.val;
    }

    public void put(int key, int value) {
        if (get(key) != -1) {
            map.get(key).val = value;
            return;
        }
        Node node = new Node(key, value);
        map.put(key, node);
        size++;
        if (size == 1) {
            head = tail = node;
            return;
        }
        tail.next = node;
        node.pre = tail;
        tail = tail.next;
        if (size > cap) {
            size = cap;
            Node header = this.head;
            head = head.next;
            head.pre = null;
            map.remove(header.key);
        }
    }
}
```

