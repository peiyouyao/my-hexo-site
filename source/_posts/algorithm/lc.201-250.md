---
title: LeetCode 201-250
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

---

## [201. Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range/)

```
left  = 9  = 00001001
right = 12 = 00001100
                 ^公共前缀 
ans =        00001000
找到它们二进制表达的, 即^处, 把^后面的位数全部抹零, 就是答案
```

```java
class Solution {
    public int rangeBitwiseAnd(int left, int right) {
        int shift = 0;
        while (left < right) {
            left = left >> 1;
            right = right >> 1;
            shift ++;
        }
        return left << shift;
    }
}
```



## [202. Happy Number](https://leetcode.com/problems/happy-number/) [Hash]



## [203. Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/) [List]



## [204. Count Primes](https://leetcode.com/problems/count-primes/)

```java
class Solution {
    public int countPrimes(int n) {
        boolean[] notprime = new boolean[n];
        int cnt = 0;
        for (int i = 2; i < n; i ++) {
            if (notprime[i]) continue;
            cnt ++;
            int j = i * i; // 从 i * i 开始, 因为 1 * i, 2 * i ... 必然已经被标注过了
            if ((long)i*(long)i > Integer.MAX_VALUE) continue;
            while (j < n) {
                notprime[j] = true;
                j += i;
            }
        }
        return cnt;
    }
}
```



## [205. Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/) [Hash & String]



## [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) [List]



## [207. Course Schedule](https://leetcode.com/problems/course-schedule/) [Graph]



## [208. Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)

```java
class Trie {
    private class Node {
        boolean end;
        Node[] nxts;
        Node() { end = false; nxts = null; }
    }
    private Node root;
    
    public Trie() { root = new Node(); }
    
    public void insert(String word) {
        Node p = root;
        for (char ch : word.toCharArray()) {
            int idx = (int)(ch - 'a');
            if (p.nxts == null) p.nxts = new Node[26];
            if (p.nxts[idx] == null) p.nxts[idx] = new Node();
            p = p.nxts[idx];
        }
        p.end = true;
    }
    
    public boolean search(String word) {
        Node p = root;
        for (char ch : word.toCharArray()) {
            int idx = (int)(ch - 'a');
            if (p.nxts == null) return false;
            if (p.nxts[idx] == null) return false;
            p = p.nxts[idx];
        }
        return p.end;
    }
    
    public boolean startsWith(String prefix) {
        Node p = root;
        for (char ch : prefix.toCharArray()) {
            int idx = (int)(ch - 'a');
            if (p.nxts == null) return false;
            if (p.nxts[idx] == null) return false;
            p = p.nxts[idx];
        }
        return true;
    }
}
```

```
insert("a") 之后的状态:
root - {end=false, nexts=[node, null, null, ..., null]}
                           |
                           `- {end=true, nexts=null}
```

使用字典, 更加通用

```java
class Trie {
    private class Node {
        boolean end;
        Map<Character, Node> nxts;
        Node() { end = false; nxts = null; }
    }
    private Node root;
    
    public Trie() { root = new Node(); }
    
    public void insert(String word) {
        Node p = root;
        for (char ch : word.toCharArray()) {
            if (p.nxts == null) p.nxts = new HashMap<>();
            if (!p.nxts.containsKey(ch)) p.nxts.put(ch, new Node());
            p = p.nxts.get(ch);
        }
        p.end = true;
    }
    
    public boolean search(String word) {
        Node p = root;
        for (char ch : word.toCharArray()) {
            if (p.nxts == null) return false;
            if (!p.nxts.containsKey(ch)) return false;
            p = p.nxts.get(ch);
        }
        return p.end;
    }
    
    public boolean startsWith(String prefix) {
        Node p = root;
        for (char ch : prefix.toCharArray()) {
            if (p.nxts == null) return false;
            if (!p.nxts.containsKey(ch)) return false;
            p = p.nxts.get(ch);
        }
        return true;
    }
}
```

```
insert("a") 之后的状态:
root - {end=false, nexts={a:node}}
                             |
                             `- {end=true, nexts=null}
```



## [209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/) [Array]



## [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) [Graph]





## [211. Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/)

参考 208

