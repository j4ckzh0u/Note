## 前序遍历

```Java
public List<Integer> preorderTraversal(TreeNode root) {
        
        List<Integer> res=new ArrayList<>();
        ArrayDeque<TreeNode> stack=new ArrayDeque<>();
        
        while(!stack.isEmpty() || root!=null){
            if(root!=null){
                res.add(root.val);
                stack.push(root);
                root=root.left;
            }else{
                TreeNode node=stack.pop();
                if(node.right!=null){
                    root=node.right;   
                }
            }
        }
        return res;
    }
```

## 中序遍历

```Java
public List<Integer> inorderTraversal(TreeNode root) {
        TreeNode t = root;
        ArrayDeque<TreeNode> stack = new ArrayDeque<>();
        List<Integer> res = new ArrayList<>();

        while (!stack.isEmpty() || t != null) {
            while (t!=null){
                stack.push(t);
                t=t.left;
            }
            t=stack.pop();
            res.add(t.val);
            t=t.right;
        }
        return res;
    }
```

## 层次遍历

```Java
    public List<List<Integer>> levelOrder(TreeNode root) {

        List<List<Integer>> res=new ArrayList<>();
        if (root==null) return res;
        ArrayDeque<TreeNode> queue=new ArrayDeque<>();
        queue.offer(root);
        while (!queue.isEmpty()){
            int size=queue.size();
            List<Integer> tmp=new ArrayList<>();
            for (int i = 0; i < size; i++) {
                TreeNode node=queue.poll();
                tmp.add(node.val);
                if(node.left!=null) queue.offer(node.left);
                if(node.right!=null) queue.offer(node.right);
            }
            res.add(tmp);
        }
        return res;
    }		
```

## 判断链表带环

````Java
 public boolean hasCycle(ListNode head) {
        if(head==null) return false;
        ListNode slow, fast;
        slow = fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow=slow.next;
            fast=fast.next.next;
            if(slow==fast){
                return true;
            }
        }
        return false;
    }
````

## 翻转链表

```java
public ListNode reverseList(ListNode head) {
    ListNode newHead = null;
    while (head != null) {
        ListNode next = head.next;
        head.next = newHead;
        newHead = head;
        head = next;
    }
    return newHead;
}
```

## 合并有序链表

```Java
 public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        
        ListNode dummy=new ListNode(-1),node=dummy;
        while (l1!=null && l2!=null){
            while (l1!=null && l1.val<=l2.val){
                node.next=l1;
                l1=l1.next;
                node=node.next;
            }
            while (l1!=null && l2!=null && l1.val>=l2.val){
                node.next=l2;
                l2=l2.next;
                node=node.next;
            }
        }
        if (l1!=null){
            node.next=l1;
        }else {
            node.next=l2;
        }
        return dummy.next;
    }
```

## 二叉树的最近公共祖先

```Java
 public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
         if(root==null)
            return null;
        if(root==p||root==q)
            return root;

        TreeNode left=lowestCommonAncestor(root.left,p,q);
        TreeNode right=lowestCommonAncestor(root.right,p,q);

        if(left==null)
            return right;
        if(right==null)
            return left;
        return root;
    }
```

## 颜色分类(0-1-2)

```Java
    /*
     *  i表示的是下一个放0的位置
     *  j表示下一个放1的位置
     *  但是当nums[i]==2时，交换之后需要回退一步
     */
    public void sortColors(int[] nums) {
        int i=0,j=nums.length-1;
        int index=0;
        while(i<=j){
           if(nums[i]==0){
               nums[i]=nums[index];
               nums[index]=0;
               index++;
           }else if(nums[i]==2){
               nums[i]=nums[j];
               nums[j]=2;
               i--; j--;
           }
           i++;
        }
    }
```

## 数组中的第k个最大的元素

```java
 public int findKthLargest(int[] nums, int k) {
        
        PriorityQueue<Integer> heap=new PriorityQueue<>();
        for(int i=0;i<k;i++){
            heap.offer(nums[i]);    
        }
        for(int i=k;i<nums.length;i++){
            if(nums[i]>heap.peek()){
                heap.poll();
                heap.offer(nums[i]);
            }
        }
        return heap.peek();
    }
```

