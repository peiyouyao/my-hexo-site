---
title: LeetCode 1001-1500
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

## [1002. Find Common Characters](https://leetcode.com/problems/find-common-characters/) [Hash]



## [1005. Maximize Sum Of Array After K Negations](https://leetcode.com/problems/maximize-sum-of-array-after-k-negations/) [Greedy]



## [1019. Next Greater Node In Linked List](https://leetcode.cn/problems/next-greater-node-in-linked-list/)

#### C++

```c++
class Solution {
public:
    vector<int> nextLargerNodes(ListNode* head) {
        vector<int> ans;
        vector<pair<int, int>> stk;
        int idx = 0; // 使用 idx 为每一个节点分配一个索引
        while (head) {
            ans.push_back(0);
            while (!stk.empty() && stk.back().second < head->val) {
                ans[stk.back().first] = head->val;
                stk.pop_back();
            }
            stk.push_back({idx, head->val});
            head = head->next;
            idx ++;
        }
        return ans;
    }
};
```



#### Go

```go
func nextLargerNodes(head *ListNode) []int {
    ans := []int{}
    stk := [][]int{}
    idx := 0
    for head != nil {
        ans = append(ans, 0)
        for len(stk) > 0 && stk[ len(stk)-1 ][1] < head.Val {
            ans[ stk[ len(stk)-1 ][0] ] = head.Val
            stk = stk[: len(stk)-1 ]
        }
        stk = append(stk, []int{idx, head.Val})
        head = head.Next
        idx ++
    }
    return ans
}
```



## [1022. Sum of Root To Leaf Binary Numbers](https://leetcode.com/problems/sum-of-root-to-leaf-binary-numbers/)

#### Go

```go
func sumRootToLeaf(root *TreeNode) int {
    sum := 0
    var dfs func(*TreeNode, int)
    dfs = func(root *TreeNode, path int) {
        if root == nil {
            return
        }
        path <<= 1
        path |= root.Val
        if root.Left == nil && root.Right == nil {
            sum += path
        } else {
            dfs(root.Left, path)
            dfs(root.Right, path)
        }
    }
    dfs(root, 0)
    return sum
}
```



## [1026. Maximum Difference Between Node and Ancestor](https://leetcode.cn/problems/maximum-difference-between-node-and-ancestor/)

最大差值, 只要维护每个节点到root路径中的最大值和最小值即可

#### Go

自底向上

```go
func maxAncestorDiff(root *TreeNode) int {
    var foo func(*TreeNode, int, int) int
    foo = func(root *TreeNode, pathMax, pathMin int) int {
        if root == nil {
            return pathMax - pathMin
        }
        pathMax = max(pathMax, root.Val)
        pathMin = min(pathMin, root.Val)
        return max(foo(root.Left, pathMax, pathMin), foo(root.Right, pathMax, pathMin))
    }
    return foo(root, root.Val, root.Val)
}
```



自顶向下

```go
func maxAncestorDiff(root *TreeNode) int {
    maxDiff := 0
    var foo func(*TreeNode, int, int)
    foo = func(root *TreeNode, pathMax, pathMin int) {
        if root == nil {
            return
        }
        if root.Val > pathMax {
            maxDiff = max(maxDiff, root.Val - pathMin)
            pathMax = root.Val
        } else if root.Val < pathMin {
            maxDiff = max(maxDiff, pathMax - root.Val)
            pathMin = root.Val
        }
        foo(root.Left, pathMax, pathMin)
        foo(root.Right, pathMax, pathMin)
    }

    foo(root, root.Val, root.Val)
    return maxDiff
}
```



## [1035. Uncrossed Lines](https://leetcode.com/problems/uncrossed-lines/) [DP-Sub]



## [1038. Binary Search Tree to Greater Sum Tree](https://leetcode.cn/problems/binary-search-tree-to-greater-sum-tree/) [Tree]



## [1047. Remove All Adjacent Duplicates In String](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/) [Stack-Queue]



## [1049. Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/) [DP-Knapsack]



## [1080. Insufficient Nodes in Root to Leaf Paths](https://leetcode.cn/problems/insufficient-nodes-in-root-to-leaf-paths/)

#### C++