```java
class WordDictionary {
    private class Node {
        boolean end;
        Node[] nxts;
        Node() { end = false; nxts = null; }
    }
    private Node root;

    public WordDictionary() { root = new Node(); }
    
    public void addWord(String word) {
        Node p = root;
        for (char ch : word.toCharArray()) {
            int idx = (int)(ch - 'a');
            if (p.nxts == null) p.nxts = new Node[26];
            if (p.nxts[idx] == null) p.nxts[idx] = new Node();
            p = p.nxts[idx];
        }
        p.end = true;
    }
    
    public boolean search(String word) {
        Node p = root;
        return dfs(word, 0, p);
    }

    private boolean dfs(String word, int th, Node p) {
        if (p == null) return false;
        if (th == word.length()) return p.end;
        char ch = word.charAt(th);
        if (ch == '.') {
            if (p.nxts == null) return false;
            for (Node nxt : p.nxts) if (dfs(word, th + 1, nxt)) return true;
            return false;
        } else {
            int idx = (int)(ch - 'a');
            if (p.nxts == null || p.nxts[idx] == null) return false;
            return dfs(word, th + 1, p.nxts[idx]);
        }
    }
}
```

## 212



## [213. House Robber II](https://leetcode.com/problems/house-robber-ii/) [DP]



## [215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)

使用内置 PriorityQueue

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        for (int num : nums) {
            pq.offer(num);
            if (pq.size() > k) pq.poll();
        }
        return pq.peek();
    }
}
```

自己 DIY 大小顶推

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        MinHeap q = new MinHeap();
        for (int num : nums) {
            q.offer(num);
            if (q.size() > k) q.poll();
        }
        return q.peek();
    }
}

class MinHeap {
    private List<Integer> heap;
    private void heapUp(int idx) { // 添加数据时, 让底部元素上浮
        if (idx <= 0) return; // 根节点不需要上浮
        int pidx = (idx - 1) / 2;
        if (heap.get(idx) < heap.get(pidx)) {
            swap(idx, pidx);
            heapUp(pidx);
        }
    }
    private void heapDown(int idx) { // 删除数据时, 让顶部元素下沉
        int lidx = idx * 2 + 1;
        int ridx = idx * 2 + 2;
        if (lidx >= heap.size()) return; // 无子节点
        int smallerChildIdx = lidx;
        if (ridx < heap.size() && heap.get(ridx) < heap.get(lidx)) smallerChildIdx = ridx;
        if (heap.get(idx) > heap.get(smallerChildIdx)) {
            swap(idx, smallerChildIdx);
            heapDown(smallerChildIdx);
        }
        
    }
    private void swap(int i, int j) {
        int tmp = heap.get(i);
        heap.set(i, heap.get(j));
        heap.set(j, tmp);
    }

    public MinHeap() { heap = new ArrayList<>(); }

    public void offer(int val) {
        heap.addLast(val);
        heapUp(heap.size() - 1);
    }

    public int poll() { // 删除堆顶 (0 处) 元素
        if (heap.size() == 0) return -1;
        int ret = heap.get(0);
        if (heap.size() == 1) heap.removeLast();
        else { 
            heap.set(0, heap.removeLast());
            heapDown(0);
        }
        return ret;
    }

    public int peek() {
        if (heap.isEmpty()) return -1;
        return heap.get(0);
    }

    public int size() { return heap.size(); }
}
```

## [216. Combination Sum III](https://LeetCode.com/problems/combination-sum-iii/) [Backtrack]



## [217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> rec = new HashSet<>();
        for (int num : nums) {
            if (rec.contains(num)) return true;
            rec.add(num);
        }
        return false;
    }
}
```



## 218



## [219. Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/)

**滑动窗口** 类似使用单调队列计算每个窗口里的最大值那道题

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Set<Integer> rec = new HashSet<>(); // 用来保存 k 范围内的数字
        for (int i = 0; i < nums.length; i ++) {
            if (i > k && i - k - 1 >= 0) rec.remove(nums[i - k - 1]);
            if (rec.contains(nums[i])) return true;
            rec.add(nums[i]);
        }
        return false;
    }
}
```

