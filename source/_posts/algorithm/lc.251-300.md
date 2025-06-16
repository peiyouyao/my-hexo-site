---
title: LeetCode 251-300
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

---

## 251 - 256 ...



## [257. Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/) [Tree]



## [258. Add Digits](https://leetcode.com/problems/add-digits/)

```rust
impl Solution {
    pub fn add_digits(mut num: i32) -> i32 {
        if num < 10 {
            return num;
        }
        let mut sum = 0;
        while num > 0 {
            sum += num % 10;
            num /= 10;
        }
        return Solution::add_digits(sum);
    }
}
```



## 259 ...



## [260. Single Number III](https://leetcode.com/problems/single-number-iii/)

```java
class Solution {
    public int[] singleNumber(int[] nums) {
        /*
        把所有的元素进行异或操作，最终得到一个异或值 xorsum。
        设要求找的两个不同的数字为 a, b
        则 a xor b = xorsum
        */
        int xorsum = 0;
        for (int num : nums) xorsum ^= num;
        /*
        取异或值最后一个二进制位为 1 的数字作为 mask，
        如果是 1 则表示两个数字在这一位上不同。
        例如: 
        xorsum = . . . 1 1 1 1
        mask =   . . . 1 0 0 0
        */
        int mask = (xorsum == Integer.MIN_VALUE ? xorsum : xorsum & (-xorsum));
        /*
        通过与这个 mask 进行与操作，
        如果为 0 的分为一个数组，
        为 1 的分为另一个数组。
        这样就把问题降低成了：
        “有一个数组每个数字都出现两次，
        有一个数字只出现了一次，
        求出该数字”。
        对这两个子问题分别进行全异或就可以得到两个解。
        也就是最终的数组了。
        */
        int[] ans = new int[2];
        for (int num : nums) {
            if ((num&mask) == 0) ans[0] ^= num;            
            else ans[1] ^= num;
        }
        return ans;
    }
}
```

以下算法更好理解

```java
class Solution {
    public int[] singleNumber(int[] nums) {
        int xorsum = 0;
        for (int num : nums) xorsum ^= num;
        int mask = 0;
        for (int i = 31; i > -1; i --) {
            if ((xorsum & (1 << i)) != 0) { // 关键
                mask = (1 << i);
                break;
            }
        }
        int[] ans = new int[2];
        for (int num : nums) {
            if ((num & mask) == 0) ans[0] ^= num;
            else ans[1] ^= num;
        }
        return ans;
    }
}
```





## 261 ...



## 262



## [263. Ugly Number](https://leetcode.com/problems/ugly-number/)

```java
class Solution {
    public boolean isUgly(int n) {
        if (n <= 0) return false;
        while (n > 1 && (n&1) == 0) { n >>>= 1; }
        while (n > 1 && n % 3 == 0) { n /= 3; }
        while (n > 1 && n % 5 == 0) { n /= 5; }
        return n == 1;
    }
}
```



## [264. Ugly Number II](https://leetcode.com/problems/ugly-number-ii/)

*DP*

```java
class Solution {
    public int nthUglyNumber(int n) {
        int[] dp = new int[n];
        int p2 = 0, p3 = 0, p5 = 0; //p2指向下一次乘以2的丑数的索引
        dp[0] = 1;
        for (int i = 1; i < n; i ++) {
            dp[i] = min(dp[p2] * 2, dp[p3] * 3, dp[p5] * 5);
            if (dp[i] == dp[p2] * 2) p2 ++; // 以下不能写 else if
            if (dp[i] == dp[p3] * 3) p3 ++; // 不能写成 p3 = i
            if (dp[i] == dp[p5] * 5) p5 ++;
        }
        return dp[n-1];
    }
    private int min(int... nums) {
        int ret = Integer.MAX_VALUE;
        for (int num : nums) ret = Math.min(ret, num);
        return ret;
    }
}
```

*Heap*

```java
class Solution {
    public int nthUglyNumber(int n) {
        int[] factors = {2, 3, 5};
        Set<Long> seen = new HashSet<>();
        Queue<Long> heap = new PriorityQueue<>();
        seen.add(1L);
        heap.offer(1L);
        long ugly = 0L;
        for (int i = 0; i < n; i ++) {
            ugly = heap.poll(); // 每次拿到heap中最小的 ugly number
            for (int f : factors) if (seen.add(ugly * f)) heap.offer(ugly * f); // 使用集合确保不要重复入堆
        }
        return (int)ugly;
    }
}
```



## 265 - 267 ...



## [268. Missing Number](https://leetcode.com/problems/missing-number/)

```java
class Solution {
    public int missingNumber(int[] nums) {
        int num = 0;
        for (int i = 0; i < nums.length + 1; i ++) {
            if (i < nums.length) num = num + i - nums[i];
            else num += i;
        }
        return num;
    }
}
```