```c++
class Solution {
    int L;

    void delTree(TreeNode* root) {
        if (!root) return;
        delTree(root->left);
        delTree(root->right);
        delete root;
    }

    int dfs(TreeNode* root, int pathsum) {
        if (!root) return INT_MIN;
        pathsum += root->val; // pathsum 是递下去的
        if (!root->left && !root->right) return pathsum;
        else {
            int l = dfs(root->left, pathsum), r = dfs(root->right, pathsum); // l, r 是归回来的
            if (l < L) {
                // delTree(root->left);
                root->left = nullptr;
            }
            if (r < L) {
                // delTree(root->right);
                root->right = nullptr;
            }
            return max(l, r);
        }
    }
public:
    TreeNode* sufficientSubset(TreeNode* root, int limit) {
        L = limit;
        if (dfs(root, 0) < L) {
            // delTree(root);
            return nullptr;
        }
        return root;
    }
};
```



#### Go

sum记录从root到curr的路径和; curr向上返回**包含curr的最大"树枝和"**(树枝和: 一条从root到叶子节点的树枝)

```go
func sufficientSubset(root *TreeNode, limit int) *TreeNode {
    var dfs func(*TreeNode, int) int
    dfs = func(root *TreeNode, sum int) (int) { // sum 作为局部变量
        if root == nil {
            return math.MinInt // not zore
        }
        sum += root.Val
        if root.Left == nil && root.Right == nil {
            return sum
        } else {
            l := dfs(root.Left, sum)
            r := dfs(root.Right, sum)
            if l < limit {
                root.Left = nil
            }
            if r < limit {
                root.Right = nil
            }
            return max(l, r)
        }
    }
    maxsum := dfs(root, 0)
    if maxsum < limit { return nil }
    return root
}
```



```go
func sufficientSubset(root *TreeNode, limit int) *TreeNode {
    sum := 0 // sum 作为全局变量
    var dfs func(*TreeNode) int
    dfs = func(root *TreeNode) (int) {
        if root == nil {
            return math.MinInt // not zore
        }
        
        if root.Left == nil && root.Right == nil {
            return sum + root.Val
        } else {
            sum += root.Val
            l := dfs(root.Left)
            r := dfs(root.Right)
            if l < limit {
                root.Left = nil
            }
            if r < limit {
                root.Right = nil
            }
            sum -= root.Val
            return max(l, r)
        }
    }
    maxsum := dfs(root)
    if maxsum < limit { return nil }
    return root
}
```



## [1110. Delete Nodes And Return Forest](https://leetcode.cn/problems/delete-nodes-and-return-forest/)

本题的核心是从下往上删除, 每个节点返回给父节点该点是否被删除

#### C++

```C++
class Solution {
    unordered_set<int> delSet;

    vector<TreeNode*> ans;

    bool dfs(TreeNode* root) {
        if (!root) return false;

        if (dfs(root->left)) {
            delete root->left;
            root->left = nullptr;
        }

        if (dfs(root->right)) {
            delete root->right;
            root->right = nullptr;
        }


        if (delSet.find(root->val) != delSet.end()) {
            if (root->left) ans.push_back(root->left);
            root->left = nullptr;
            if (root->right) ans.push_back(root->right);
            root->right = nullptr;
            return true;
        }
        return false;
    }

public:
    vector<TreeNode*> delNodes(TreeNode* root, vector<int>& to_delete) {
        for (int v : to_delete) delSet.insert(v);
        if (dfs(root)) {
            if (root->left) ans.push_back(root->left);
            root->left = nullptr;
            if (root->right) ans.push_back(root->right);
            root->right = nullptr;
            delete root;
        } else ans.push_back(root);
        return ans;
    }
};
```



#### Go