**哈希**

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Map<Integer, Integer> rec = new HashMap<>();
        for (int i = 0; i < nums.length; i ++) {
            if (rec.containsKey(nums[i]) && i - rec.get(nums[i]) <= k) return true;
            rec.put(nums[i], i);
        }
        return false;
    }
}
```



## [220. Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/)

**滑动窗口 + TreeSet**

```java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int indexDiff, int valueDiff) {
        TreeSet<Integer> set = new TreeSet<>();
        for (int i = 0; i < nums.length; i ++) {
            if (i > indexDiff && i - indexDiff - 1 >= 0) set.remove(nums[i - indexDiff - 1]); // 移除不在滑动窗口范围的旧元素
            Integer ceil = set.ceiling(nums[i] - valueDiff); // 找到大于等于 nums[i] - valueDiff 的最小值
            if (ceil != null && ceil <= nums[i] + valueDiff) return true; // 检查滑动窗口中是否有与 nums[i] 距离不超过 valueDiff 的元素
            set.add(nums[i]);
        }
        return false;
    }
}
```

📚 **TreeSet 的主要功能**

| **方法**            | **描述**                      | **时间复杂度** |
| ------------------- | ----------------------------- | -------------- |
| **`add(E e)`**      | 向 `TreeSet` 中添加元素 e     | **O(log n)**   |
| **`remove(E e)`**   | 从 `TreeSet` 中移除元素 e     | **O(log n)**   |
| **`contains(E e)`** | 检查 TreeSet 是否包含 e       | **O(log n)**   |
| **`first()`**       | 返回集合中的最小元素          | **O(log n)**   |
| **`last()`**        | 返回集合中的最大元素          | **O(log n)**   |
| **`ceiling(E e)`**  | 返回**大于等于** e 的最小元素 | **O(log n)**   |
| **`floor(E e)`**    | 返回**小于等于** e 的最大元素 | **O(log n)**   |
| **`higher(E e)`**   | 返回**大于** e 的最小元素     | **O(log n)**   |
| **`lower(E e)`**    | 返回**小于** e 的最大元素     | **O(log n)**   |



## [221. Maximal Square](https://leetcode.com/problems/maximal-square/)

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        int nr = matrix.length, nc = matrix[0].length;
        int[][] dp = new int[nr][nc];
        int ans = 0;
        for (int i = 0; i < nr; i ++) {
            for (int j = 0; j < nc; j ++) {
                if (i == 0 || j == 0) dp[i][j] = matrix[i][j] == '0' ? 0 : 1;
                else {
                    if (matrix[i][j] == '0') dp[i][j] = 0;
                    else {
                        dp[i][j] = 1;
                        int offset = Math.min(dp[i-1][j], dp[i][j-1]);
                        if (dp[i - offset][j - offset] > 0) dp[i][j] += offset;
                        else dp[i][j] += offset - 1;
                    }
                }
                ans = Math.max(ans, dp[i][j] * dp[i][j]);
            }
        }
        return ans;
    }
}

```

```
e.g.
mat = 
0 1 1
1 1 1
1 1 1

在 [2][2] 处时, l = 2, 如果 dp[0][0] > 0 <=> mat[0][0] = 1, 此时 dp[2][2] = l + 1 = 3
若 dp[0][0] = 0 <=> mat[0][0] = 0, 此时 dp[2][2] = 2

dp = 
0 1 1
1 1 2
1 2 2
```

简化版

```java
class Solution {
    private int min(int... nums) {
        int min = Integer.MAX_VALUE;
        for (int num : nums) min = Math.min(min, num);
        return min;
    }
    
    public int maximalSquare(char[][] matrix) {
        int maxSide = 0;
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return maxSide;
        }
        int rows = matrix.length, columns = matrix[0].length;
        int[][] dp = new int[rows][columns];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = min(dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1]) + 1;
                    }
                    maxSide = Math.max(maxSide, dp[i][j]);
                }
            }
        }
        int maxSquare = maxSide * maxSide;
        return maxSquare;
    }
}
```



## [222. Count Complete Tree Nodes](https://leetcode.com/problems/count-complete-tree-nodes/) [Tree]



## [223. Rectangle Area](https://leetcode.com/problems/rectangle-area/)

```java
class Solution {
    public int computeArea(int ax1, int ay1, int ax2, int ay2, int bx1, int by1, int bx2, int by2) {
        int overlapX = overlap(ax1, ax2, bx1, bx2);
        int overlapY = overlap(ay1, ay2, by1, by2);
        return (ax2 - ax1) * (ay2 - ay1) + (bx2 - bx1) * (by2 - by1) - overlapX * overlapY;
    }
    private int overlap(int a1, int a2, int b1, int b2) {
        if (a1 > a2) { int t = a1; a1 = a2; a2 = t; }
        if (b1 > b2) { int t = b1; b1 = b2; b2 = t; }
        if (a1 > b1) { int t = b1; b1 = a1; a1 = t; t = b2; b2 = a2; a2 = t; }
        if (a2 <= b1) return 0;
        return Math.min(a2, b2) - b1;
    }
}
```