## 无向图的宽度优先搜索

```java
	    list.add(root);  // 搜索结果
        map.put(root,new UndirectedGraphNode(root.label));
        while(index<list.size()){
            UndirectedGraphNode curNode=list.get(index++);
            for(UndirectedGraphNode neighbor:curNode.neighbors){
                if(!map.containsKey(neighbor)){
                    list.add(neighbor);
                    map.put(neighbor,new UndirectedGraphNode(neighbor.label));
                }
            }
        }
```

## 最长上升子序列

```java
   dp[i]表示的是最长上升子序列的长度为i的时候,最后的元素需要满足的值.
   public int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        int len = 0; 
        for (int x : nums) {
            int i=binarySearch(dp, len, x);
            dp[i]=x;
            if(i==len){
                len++;
            }
        }
        return len;
    }

    // 找到第一个比key大的元素的位置
    private int binarySearch(int A[], int len, int key) {
        if(len==0) return 0;
        if (key <= A[0]) return 0;
        if(key >= A[len - 1]) return len;
       
        int low = 0, high = len - 1;
        while (low + 1 < high) {
            int mid = low + ((high - low)>>1);
            if (A[mid] == key) {
                return mid;
            } else if (A[mid] > key) {
                high = mid;
            } else {
                low = mid;
            }
        }
        if (A[low] > key) {
            return low;
        }
        return high;
    }
```

## 拓扑排序

```java
public void topoSortByKahn() {
      int[] inDegree = new int[v]; // 统计每个顶点的入度
      for (int i = 0; i < v; ++i) {
        for (int j = 0; j < adj[i].size(); ++j) {
          int w = adj[i].get(j); // i->w
          inDegree[w]++;
        }
      }
      LinkedList<Integer> queue = new LinkedList<>();
      for (int i = 0; i < v; ++i) {
        if (inDegree[i] == 0) queue.add(i);
      }
      while (!queue.isEmpty()) {
        int i = queue.remove();
        System.out.print("->" + i);
        for (int j = 0; j < adj[i].size(); ++j) {
          int k = adj[i].get(j);
          inDegree[k]--;
          if (inDegree[k] == 0) queue.add(k);
        }
      }
}
// 还有一种方式是通过dfs实现
```

## Dijkstra 算法

```java
public void dijkstra(int s, int t) { // 从顶点 s 到顶点 t 的最短路径
  int[] predecessor = new int[this.v]; // 用来还原最短路径
  Vertex[] vertexes = new Vertex[this.v];
  for (int i = 0; i < this.v; ++i) {
    vertexes[i] = new Vertex(i, Integer.MAX_VALUE);
  }
  PriorityQueue queue = new PriorityQueue(this.v);// 小顶堆
  boolean[] inqueue = new boolean[this.v]; // 标记是否进入过队列
  vertexes[s].dist = 0;
  queue.add(vertexes[s]);
  inqueue[s] = true;
  while (!queue.isEmpty()) {
    Vertex minVertex= queue.poll(); // 取堆顶元素并删除
    if (minVertex.id == t) break; // 最短路径产生了
    for (int i = 0; i < adj[minVertex.id].size(); ++i) {
      Edge e = adj[minVertex.id].get(i); // 取出一条 minVetex 相连的边
      Vertex nextVertex = vertexes[e.tid]; // minVertex-->nextVertex
      if (minVertex.dist + e.w < nextVertex.dist) { // 更新 next 的 dist
        nextVertex.dist = minVertex.dist + e.w;
        predecessor[nextVertex.id] = minVertex.id;
        if (inqueue[nextVertex.id] == true) {
          queue.update(nextVertex); // 更新队列中的 dist 值
        } else {
          queue.add(nextVertex);
          inqueue[nextVertex.id] = true;
        }
      }
    }
  }
  // 输出最短路径
  System.out.print(s);
  print(s, t, predecessor);
}
```

## KMP