```go
func delNodes(root *TreeNode, to_delete []int) []*TreeNode {
    mp := map[int]bool{}
    for _, v := range to_delete { mp[v] = true }
    ans := []*TreeNode{}
    var dfs func(*TreeNode) bool
    dfs = func(root *TreeNode) bool {
        if root == nil { return false }

        if dfs(root.Left) {
            root.Left = nil
        }

        if dfs(root.Right) {
            root.Right = nil
        }

        if _, ok := mp[root.Val]; ok {
            if root.Left != nil {
                ans = append(ans, root.Left)
                root.Left = nil
            }
            if root.Right != nil {
                ans = append(ans, root.Right)
                root.Right = nil
            }
            return true
        }
        return false
    }
    if dfs(root) {
        if root.Left != nil {
            ans = append(ans, root.Left)
            root.Left = nil
        }
        if root.Right != nil {
            ans = append(ans, root.Right)
            root.Right = nil
        }
    } else {
        ans = append(ans, root)
    }
    return ans
}
```



## [1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/) [DP-Sub]



## [1145. Binary Tree Coloring Game](https://leetcode.cn/problems/binary-tree-coloring-game/)

*Greedy*

对于player2的最优策略, 就是落子在player1 x的父节点, 左孩子, 或右孩子

如果落子在x的父节点: player1最终可以获得以x为根的子树的全部节点数(`dfs(xNode)`), 同时player2最终获得`n - p1`

如果落子在x的左孩子: player2最终可以获得以x.Left为根的子树的全部节点数(`dfs(xNode.Left)`), 同时player1最终获得`n - p2`

如果落子在x的右孩子: ...

#### Go

```go
func btreeGameWinningMove(root *TreeNode, n int, x int) bool {
    xcnt := 0
    xlcnt := 0
    xrcnt := 0
    var dfs func(*TreeNode) int
    dfs = func(root *TreeNode) int {
        if root == nil { return 0 }
        lcnt := dfs(root.Left)
        rcnt := dfs(root.Right)
        if root.Val == x { 
            xcnt = 1 + lcnt + rcnt
            xlcnt = lcnt
            xrcnt = rcnt
        }
        return 1 + lcnt + rcnt
    }
    dfs(root)
    
    p2 := n - xcnt
    p2 = max(p2, xlcnt)
    p2 = max(p2, xrcnt)
    
    p1 := n - p2
    return p2 > p1
}
```



## [1171. Remove Zero Sum Consecutive Nodes from Linked List](https://leetcode.cn/problems/remove-zero-sum-consecutive-nodes-from-linked-list/)

*Prefix Sum + Hash Table*

```
考察数组
1 < 3 2 -3 -2 > 5 < 5 -5 > 1
可以对<>中内容进行删除
考察其对应的前缀和:
0 1 2 3 -3 -2 5 5 -5 1
0 1 3 6  3  1 6 11 6 7
    ^----^
  ^---------^ ^----^
我们发现, 如果, prefixsum[i] == prefixsum[j]
那么只要删除区间 (i, j] 中的元素即可, 于是考虑使用哈希表记录 sum : idx

*** 在删除区间中的元素时, 必须把区间中所有元素对应于哈希表中的键值对删除!!!
```



#### C++

转成数组

```c++
class Solution {
public:
    ListNode* removeZeroSumSublists(ListNode* head) {
        vector<int> nums{0};
        vector<int> prefixsum{0};
        while (head) {
            nums.push_back(head->val);
            prefixsum.push_back(prefixsum.back() + head->val);
            head = head->next;
        }
        unordered_map<int, int> mp;
        for (int i = 0; i < prefixsum.size(); i ++) {
            int sum = prefixsum[i];
            if (mp.find(sum) != mp.end()) { // 删除 (j, i] 中的元素
                int j = mp[sum];
                int jj = j;
                while (j < i) {
                    j ++;
                    nums[j] = 8964; // 标记
                    mp.erase(prefixsum[j]); // 这里会将 (sumjj : jj) 删除!!!! 因为 sumi = sumjj
                }
                mp[sum] = jj; // jj 处的键值对 (sumjj : jj) 是不能删除的, 要防止在while中擦除该键值对
            } else {
                mp[sum] = i;
            }
        }
        ListNode ans{0};
        ListNode* p = &ans;
        for (int i = 1; i < nums.size(); i ++) {
            if (nums[i] != 8964) {
                p->next = new ListNode{nums[i]};
                p = p->next;
            }
        }
        return ans.next;
    }
};
```



直接在链表上操作