## 269 - 272 ...



## 273



## [274. H-Index](https://leetcode.com/problems/h-index/)

```java
class Solution {
    public int hIndex(int[] citations) {
        Arrays.sort(citations); // 这里要是可以从大到小排序会更好, 但因为是int[] 不方便使用Comparator
        int h = 0;
        for (int i = citations.length - 1; i > -1; i --) {
            if (citations[i] >= citations.length - i) h = citations.length - i;
            else break;
        }
        return h;
    }
}
```



## [275. H-Index II](https://leetcode.com/problems/h-index-ii/)

```java
class Solution {
    public int hIndex(int[] citations) {
        int h = 0;
        for (int i = citations.length - 1; i > -1; i --) {
            if (citations[i] >= citations.length - i) h = citations.length - i;
            else break;
        }
        return h;
    }
}
```



## 276 - 277 ...



## [278. First Bad Version](https://leetcode.com/problems/first-bad-version/)

```java
/* The isBadVersion API is defined in the parent class VersionControl.
      boolean isBadVersion(int version); */

public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int l = 0, r = n;
        while (l <= r) {
            int m = r - (r - l) / 2;
            if (isBadVersion(m)) r = m - 1;
            else l = m + 1;
        }
        return l;
    }
}
```



## [279. Perfect Squares](https://leetcode.com/problems/perfect-squares/) [DP-Knapsack]



## 280 - 281 ...



## 282



## [283. Move Zeroes](https://leetcode.com/problems/move-zeroes/) [Array]



## [284. Peeking Iterator](https://leetcode.com/problems/peeking-iterator/)

```java
// Java Iterator interface reference:
// https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html

class PeekingIterator implements Iterator<Integer> {
    private Iterator<Integer> iter;
    private Integer nextEle;

	public PeekingIterator(Iterator<Integer> iterator) {
	    // initialize any member here.
	    iter = iterator;
        nextEle = iter.next();
	}
	
    // Returns the next element in the iteration without advancing the iterator.
	public Integer peek() {
        return nextEle;
	}
	
	// hasNext() and next() should behave the same as in the Iterator interface.
	// Override them if needed.
	@Override
	public Integer next() {
	    Integer ret = nextEle;
        nextEle = iter.hasNext() ? iter.next() : null;
        return ret;
	}
	
	@Override
	public boolean hasNext() {
	    return nextEle != null;
	}
}
```



## 285 - 286 ...



## [287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

**二分搜索**

```
nums: n + 1个元素 in range [1, n] 有一个元素是重复的
对与 i in [1, n], 考虑 <= i 的元素个数 (cnt[i]), 如果 i >= 重复的元素, 那么 cnt[i] > i 反之 cnt[i] <= i
eg
n = 5
nums = 1 5 3 2 3 4
cnt[1] = 1
cnt[2] = 2
cnt[3] = 4 <<-- 使用二分法找到它
cnt[4] = 5
cnt[5] = 6

n = 3
nums = 3 3 3 3
cnt[1] = 0 <= 1
cnt[2] = 0 <= 2
cnt[3] = 4 > 3 <<-- 找到它
```



```java
class Solution {
    public int findDuplicate(int[] nums) {
        int n = nums.length - 1;
        int l = 1, r = n;
        while (l <= r) {
            int m = r - ((r - l) >> 1);
            int cnt = 0; // 计算 <= m 的个数
            for (int num : nums) if (num <= m) cnt ++;
            if (cnt <= m) l = m + 1; // 搜索区间右移
            else r = m - 1;
        }
        return l;
    }
}
```

**环形链表**

```
nums: 1 5 3 2 3 4
idx:  0 1 2 3 4 5
把 nums[i] 看成下一步要去的地方:
0 -> 1 -> 5 -> 4 -> 3 -> 2 -> 3 -> 2 -> ... 后面就循环了
0 -> 1 -> 5 -> 4 -> 3 <=> 2
```

```java
class Solution {
    private class MutInt { // 把数据放在堆上
        int val;
        MutInt() { val = 0; }
    }
    private void move(int[] nums, MutInt idx) {
        idx.val = nums[idx.val];
    }
    public int findDuplicate(int[] nums) {
        MutInt slow = new MutInt(), fast = new MutInt();
        while (true) {
            move(nums, slow);
            move(nums, fast);
            move(nums, fast);
            if (slow.val == fast.val) break;
        }
        MutInt idx = new MutInt();
        while (idx.val != slow.val) {
            move(nums, idx);
            move(nums, slow);
        }
        return idx.val;
    }
}
```

对于带有指针的语言, 就不需要搞一个 `MutInt` 类了