```java
// b 表示模式串，m 表示模式串的长度
private static int[] getNexts(char[] b, int m) {
      int[] next = new int[m];
      next[0] = -1;
      int k = -1; //表示上一个next的结果
      for (int i = 1; i < m; ++i) {
        while (k != -1 && b[k + 1] != b[i]) {
          k = next[k];
        }
        if (b[k + 1] == b[i]) {
          ++k;
        }
        next[i] = k;
      }
      return next;
}
```


## AQS

```java
public class AQS1<E> {

    private int head;
    private int tail;
    private Object[] arr;
    private int size = 0;

    public AQS1(int capability) {
        arr = new Object[capability];
        head = tail = 0;
    }

    // 使用wait/notify必须拥有其monitor,因此在这里只能使用一个监控器
    // 当从wait中被唤醒之后,首先进入请求锁的等待队列中
    public synchronized void put(E e) throws InterruptedException {
        while (size == arr.length) {
            this.wait();
        }
        arr[tail] = e;
        if (++tail == arr.length) {
            tail = 0;
        }
        size++;
        this.notifyAll();
    }

    public synchronized E take() throws InterruptedException {
        while (size == 0) {
            this.wait();
        }
        E ret = (E) arr[head];
        if (++head == arr.length) {
            head = 0;
        }
        this.notifyAll();
        size--;
        return ret;
    }

```

```java
public class AQS2<E> {

    private final Object[] items;
    private int takeIndex;
    private int putIndex;
    private int count;
    private final ReentrantLock lock;
    private final Condition notEmpty;
    private final Condition notFull;

    public AQS2(int capacity) {
        if (capacity <= 0)
            throw new IllegalArgumentException();
        this.items = new Object[capacity];
        lock = new ReentrantLock();
        notEmpty = lock.newCondition();
        notFull = lock.newCondition();
    }

    public void put(E e) throws InterruptedException {
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == items.length)
                notFull.await();
            items[putIndex] = e;
            if (++putIndex == items.length) putIndex = 0;
            count++;
            notEmpty.signal();
        } finally {
            lock.unlock();
        }
    }

    public E take() throws InterruptedException {
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == 0)
                notEmpty.await();
            E e = (E) items[takeIndex];
            items[takeIndex] = null;
            if (++takeIndex == items.length) takeIndex = 0;
            count--;
            notFull.signal();
            return e;
        } finally {
            lock.unlock();
        }
    }
}

```

## 排序

```java
  	  public void mergeSort(int A[], int from, int to) {
            if (from < to) {
                int mid = ((to - from) >> 1) + from;
                mergeSort(A, from, mid);
                mergeSort(A, mid + 1, to);
                merge(A, from, mid, to);
            }
        }

        private void merge(int A[], int from, int mid, int to) {
            int[] t = new int[to - from + 1];
            int indexA = from, indexB = mid + 1, indext = 0;
            while (indexA <= mid && indexB <= to) {
                while (indexA <= mid && A[indexA] <= A[indexB]) {
                    t[indext++] = A[indexA++];
                }
                while (indexA <= mid && indexB <= to && A[indexA] >= A[indexB]) {
                    t[indext++] = A[indexB++];
                }
            }
            while (indexA <= mid) {
                t[indext++] = A[indexA++];
            }
            while (indexB <= to) {
                t[indext++] = A[indexB++];
            }
            for (int i = 0; i < t.length; i++) {
                A[from + i] = t[i];
            }
        }
```

```java
 		public void quickSort(int A[], int from, int to) {
            if (from < to) {
                int pivot = quickPass(A, from, to);
                quickSort(A, from, pivot - 1);
                quickSort(A, pivot + 1, to);
            }
        }

        private int quickPass(int A[], int from, int to) {
            int t = A[from];
            while (from < to) {
                while (from < to && A[to] >= t) {
                    to--;
                }
                A[from] = A[to];
                while (from < to && A[from] <= t) {
                    from++;
                }
                A[to] = A[from];
            }
            A[from] = t;
            return from;
        }
```