```c++
class Solution {
public:
    ListNode* removeZeroSumSublists(ListNode* head) {
        head = new ListNode{0, head};
        unordered_map<ListNode*, int> prefixsum;
        ListNode* p = head;
        int sum = 0;
        while (p) {
            sum += p->val;
            prefixsum[p] = sum;
            p = p->next;
        }
        unordered_map<int, ListNode*> mp;
        p = head;
        while (p) {
            int s = prefixsum[p];
            if (mp.find(s) != mp.end()) { // 删除 (qq, p]
                ListNode* qq = mp[s];
                ListNode* q = qq;
                while (true) {
                    ListNode* del = q->next;
                    mp.erase(prefixsum[del]);
                    q->next = del->next;
                    if (del == p) break;
                }
                mp[s] = qq;
            } else {
                mp[s] = p;
            }
            p = p->next;
        }
        return head->next;
    }
};
```



*Prefix Sum + Hash Table + Greedy*

```
加入贪心思想, 将map prefixsum[sum]=p 设置成为前缀和为 sum 的最远节点 p
```



```c++
class Solution {
public:
    ListNode* removeZeroSumSublists(ListNode* head) {
        head = new ListNode{0, head};
        unordered_map<int, ListNode*> prefixsum;
        int sum = 0;
        for (ListNode* p = head; p; p = p->next) {
            sum += p->val;
            prefixsum[sum] = p;
        }
        sum = 0;
        for (ListNode* p = head; p; p = p->next) {
            sum += p->val;
            p->next = prefixsum[sum]->next;
        }
        return head->next;
    }
};
```



#### Go

```go
func removeZeroSumSublists(head *ListNode) *ListNode {
    head = &ListNode{0, head}
    mp := map[int]*ListNode{}
    sum := 0
    for p := head; p != nil; p = p.Next {
        sum += p.Val
        mp[sum] = p
    }
    sum = 0
    for p := head; p != nil; p = p.Next {
        sum += p.Val
        p.Next = mp[sum].Next
    }
    return head.Next
}
```



## [1207. Unique Number of Occurrences](https://leetcode.com/problems/unique-number-of-occurrences/) [Array]



## [1221. Split a String in Balanced Strings](https://leetcode.com/problems/split-a-string-in-balanced-strings/) [Greedy]



## [1290. Convert Binary Number in a Linked List to Integer](https://leetcode.cn/problems/convert-binary-number-in-a-linked-list-to-integer/)

#### C++

```c++
class Solution {
public:
    int getDecimalValue(ListNode* head) {
        int ans = 0;
        while (head) {
            ans *= 2; // 如果head表示高位, 就这样写; 如果head表示低位, 就搞一个 exp
            ans += head->val;
            head = head->next;
        }
        return ans;
    }
};
```



## [1310. XOR Queries of a Subarray](https://leetcode.com/problems/xor-queries-of-a-subarray/)

**前缀和**

```
注意到:
a[n] ^ ... ^ a[m] = (a[0] ^ ... ^ a[n - 1] ^ a[n] ^ ... ^ a[m]) ^ (a[0] ^ ... ^ a[n - 1])
```

```java
class Solution {
    public int[] xorQueries(int[] arr, int[][] queries) {
        int[] xors = new int[arr.length + 1];
        for (int i = 0; i < arr.length; i ++) xors[i+1] = xors[i] ^ arr[i];
        int[] ans = new int[queries.length];
        for (int i = 0; i < queries.length; i ++) ans[i] = xors[queries[i][0]] ^ xors[queries[i][1] + 1];
        return ans;
    }
}
```



## [1315. Sum of Nodes with Even-Valued Grandparent](https://leetcode.cn/problems/sum-of-nodes-with-even-valued-grandparent/)

#### Go

stand at grandparent, not at grandson

```go
func sumEvenGrandparent(root *TreeNode) int {
    sum := 0
    var foo func(*TreeNode)
    foo = func(root *TreeNode) {
        if root == nil {
            return
        }
        if root.Val & 1 == 0 {
            if root.Left != nil {
                if root.Left.Left != nil {
                    sum += root.Left.Left.Val
                }

                if root.Left.Right != nil {
                    sum += root.Left.Right.Val
                }
            }

            if root.Right != nil {
                if root.Right.Left != nil {
                    sum += root.Right.Left.Val
                }

                if root.Right.Right != nil {
                    sum += root.Right.Right.Val
                }
            }
        }
        foo(root.Left)
        foo(root.Right)
    }
    foo(root)
    return sum
}
```



