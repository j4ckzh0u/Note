BFS： 队列加map实现，非常简单。 O(n).

- 每个点只会遍历一次
- 在简单图中找最短路径

DFS：O(n!)或者O(2^n)   思想就是：构建搜索树，判断可行解

- 找到所有可能的解
- 排列问题，子集问题

BFS搜索图的时候，是可以确保每一个点只搜索到一次的，非常适用于按照层次遍历(起点到遍历点的距离一样的在一层)

DFS搜索图的时候，尤其是对于那种无向图来说就比较复杂一些，可能就会有不同层之间来回跳。想要按层次遍历，必须要知道每一个点所在的层次，在搜索的时候选择性的剪枝。

#### 拓扑排序

```java
 public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {

        // 计算每一个点的入度
        Map<DirectedGraphNode, Integer> grade = new HashMap<>();
        for (DirectedGraphNode node : graph) {
            for (DirectedGraphNode neighbor : node.neighbors) {
                grade.put(neighbor, grade.getOrDefault(neighbor, 0) + 1);
            }
        }
        // 找到所有入度为0的点，初始化队列
        Queue<DirectedGraphNode> queue = new ArrayDeque<>();
        for (DirectedGraphNode node : graph) {
            if (!grade.containsKey(node)) {
                queue.add(node);
            }
        }
        // BFS,一个个删除入度为0的点
        ArrayList<DirectedGraphNode> result = new ArrayList<>();
        while (!queue.isEmpty()) {
            DirectedGraphNode node = queue.poll();
            result.add(node);
            for (DirectedGraphNode neighbor : node.neighbors) {
                int val = grade.get(neighbor);
                if (val == 1) {
                    queue.add(neighbor);
                } else {
                    grade.put(neighbor,val-1);
                }
            }
        }
        return result;
    }
```

#### 求全排列

```java
public class Permutations {

    public List<List<Integer>> permute(int[] nums) {
        if(nums==null || nums.length==0)  return result;
        List<List<Integer>> result = new ArrayList<>();
        backtrace(result, nums, new ArrayList<>());
        return result;
    }
    private void backtrace(List<List<Integer>> result, int[] nums, List<Integer> tmpList){

        if (tmpList.size() == nums.length) {
            result.add(new ArrayList<>(tmpList));
        }
        for (int num : nums) {
            if (!tmpList.contains(num)) {
                tmpList.add(num);
                backtrace(result, nums, tmpList);
                tmpList.remove(tmpList.size() - 1);
            }
        }
    }
}
```

#### 求子集

```java
 public List<List<Integer>> subsets(int[] nums) {
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
```