```java
 	    public void insertSort(int A[]) {
            for (int i = 1; i < A.length; i++) {
                int t = A[i];
                int j = i - 1;
                while (j >= 0 && A[j] > t) {
                    A[j + 1] = A[j];
                    j--;
                }
                A[j + 1] = t;
            }
        }
         public void bubbleSort(int[] A) {
            for (int i = 0; i < A.length - 1; i++) {
                for (int j = 0; j < A.length - i - 1; j++) {
                    if (A[j] > A[j + 1]) {
                       swap(A,j,j+1);
                    }
                }
            }
        }
        public void selectSort(int[] A) {
            for (int i = 0; i < A.length - 1; i++) {
                int min = i;
                for (int j = i; j < A.length; j++) {
                    if (A[j] < A[min]) {
                        min = j;
                    }
                }
                swap(A,i,min);
            }
        }
```

## swap(a,b)

```java
a = a ^ b;
b = a ^ b;
a = a ^ b;
```

## 判断有向图是否有环

1: 拓扑排序    2:直接dfs

```java
public boolean canFinish(int[][] graph) {
   
    boolean[] globalMarked = new boolean[graph.length];
    boolean[] localMarked = new boolean[graph.length];
    for (int i = 0; i < raph.length; i++) {
        if (hasCycle(globalMarked, localMarked, graph, i)) {
            return false;
        }
    }
    return true;
}

private boolean hasCycle(boolean[] globalMarked, boolean[] localMarked,
                         int[][] graph, int curNode) {

    if (localMarked[curNode]) {  // 发现有环了
        return true;
    }
    if (globalMarked[curNode]) {   // 这个点已经访问过了,不用遍历了
        return false;
    }
    globalMarked[curNode] = true;
    localMarked[curNode] = true;
    for (int nextNode : graphic[curNode]) {
        if (hasCycle(globalMarked, localMarked, graphic, nextNode)) {
            return true;
        }
    }
    localMarked[curNode] = false;
    return false;
}
```

## 并查集

```java
class UnionSet {

    int f[];

    public UnionSet(int n) {
        f = new int[n];
        for (int i = 0; i < n; i++) {
            f[i] = i;
        }UnionSet
    }

    public void union(int a, int b) {
        f[getRoot(a)] = getRoot(b);
    }

    public boolean isConnected(int a, int b) {
        return getRoot(a) == getRoot(b);
    }

    private int getRoot(int a) {

        int x = a;     public List<List<Integer>> subsets(int[] nums) {
        if (nums == null || nums.length == 0) return new ArrayList<>();
        List<List<Integer>> result = new ArrayList<>();
        backtrace(result, new ArrayList<Integer>(), nums, 0);
        return result;
    }

    private void backtrace(List<List<Integer>> result, List<Integer> tmpList, int[] nums, int index) {

        result.add(new ArrayList<>(tmpList));

        for (int i = index; i < nums.length; i++) {
            tmpList.add(nums[i]);
            backtrace(result, tmpList, nums, i + 1);
            tmpList.remove(tmpList.size() - 1);
        }
    }
        while (f[x] != x) {
            x = f[x];
        }
        // 这里缩短路径,很重要
        int t;
        while (f[a] != x) {
            t = f[a];
            f[a] = x;
            a = t;
        }
        return x;
    }
}
```

## 回溯

### 子集(无重复)

```java
     public List<List<Integer>> subsets(int[] nums) {
        if (nums == null || nums.length == 0) return new ArrayList<>();
        List<List<Integer>> result = new ArrayList<>();
        backtrace(result, new ArrayList<Integer>(), nums, 0);
        return result;
    }

    private void backtrace(List<List<Integer>> result, List<Integer> tmpList, 
                           	int[] nums, int index) {
        result.add(new ArrayList<>(tmpList));
        
        for (int i = index; i < nums.length; i++) {
            tmpList.add(nums[i]);
            backtrace(result, tmpList, nums, i + 1);
            tmpList.remove(tmpList.size() - 1);
        }
    }
```

### 子集(有重复)

```java
 public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);  // sort
    backtrack(list, new ArrayList<>(), nums, 0);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, 	
                       int [] nums, int start){
    list.add(new ArrayList<>(tempList));
    for(int i = start; i < nums.length; i++){
        if(i > start && nums[i] == nums[i-1]) continue; // skip duplicates
        tempList.add(nums[i]);
        backtrack(list, tempList, nums, i + 1);
        tempList.remove(tempList.size() - 1);
    }
}
```