###### Rust

```rust
impl Solution {
    pub fn find_duplicate(nums: Vec<i32>) -> i32 {
        let next = |idx: &mut i32| { // 只有闭包才可以使用 (捕获) 函数内部的变量 (nums)
            *idx = nums[*idx as usize];
        };
        let mut slow: i32 = 0;
        let mut fast: i32 = 0;
        loop {
            next(&mut slow);
            next(&mut fast);
            next(&mut fast);
            if (slow == fast) { break; }
        }
        let mut idx: i32 = 0;
        while idx != slow {
            next(&mut idx);
            next(&mut slow);
        }
        idx
    }
}
```



## 288 ...



## [289. Game of Life](https://leetcode.com/problems/game-of-life/)

```java
class Solution {
    public void gameOfLife(int[][] board) {
        int m = board.length, n = board[0].length;
        int[][] rec = new int[m][n];
        for (int i = 0; i < m; i ++) {
            for (int j = 0; j < n; j ++) {
                int cnt = 0;
                cnt += i == 0   || j == 0   ? 0 : board[i-1][j-1];
                cnt += i == 0               ? 0 : board[i-1][j  ];
                cnt += i == 0   || j == n-1 ? 0 : board[i-1][j+1];
                cnt +=             j == 0   ? 0 : board[i  ][j-1];
                cnt +=             j == n-1 ? 0 : board[i  ][j+1];
                cnt += i == m-1 || j == 0   ? 0 : board[i+1][j-1];
                cnt += i == m-1             ? 0 : board[i+1][j  ];
                cnt += i == m-1 || j == n-1 ? 0 : board[i+1][j+1];
                rec[i][j] = cnt;
            }
        }

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j ++) {
                if (board[i][j] == 1) {
                    if (rec[i][j] < 2 || rec[i][j] > 3) board[i][j] = 0;
                } else {
                    if (rec[i][j] == 3) board[i][j] = 1;
                }
            }
        }
    }
}
```

###### Rust

```rust
impl Solution {
    pub fn game_of_life(board: &mut Vec<Vec<i32>>) {
        let m: usize = board.len();
        let n: usize = board[0].len();
        let mut rec: Vec<Vec<i32>> = vec![vec![0; n]; m];
        for i in 0..m {
            for j in 0..n {
                let mut cnt: i32 = 0;

                cnt += if i == 0 || j == 0 { 0 } else { board[i - 1][j - 1] };
                cnt += if i == 0 { 0 } else { board[i - 1][j] };
                cnt += if i == 0 || j == n - 1 { 0 } else { board[i - 1][j + 1] };
                cnt += if j == 0 { 0 } else { board[i][j - 1] };
                cnt += if j == n - 1 { 0 } else { board[i][j + 1] };
                cnt += if i == m - 1 || j == 0 { 0 } else { board[i + 1][j - 1] };
                cnt += if i == m - 1 { 0 } else { board[i + 1][j] };
                cnt += if i == m - 1 || j == n - 1 { 0 } else { board[i + 1][j + 1] };

                rec[i][j] = cnt;
            }
        }

        for i in 0..m {
            for j in 0..n {
                if board[i][j] == 1 {
                    if (rec[i][j] < 2 || rec[i][j] > 3) { board[i][j] = 0; }
                } else {
                    if rec[i][j] == 3 { board[i][j] = 1; }
                }
            }
        }
    }
}
```



## [290. Word Pattern](https://leetcode.com/problems/word-pattern/)

```java
class Solution {
    public boolean wordPattern(String pattern, String s) {
        String[] ss = s.split(" ");
        if (pattern.length() != ss.length) return false;
        Map<String, String> map = new HashMap<>();
        for (int i = 0; i < ss.length; i ++) {
            String ch = String.valueOf(pattern.charAt(i));
            String word = ss[i];
            if (map.containsKey(ch) && !word.equals(map.get(ch))) return false;
            else map.put(ch, word);
        }
        map.clear();
        for (int i = 0; i < ss.length; i ++) {
            String ch = String.valueOf(pattern.charAt(i));
            String word = ss[i];
            if (map.containsKey(word) && !ch.equals(map.get(word))) return false;
            else map.put(word, ch);
        }
        return true;
    }
}
```

###### Rust

```rust
use std::collections::HashMap;
impl Solution {
    pub fn word_pattern(pattern: String, s: String) -> bool {
        let words: Vec<&str> = s.split_whitespace().collect();
        if pattern.len() != words.len() {
            return false;
        }
        let mut p2w: HashMap<char, &str> = HashMap::new();
        let mut w2p: HashMap<&str, char> = HashMap::new();
        for (_, (ch, word)) in pattern.chars().zip(words.iter()).enumerate() {
            if p2w.contains_key(&ch) && p2w[&ch] != *word {
                return false;
            }
            if w2p.contains_key(word) && w2p[word] != ch {
                return false;
            }
            p2w.insert(ch, word);
            w2p.insert(word, ch);
        }
        true
    }
}
```