## [1325. Delete Leaves With a Given Value](https://leetcode.cn/problems/delete-leaves-with-a-given-value/)

#### C++

```c++
class Solution {
public:
    TreeNode* removeLeafNodes(TreeNode* root, int target) {
        if (!root) return nullptr;
        root->left = removeLeafNodes(root->left, target);
        root->right = removeLeafNodes(root->right, target);
        if (root->val == target && !root->left && !root->right) {
            // delete root;
            return nullptr;
        }
        return root;
    }
};
```



## [1339. Maximum Product of Splitted Binary Tree](https://leetcode.com/problems/maximum-product-of-splitted-binary-tree/)

均值不等式: 当$a + b = S, a = S/2, b = S/2, a*b$最大

#### Go

```go
const M int = 1_000_000_007
func abs(x int) int {
    if x < 0 { x *= -1 }
    return x
}
func maxProduct(root *TreeNode) int {
    sum := 0
    act := false

    dist := math.MaxInt
    bestSubsum := 0
    
    var dfs func(*TreeNode) int
    dfs = func(root *TreeNode) int {
        if root == nil { return 0 }
        subsum := root.Val % M + dfs(root.Left) % M + dfs(root.Right) % M
        if act {
            if abs(subsum - sum / 2) < dist {
                dist = abs(subsum - sum / 2)
                bestSubsum = subsum
            }
        }
        return subsum
    }
    
    sum = dfs(root) % M
    act = true
    dfs(root)
    return (bestSubsum * (sum - bestSubsum)) % M
}
```



## [1343. Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold](https://leetcode.com/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/)

#### Go

```go
func numOfSubarrays(arr []int, k int, threshold int) int {
    thresholdSum := k * threshold
    sum := 0
    cnt := 0
    for l, r := 0, 0; r < len(arr); r ++ {
        sum += arr[r]
        if r - l + 1 == k {
            if sum >= thresholdSum {
                cnt ++
            }
            sum -= arr[l]
            l ++
        }
    }
    return cnt
}
```



## [1356. Sort Integers by The Number of 1 Bits](https://leetcode.com/problems/sort-integers-by-the-number-of-1-bits/) [Bitwise]



## [1365. How Many Numbers Are Smaller Than the Current Number](https://leetcode.com/problems/how-many-numbers-are-smaller-than-the-current-number/) [Array]



## [1367. Linked List in Binary Tree](https://leetcode.com/problems/linked-list-in-binary-tree/)

```java
class Solution {
    public boolean isSubPath(ListNode head, TreeNode root) {
        if (head == null) return true;
        if (root == null) return false;
        if (searchAt(head, root)) return true;
        if (isSubPath(head, root.left)) return true;
        if (isSubPath(head, root.right)) return true;
        return false;
    }

    private boolean searchAt(ListNode head, TreeNode root) { // 站在 root 处, 向下搜一波 head
        if (head == null) return true;
        if (root == null) return false;
        if (head.val == root.val) {
            if (searchAt(head.next, root.left)) return true;
            if (searchAt(head.next, root.right)) return true;
            return false;
        } else return false;
    }
}
```



## [1371. Find the Longest Substring Containing Vowels in Even Counts](https://leetcode.com/problems/find-the-longest-substring-containing-vowels-in-even-counts/)

```java
class Solution {
    public int findTheLongestSubstring(String s) {
        int state = 0; // 一共有 32 种状态 (1 << 5)
        int[] pos = new int[1 << 5]; // 记录每一种状态出现的最早位置
        /*
        pos[state] 表示状态 state 出现在子串 s[0 ..< pos[state]] 中
        故 pos[0] = 0 表示状态 00000 出现在子串 s[0..<0] 即空串中
        */
        Arrays.fill(pos, -1); // 一开始状态都没有出现, 置为 -1, 除了 ...
        pos[0] = 0; // 除了 0 状态, 它在 位置 0 处出现
        int ans = 0;
        for (int i = 1; i <= s.length(); i ++) {
            char ch = s.charAt(i - 1);
            switch (ch) {
                case 'a': state ^= (1 << 0); break; // 翻转某一位
                case 'e': state ^= (1 << 1); break;
                case 'i': state ^= (1 << 2); break;
                case 'o': state ^= (1 << 3); break;
                case 'u': state ^= (1 << 4); break;
            }
            if (pos[state] == -1) pos[state] = i;
            else ans = Math.max(ans, i - pos[state]);
        }
        return ans;
    }
}
```