### 全排列(无重复)

```java
public List<List<Integer>> permute(int[] nums) {
   List<List<Integer>> list = new ArrayList<>();
   backtrack(list, new ArrayList<>(), nums);
   return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums){
   if(tempList.size() == nums.length){
       list.add(new ArrayList<>(tempList));
       return;
   } 
    for(int i = 0; i < nums.length; i++){ 
        if(tempList.contains(nums[i])) continue; // element already exists, skip
        tempList.add(nums[i]);
        backtrack(list, tempList, nums);
        tempList.remove(tempList.size() - 1);
    }
} 
```

### 全排列(有重复)

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, new boolean[nums.length]);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, 
                       int [] nums, boolean [] used){
    if(tempList.size() == nums.length){
        list.add(new ArrayList<>(tempList));
        return;
    } 
    for(int i = 0; i < nums.length; i++){
        if(used[i] || i > 0 && nums[i] == nums[i-1] && !used[i - 1]) continue;
        used[i] = true; 
        tempList.add(nums[i]);
        backtrack(list, tempList, nums, used);
        used[i] = false; 
        tempList.remove(tempList.size() - 1);
    }
}
```

## 树状数组

```java
class NumArray {

    private int[] nums;
    private int[] C;
    private int[] lowBits;  // C[i]是lowBits[i]个元素的和

    public NumArray(int[] nums) {
        C = new int[nums.length];
        lowBits = new int[nums.length];
        this.nums = nums;

        for (int i = 0; i < C.length; i++) {
            lowBits[i] = (i + 1) ^ (i & (i + 1));
            for (int j = i; j > i - lowBits[i]; j--) {
                C[i] += nums[j];
            }
        }
    }

    private int sum(int n) {
        int sum = 0;
        while (n >= 0) {
            sum += C[n];
            n -= lowBits[n];
        }
        return sum;
    }

     public void update(int i, int val) {
        int delta = val - nums[i];
        nums[i] = val;
        while (i < nums.length) {
            C[i] += delta;
            i += lowBits[i];
        }
    }

    public int sumRange(int i, int j) {
        return sum(j) - sum(i-1);
    }
}
```

线段树

```java
public class NumArrayI {


    class Node {
        int l, r , val;
        Node(int l, int r) {
            this.l = l;this.r = r;
        }
    }

    private int[] nums;
    private Node[] tree;

    public NumArrayI(int[] nums) {

        this.nums = nums;
        // 叶子节点是N,总结点数最多4*N
        tree = new Node[nums.length * 4];
        build(0, 0, nums.length - 1);

    }

    private void build(int n, int l, int r) {
        tree[n] = new Node(l, r);
        if (l == r) {
            tree[n].val = nums[l];
            return;
        }
        int mid = (l + r) / 2;
        build(2 * n + 1, l, mid);
        build(2 * n + 2, mid + 1, r);
        tree[n].val = tree[2 * n + 1].val + tree[2 * n + 2].val;
    }

    public void update(int i, int val) {
        update(0, i, val);
    }

    public int sumRange(int i, int j) {
        return sumRange(0, i, j);
    }
    
    private void update(int n, int i, int val)//单点修改
    {
        if (tree[n].l == tree[n].r) {
            tree[n].val = val;
            return;
        }
        int m = (tree[n].l + tree[n].r) / 2;
        if (i <= m) update(n * 2 + 1, i, val);
        else update(n * 2 + 2, i, val);
        tree[n].val = tree[n * 2 + 1].val + tree[n * 2 + 2].val;
    }

    private int sumRange(int n, int i, int j)//区间查询
    {
        if (tree[n].l >= i && tree[n].r <= j) {
            return tree[n].val;
        }
        int result = 0;
        int mid = (tree[n].l + tree[n].r) / 2;
        if (i <= mid) {
            result += sumRange(2 * n + 1, i, j);
        }
        if (j > mid) {
            result += sumRange(2 * n + 2, i, j);
        }
        return result;
    }

}
```