## 291 ...



## [292. Nim Game](https://leetcode.com/problems/nim-game/)

```java
class Solution {
    public boolean canWinNim(int n) { return n % 4 != 0; }
}
```

本质上是一个dp, 满足

```
dp[i] = !(dp[i-1] && dp[i-2] && dp[i-3])
```

###### Rust

```rust
impl Solution {
    pub fn can_win_nim(n: i32) -> bool {
        n % 4 != 0
    }
}
```



## 293 - 294 ...



## 295





## [297. Serialize and Deserialize Binary Tree](https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/)

```java
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        List<String> lst = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            TreeNode curr = q.poll();
            if (curr == null) {
                lst.add("null");
            } else {
                lst.add(String.valueOf(curr.val));
                q.offer(curr.left);
                q.offer(curr.right);
            }
        }
        while (!lst.isEmpty() && lst.getLast().equals("null")) lst.removeLast();
        return lst.stream().collect(Collectors.joining(",", "[", "]"));
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        data = data.substring(1, data.length() - 1);
        String[] vals = data.split(",");
        byte[] st = new byte[vals.length]; // 0 左右均未确定; 1 确定了左; 2 确定了左右
        TreeNode[] nodes = Arrays.stream(vals).map(v -> v.equals("null") || v.equals("") ? null : new TreeNode(Integer.parseInt(v))).toArray(TreeNode[]::new);
        int start = 0;
        for (int i = 1; i < nodes.length; i ++) {
            int pId = Math.max(((i - 1) >> 1), start); // 确定当前节点的父节点
            while (pId < nodes.length && (nodes[pId] == null || st[pId] == (byte)2)) pId ++;
            if (pId == nodes.length) break;
            start = pId;
            
            if (st[pId] == (byte)0) {
                nodes[pId].left = nodes[i];
                st[pId] = (byte)1;
            } else {
                nodes[pId].right = nodes[i];
                st[pId] = (byte)2;
            }
        }
        return nodes[0];
    }
}
```



使用队列优化

```java
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        List<String> lst = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            TreeNode curr = q.poll();
            if (curr == null) {
                lst.add("null");
            } else {
                lst.add(String.valueOf(curr.val));
                q.offer(curr.left);
                q.offer(curr.right);
            }
        }
        while (!lst.isEmpty() && lst.getLast().equals("null")) lst.removeLast();
        return lst.stream().collect(Collectors.joining(",", "[", "]"));
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        data = data.substring(1, data.length() - 1);
        String[] vals = data.split(",");
        if (vals.length == 0 || vals[0].equals("") || vals[0].equals("null")) return null;
        Queue<TreeNode> q = new LinkedList<>();
        TreeNode root = new TreeNode(Integer.parseInt(vals[0]));
        q.offer(root);
        int idx = 1;
        while (!q.isEmpty()) {
            TreeNode curr = q.poll(); // 每次把curr作为父节点, 为它添加左右节点
            TreeNode left = null;
            if (idx < vals.length && !vals[idx].equals("") && !vals[idx].equals("null")) {
                left = new TreeNode(Integer.parseInt(vals[idx]));
                q.offer(left);
            }
            idx ++;
            TreeNode right = null;
            if (idx < vals.length && !vals[idx].equals("") && !vals[idx].equals("null")) {
                right = new TreeNode(Integer.parseInt(vals[idx]));
                q.offer(right);
            }
            idx ++;

            curr.left = left;
            curr.right = right;
        }
        return root;
    }
}
```



## 298 ...



## [299. Bulls and Cows](https://leetcode.com/problems/bulls-and-cows/)

```java
class Solution {
    public String getHint(String secret, String guess) {
        int n = secret.length();
        boolean[] correct = new boolean[n];
        int[] rec = new int[10];
        int A = 0, B = 0;
        for (int i = 0; i < n; i ++) {
            char sch = secret.charAt(i), gch = guess.charAt(i);
            if (sch == gch) {
                correct[i] = true;
                A ++;
            } else {
                rec[toNum(sch)] ++;
            }
        }

        for (int i = 0; i < n; i ++) {
            if (correct[i]) continue;
            char gch = guess.charAt(i);
            int idx = toNum(gch);
            if (rec[idx] > 0) {
                B ++;
                rec[idx] --;
            }
        }
        return A + "A" + B + "B";
    }

    private int toNum(char c) { return (int)(c - '0'); }
}
```



## [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) [DP-Sub]

进阶 [673. Number of Longest Increasing Subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/)