## [1372. Longest ZigZag Path in a Binary Tree](https://leetcode.cn/problems/longest-zigzag-path-in-a-binary-tree/)

#### Go

```go
func longestZigZag(root *TreeNode) int {
    maxLen := 0
    pathLen := 0
    var dfs func(*TreeNode, bool)
    dfs = func(root *TreeNode, flg bool) { // flg 代表当前节点取到的有效方向, flg=false意味着当前节点去到左边是有效的
        if root == nil {
            pathLen = 0
            return
        }
        pathLen ++
        maxLen = max(maxLen, pathLen)
        if !flg {
            dfs(root.Left, !flg)
            pathLen = 1 // 从有效方向回来了, 置pathLen = 1
            dfs(root.Right, flg)
        } else {
            dfs(root.Right, !flg)
            pathLen = 1
            dfs(root.Left, flg)
        }
    }
    dfs(root, false)
    dfs(root, true)
    return maxLen - 1
}
```



*Tree-DP*

自底向上DP

这种写法更加高效, 不需要使用哈希维护

`dfs`的返回值 `dpl, dpr`的含义:

例如 `dpl, dpr = dfs(root)`

`dpl`: `root` 作为"左父亲"时的最长zigzag路径; 

"左父亲": 存在一条以`root`开始, 到某一叶子节点结束的zigzag路径, 且`root.Left`在路径中(或者`root`本身就是根节点). 

同理, `dpr`: `root` 作为"右父亲"时的最长zigzag路径. 



```go
func longestZigZag(root *TreeNode) int {
    ans := 0
    var dfs func(*TreeNode) (int, int)
    dfs = func(root *TreeNode) (dpl, dpr int) {
        dpl, dpr = 0, 0
        if root == nil { return }
        _, ldpr := dfs(root.Left)
        rdpl, _ := dfs(root.Right)
        
        dpl = ldpr + 1
        dpr = rdpl + 1
        ans = max(ans, max(dpl, dpr))
        return
    }
    dfs(root)
    return ans - 1
}
```



---



自顶向下DP

这种写法不一定要使用DFS, 使用BFS也行, 维护每一个节点及其父节点`pair<TreeNode*, TreeNode*>{root, parent}`

```go
func longestZigZag(root *TreeNode) int {
    dpl := map[*TreeNode]int{}
    dpr := map[*TreeNode]int{}

    maxLen := 0

    var dfs func(*TreeNode, *TreeNode)
    dfs = func(root *TreeNode, parent *TreeNode) {
        if root == nil { return }

        if parent == nil {
            dpl[root] = 1
            dpr[root] = 1
        } else {
            if parent.Left == root {
                dpl[root] = dpr[parent] + 1
                dpr[root] = 1
            } else {
                dpr[root] = dpl[parent] + 1
                dpl[root] = 1
            }
        }
        maxLen = max(maxLen, dpl[root])
        maxLen = max(maxLen, dpr[root])

        dfs(root.Left, root)
        dfs(root.Right, root)
    }
    dfs(root, nil)
    return maxLen - 1
}
```



#### C++

```c++
class Solution {
    void dfs(TreeNode* root, int& maxLen, int& pathLen, bool dir) {
        if (!root) {
            pathLen = 0;
            return;
        }
        pathLen ++;
        maxLen = max(maxLen, pathLen);
        if (!dir) {
            dfs(root->left, maxLen, pathLen, !dir);
            pathLen = 1;
            dfs(root->right, maxLen, pathLen, dir);
        } else {
            dfs(root->right, maxLen, pathLen, !dir);
            pathLen = 1;
            dfs(root->left, maxLen, pathLen, dir);
        }
    }
public:
    int longestZigZag(TreeNode* root) {
        int maxLen = 0, pathLen = 0;
        dfs(root, maxLen, pathLen, false);
        dfs(root, maxLen, pathLen, true);
        return maxLen - 1;
    }
};
```