## [225. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/) [Stack-Queue]



## [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/) [Tree]



## [227. Basic Calculator II](https://leetcode.com/problems/basic-calculator-ii/)

```java
class Solution {
    public int calculate(String s) {
        List<String> ss = splitStr(s);
        List<String> stk = new ArrayList<>();
        for (String str : ss) {
            if (stk.isEmpty() || isOpt(str.charAt(0))) stk.addLast(str);
            else if (stk.getLast().equals("*") || stk.getLast().equals("/")) {
                String sig = stk.removeLast();
                int num1 = Integer.parseInt(stk.removeLast());
                int num2 = Integer.parseInt(str);
                if (sig.equals("*")) stk.addLast(String.valueOf(num1 * num2));
                else stk.addLast(String.valueOf(num1 / num2));
            } else if (stk.getLast().equals("-")) {
                stk.removeLast();
                stk.addLast("-" + str);
            } else {
                stk.removeLast();
                stk.addLast(str);
            }
        }
        int ans = 0;
        for (String num : stk) ans += Integer.parseInt(num);
        return ans;
    }
    private boolean isOpt(char ch) {
        return ch == '+' || ch == '-' || ch == '*' || ch == '/';
    }
    private List<String> splitStr(String s) {
        List<String> ss = new ArrayList<>();
        int i = 0, j = 0;
        while (j < s.length()) {
            if (isOpt(s.charAt(j))) {
                String num = s.substring(i, j).trim();
                ss.addLast(num);
                String opt = String.valueOf(s.charAt(j));
                ss.addLast(opt);
                j ++;
                i = j;
            } else {
                j ++;
            }
        }
        ss.addLast(s.substring(i, j).trim());
        return ss;
    }
}
```



## [228. Summary Ranges](https://leetcode.com/problems/summary-ranges/)

```java
class Solution {
    public List<String> summaryRanges(int[] nums) {
        List<String> ans = new ArrayList<>();
        if (nums.length == 0) return ans;
        int val = nums[0];
        int i = 0, j = 0;
        while (j < nums.length) {
            if (nums[j] != val) {
                if (i != j - 1) ans.add(nums[i] + "->" + nums[j-1]);
                else ans.add(nums[i] + "");
                val = nums[j];
                i = j;
            }
            val ++;
            j ++;
        }
        if (i != j - 1) ans.add(nums[i] + "->" + nums[j-1]);
        else ans.add(nums[i] + "");
        return ans;
    }
}
```



## [229. Majority Element II](https://leetcode.com/problems/majority-element-ii/)

参考[169. Majority Element](https://leetcode.com/problems/majority-element/)**莫尔投票**

```java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        int maj1 = 0, vote1 = 0, maj2 = 0, vote2 = 0;
        for (int num : nums) {
            if (vote1 > 0 && num == maj1) vote1 ++;//如果该元素为第一个元素，则计数加1
            else if (vote2 > 0 && num == maj2) vote2 ++;//如果该元素为第二个元素，则计数加1
            else if (vote1 == 0) {// 选择第一个元素
                maj1 = num;
                vote1 ++;
            } else if (vote2 == 0) {// 选择第二个元素
                maj2 = num;
                vote2 ++;
            } else {//如果三个元素均不相同，则相互抵消1次
                vote1 --;
                vote2 --;
            }
        }
        int cnt1 = 0;
        int cnt2 = 0;
        for (int num : nums) {
            if (vote1 > 0 && num == maj1) cnt1 ++;
            if (vote2 > 0 && num == maj2) cnt2 ++;
        }
        List<Integer> ans = new ArrayList<>();
        if (vote1 > 0 && cnt1 > nums.length / 3) ans.add(maj1);
        if (vote2 > 0 && cnt2 > nums.length / 3) ans.add(maj2);
        return ans;
    }
}
```

**哈希法**

```java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        int targetFreq = nums.length / 3;
        Set<Integer> ans = new HashSet<>();
        Map<Integer, Integer> rec = new HashMap<>();
        for (int num : nums) {
            rec.put(num, rec.getOrDefault(num, 0) + 1);
            if (rec.get(num) > targetFreq) ans.add(num);
        }
        List<Integer> res = new ArrayList<>(ans);
        return res;
    }
}
```



## [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

```c++
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        int ans = 0;
        int steps = 0;
        traversal(root, &steps, k, &ans);
        return ans;
    }
private:
    void traversal(TreeNode* root, int* steps, int k, int* ans) {
        if (root == nullptr) return;
        traversal(root->left, steps, k, ans);
        (*steps) ++; // * 优先级极低, 一定要打括号
        if ((*steps) == k) {
            *ans = root->val;
            return;
        }
        traversal(root->right, steps, k, ans);
    }
};
```



```java
class Solution {
    private int th = 0;
    public int kthSmallest(TreeNode root, int k) {
        if (root == null) return -1;
        if (root.left != null) {
            int leftAns = kthSmallest(root.left, k);
            if (leftAns != -1) return leftAns;
        }
        th ++;
        if (th == k) return root.val;
        if (root.right != null) {
            int rightAns = kthSmallest(root.right, k);
            if (rightAns != -1) return rightAns;
        }
        return -1;
    }
}
```

错误解法: 错误的原因是每一个结点单独存了一个 k, 它无法做到全局修改

```java
class Solution {
    public int kthSmallest(TreeNode root, int k) {
        if (root == null) return -1;
        if (root.left != null) {
            int leftAns = kthSmallest(root.left, k);
            if (leftAns != -1) return leftAns;
        }
        k --;
        if (k == 0) return root.val;
        if (root.right != null) {
            int rightAns = kthSmallest(root.right, k);
            if (rightAns != -1) return rightAns;
        }
        return -1;
    }
}
```



## [231. Power of Two](https://leetcode.com/problems/power-of-two/)

#### Java

```java
class Solution {
    public boolean isPowerOfTwo(int n) {
        if (n == 0) return false;
        while (n > 1 || n < -1) {
            if (n % 2 != 0) return false;
            n /= 2;
        }
        return n == 1;
    }
}
```

*Bitwise*

#### C++

```c++
class Solution {
public:
    bool isPowerOfTwo(int n) { return n > 0 && (n & (n - 1)) == 0; }
};
```

```
n & (n - 1) -> 移除最低位的 1

n = bbb1000
n - 1 = bbb0111
n & (n - 1) = bbb0000 =?= 0000000

若 n 是 2 的幂, 则 n 必形如 10000...(前面一个1, 后面全是 0)
```

---

```c++
class Solution {
public:
    bool isPowerOfTwo(int n) { return n > 0 && (n & -n) == n; }
};
```

```
n & (-n)

n = bbb1000
-n = ppp1000 (p 表示 b 取反)
n & (-n) = 0001000 =?= bbb1000

若 n 是 2 的幂, 则 n 必形如 10000...(前面一个1, 后面全是 0)
```



## [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/) [Stack-Queue]



## 233



## [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/) [List]



## [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/) [Tree]



## [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) [Tree]



## [237. Delete Node in a Linked List](https://leetcode.com/problems/delete-node-in-a-linked-list/)

要删除当前结点, 找不到 pre, 故使用 val 覆盖

```java
class Solution {
    public void deleteNode(ListNode node) {
        if (node == null) return;
        ListNode front = node.next, back = node;
        while (front != null && front.next != null) {
            back.val = front.val;
            back = back.next;
            front = front.next;
        }
        back.val = front.val;
        back.next = null;
    }
}
```

**更简单**的做法

```c++
class Solution {
public:
    void deleteNode(ListNode* node) {
        if (node == nullptr) return;
        ListNode *del = node->next;
        node->val = del->val;
        node->next = del->next;
        delete del;
    }
};
```



## [238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int prod = 1;
        int zeroIdx = -1;
        int[] ans = new int[nums.length];
        for (int i = 0; i < nums.length; i ++) {
            if (nums[i] == 0) {
                if (zeroIdx == -1) zeroIdx = i;
                else return ans;
            } else prod *= nums[i];
        }
        if (zeroIdx != -1) {
            ans[zeroIdx] = prod;
            return ans;
        }
        for (int i = 0; i < nums.length; i ++) ans[i] = prod / nums[i];
        return ans;
    }
}
```



## [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) [Stack-Queue]



## [240. Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)

```java
/*
(si, sj) --- (si, mj) --- (si, ej)
    |    ---     |    ---     |
(mi, sj) --- (mi, mj) --- (mi, ej)
    |    ---     |    ---     |
(ei, sj) --- (ei, mj) --- (ei, ej)
*/
class Solution {
    private int[][]mat;
    private int t;
    private boolean search(int si, int sj, int ei, int ej) {
        if (mat[si][sj] == t) return true;
        if (mat[ei][ej] == t) return true;
        if (mat[ei][sj] == t) return true;
        if (mat[si][ej] == t) return true;

        if (si + 1 >= ei && sj + 1 >= ej) return false;
        
        int mi = (si+ei)/2, mj = (sj+ej)/2;  // 分田字格
        if (mat[mi][mj] == t) return true;
        if (mat[si][mj] == t) return true;
        if (mat[mi][ej] == t) return true;
        if (mat[mi][sj] == t) return true;
        if (mat[ei][mj] == t) return true;
        
        if (mat[si][sj] < t && t < mat[mi][mj] && search(si, sj, mi, mj)) return true;
        else if (mat[si][mj] < t && t < mat[mi][ej] && search(si, mj, mi, ej)) return true;
        else if (mat[mi][sj] < t && t < mat[ei][mj] && search(mi, sj, ei, mj)) return true;
        else if (mat[mi][mj] < t && t < mat[ei][ej] && search(mi, mj, ei, ej)) return true;
        /*
        && search(si, sj, mi, mj) 不能少
        例如
        [[1,2,3,4,5],
         [6,7,8,9,10],
         [11,12,13,14,15],
         [16,17,18,19,20],
         [21,22,23,24,25]]
        如果只写
        if (mat[si][sj] < t && t < mat[mi][mj]) return search(...) 就会出错
        */
        return false; 
    }
    