## [1379. Find a Corresponding Node of a Binary Tree in a Clone of That Tree](https://leetcode.com/problems/find-a-corresponding-node-of-a-binary-tree-in-a-clone-of-that-tree/)

#### C++

```c++
class Solution {
public:
    TreeNode* getTargetCopy(TreeNode* original, TreeNode* cloned, TreeNode* target) {
        if (!original) return nullptr;
        if (original == target) return cloned;
        TreeNode* resLeft = getTargetCopy(original->left, cloned->left, target);
        if (resLeft) return resLeft;
        TreeNode* resRight = getTargetCopy(original->right, cloned->right, target);
        if (resRight) return resRight;
        return nullptr;
    }
};
```



## [1382. Balance a Binary Search Tree](https://leetcode.com/problems/balance-a-binary-search-tree/) [Tree]



## [1392. Longest Happy Prefix](https://leetcode.com/problems/longest-happy-prefix/)

#### Go

```go
func longestPrefix(s string) string {
    next := make([]int, len(s))
    j := 0
    for i := 1; i < len(s); i ++ {
        for j > 0 && s[i] != s[j] { j = next[j - 1] }
        if s[i] == s[j] { j ++ }
        next[i] = j
    }
    return s[:j]
}
```



## [1448. Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/)

#### Go

```go
func goodNodes(root *TreeNode) int {
    cnt := 0
    var foo func(*TreeNode, int)
    foo = func(root *TreeNode, pathMax int) {
        if root == nil {
            return
        }

        if root.Val >= pathMax {
            cnt ++
            pathMax = root.Val
        }
        foo(root.Left, pathMax)
        foo(root.Right, pathMax)
    }
    foo(root, math.MinInt)
    return cnt
}
```



## [1456. Maximum Number of Vowels in a Substring of Given Length](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/)

#### Go

```go
func maxVowels(s string, k int) int {
    vowel := func(c byte) bool {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u'
    }
    l := 0
    cnt := 0
    maxCnt := 0
    for r := 0; r < len(s); r ++ {
        if r - l + 1 > k { // 在循环中, 一般要先判断窗口合法性
            if vowel(s[l]) {
                cnt --
            }
            l ++
        }

        if vowel(s[r]) {
            cnt ++
            if cnt == k {
                return cnt
            }
            if cnt > maxCnt {
                maxCnt = cnt
            }
        }
    }
    return maxCnt
}
```



## [1457. Pseudo-Palindromic Paths in a Binary Tree](https://leetcode.com/problems/pseudo-palindromic-paths-in-a-binary-tree/)

#### Go

```go
func pseudoPalindromicPaths (root *TreeNode) int {
    rec := [9]int{}
    cnt := 0
    var foo func(*TreeNode)
    foo = func(root *TreeNode) {
        if root == nil {
            return
        }
        rec[root.Val - 1] ++
        if root.Left == nil && root.Right == nil {
            oddRec := 0
            for _, r := range rec {
                if r & 1 == 1 {
                    oddRec ++
                }
                if oddRec == 2 {
                    break
                }
            }
            if oddRec < 2 {
                cnt ++
            }
        } else {
            foo(root.Left)
            foo(root.Right)
        }
        rec[root.Val - 1] --
    }
    foo(root)
    return cnt
}
```



## [1475. Final Prices With a Special Discount in a Shop](https://leetcode.com/problems/final-prices-with-a-special-discount-in-a-shop/)

#### Go

```go
func finalPrices(prices []int) []int {
    discounts := make([]int, len(prices))
    stk := []int{}
    for i, p := range prices {
        discounts[i] = prices[i]
        for len(stk) > 0 && prices[ stk[len(stk)-1] ] >= p {
            discounts[ stk[len(stk)-1] ] = prices[ stk[len(stk)-1] ] - p
            stk = stk[: len(stk)-1]
        }
        stk = append(stk, i)
    }
    return discounts
}
```