    public boolean searchMatrix(int[][] matrix, int target) {
        mat = matrix;
        t = target;
        int m = mat.length;
        int n = mat[0].length;
        return search(0, 0, m-1, n-1);
    }
}
```

事实上以上写法意义不大且容易出错, 不如对每一行搞一个binary search



## [241. Different Ways to Add Parentheses](https://leetcode.com/problems/different-ways-to-add-parentheses/)

```java
class Solution {
    public List<Integer> diffWaysToCompute(String expression) {
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < expression.length(); ++i) {
            char oper = expression.charAt(i);
            if (oper == '+' || oper == '-' || oper == '*') {
                List<Integer> s1 = diffWaysToCompute(expression.substring(0, i)); // 返回了前半部分所有可能的结果
                List<Integer> s2 = diffWaysToCompute(expression.substring(i + 1)); // 后半部分所有可能的结果
                for (int a : s1) {
                    for (int b : s2) {
                        if (oper == '+') res.add(a + b);
                        else if (oper == '-') res.add(a - b);
                        else if (oper == '*') res.add(a * b);
                    }
                }
            }
        }
        if (res.isEmpty()) res.add(Integer.parseInt(expression));
        return res;
    }
}
```

**加入记忆优化**

```java
class Solution {
    public List<Integer> diffWaysToCompute(String expression) {
        return calculate(expression, new HashMap<String, List<Integer>>());
    }
    private List<Integer> calculate(String expr, Map<String, List<Integer>> rec) {
        if (rec.containsKey(expr)) return rec.get(expr);
        List<Integer> ret = new ArrayList<>();
        try {
            int val = Integer.parseInt(expr);
            ret.add(val);
        } catch(Exception e) {
            for (int i = 0; i < expr.length(); i ++) {
                char opt = expr.charAt(i);
                if (opt == '+' || opt == '-' || opt == '*') {
                    String expr1 = expr.substring(0, i);
                    String expr2 = expr.substring(i+1);
                    List<Integer> rets1 = calculate(expr1, rec);
                    List<Integer> rets2 = calculate(expr2, rec);
                    for (int val1 : rets1) {
                        for (int val2 : rets2) {
                            int val;
                            switch (opt) {
                            case '+' :
                                val = val1 + val2;
                                break;
                            case '-' : 
                                val = val1 - val2;
                                break;
                            default:
                                val = val1 * val2;
                            }
                            ret.add(val);
                        }
                    }
                }
            }
        }
        rec.put(expr, ret);
        return ret;
    }
}
```



## [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/) [Hash]



## 243 - 250 ...