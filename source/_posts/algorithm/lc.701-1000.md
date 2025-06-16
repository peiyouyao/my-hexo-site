---
title: LeetCode 701-1000
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

## [701. Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/) [Tree]



## [704. Binary Search](https://leetcode.com/problems/binary-search/) [Array]



## [707. Design Linked List](https://leetcode.com/problems/design-linked-list/) [List]



## [714. Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/) [DP]



## [718. Maximum Length of Repeated Subarray](https://leetcode.com/problems/maximum-length-of-repeated-subarray/) [DP-Sub]



## [724. Find Pivot Index](https://leetcode.com/problems/find-pivot-index/) [Array]



## [725. Split Linked List in Parts](https://leetcode.com/problems/split-linked-list-in-parts/)

#### C++

```c++
class Solution {
    int lstsz(ListNode* head) {
        int sz = 0;
        while (head) {
            sz ++;
            head = head->next;
        }
        return sz;
    }
public:
    vector<ListNode*> splitListToParts(ListNode* head, int k) {
        int sz = lstsz(head);
        int q = sz / k;
        int r = sz - q * k;
        vector<ListNode*> ans(k, nullptr);
        for (int i = 0; i < k; i ++) {
            int splitsz = q;
            if (r > 0) {
                splitsz ++;
                r --;
            }
            if (splitsz == 0) break;
            splitsz --;
            ans[i] = head;
            while (splitsz > 0) {
                head = head->next;
                splitsz --;
            }
            ListNode* nxt = head->next;
            head->next = nullptr;
            head = nxt;
        }
        return ans;
    }
};
```



#### Java

```java
class Solution {
    public ListNode[] splitListToParts(ListNode head, int k) {
        int sz = listSize(head);
        int[] szs = new int[k];
        ListNode[] nds = new ListNode[k];
        Arrays.fill(szs, sz / k);
        int r = sz - sz / k * k;
        for (int i = 0; i < r; i ++) szs[i] ++;
        for (int i = 0; i < nds.length; i ++) {
            if (szs[i] == 0) continue;
            nds[i] = head;
            ListNode p = head;
            for (int j = 0; j < szs[i] - 1; j ++) if (p != null) p = p.next;
            head = p.next;
            p.next = null;
        }
        return nds;
    }
    private int listSize(ListNode head) {
        int sz = 0;
        while (head != null) {
            sz ++;
            head = head.next;
        }
        return sz;
    }
}
```



## [738. Monotone Increasing Digits](https://leetcode.com/problems/monotone-increasing-digits/) [Greedy]



## [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/) [Mono Stack]



## [746. Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/) [DP]



## [763. Partition Labels](https://leetcode.com/problems/partition-labels/) [Greedy]



## [796. Rotate String](https://leetcode.com/problems/rotate-string/)

#### Go

```go
func rotateString(s string, goal string) bool {
    return len(s) == len(goal) && kmp(s + s, goal) != -1
}

func kmp(haystack, needle string) int {
    next := make([]int, len(needle))
    for i, j := 1, 0; i < len(next); i ++ {
        for j > 0 && needle[i] != needle[j] { j = next[j - 1] }
        if needle[i] == needle[j] { j ++ }
        next[i] = j
    }

    for i, j := 0, 0; i < len(haystack); i ++ {
        for j > 0 && haystack[i] != needle[j] { j = next[j - 1] }
        if haystack[i] == needle[j] { j ++ }
        if j == len(needle) { return i - len(needle) + 1 }
    }
    return -1
}
```



## [797. All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/) [Graph]



## [814. Binary Tree Pruning](https://leetcode.cn/problems/binary-tree-pruning/)

#### Go

正确写法

```go
func pruneTree(root *TreeNode) *TreeNode {
    if root == nil { return nil }
    root.Left = pruneTree(root.Left)
    root.Right = pruneTree(root.Right)
    if root.Val == 0 && root.Left == nil && root.Right == nil { return nil }
    return root
}
```



错误写法

```go
func pruneTree(root *TreeNode) *TreeNode {
    if root == nil { return nil }
    if root.Val == 0 && root.Left == nil && root.Right == nil { return nil } // 在这里return, 遗漏了0在枝干上的情况
    root.Left = pruneTree(root.Left)
    root.Right = pruneTree(root.Right)
    return root
}
```



两者仅仅是if的位置不对



## [817. Linked List Components](https://leetcode.cn/problems/linked-list-components/)

#### C++

```c++
class Solution {
public:
    int numComponents(ListNode* head, vector<int>& nums) {
        unordered_set<int> uset;
        for (int num : nums) uset.insert(num);
        bool lastin = false; // 记录之前的节点在不在uset中
        int cnt = 0;
        while (head) {
            if (uset.find(head->val) != uset.end()) {
                if (!lastin) {
                    cnt ++;
                    lastin = true;
                }
            } else lastin = false;
            head = head->next;
        }
        return cnt;
    }
};
```



## [841. Keys and Rooms](https://leetcode.com/problems/keys-and-rooms/) [Graph]



## [844. Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/) [Array & String]



## [860. Lemonade Change](https://leetcode.com/problems/lemonade-change/) [Greedy]



## [865. Smallest Subtree with all the Deepest Nodes](https://leetcode.cn/problems/smallest-subtree-with-all-the-deepest-nodes/)

#### C++

```c++
class Solution {
    int H;

    int treeHeight(TreeNode* root) {
        if (!root) return 0;
        return 1 + max(treeHeight(root->left), treeHeight(root->right));
    }

    TreeNode* dfs(TreeNode* root, int depth) {
        if (!root) return nullptr;
        depth ++;
        if (depth == H) return root;
        TreeNode* l = dfs(root->left, depth);
        TreeNode* r = dfs(root->right, depth);
        if (l && r) return root;
        else if (l) return l;
        else if (r) return r;
        else return nullptr;
    }

public:
    TreeNode* subtreeWithAllDeepest(TreeNode* root) {
        H = treeHeight(root);
        return dfs(root, 0);
    }
};
```



#### Go

更巧妙的方法, 只需DFS一遍

```go
func subtreeWithAllDeepest(root *TreeNode) *TreeNode {
    var dfs func(*TreeNode) (*TreeNode, int)
    dfs = func(root *TreeNode) (*TreeNode, int) {
        if root == nil {
            return nil, 0
        }
        lnode, ldepth := dfs(root.Left)
        rnode, rdepth := dfs(root.Right)
        if ldepth == rdepth {
            return root, 1 + max(ldepth, rdepth)
        } else if ldepth < rdepth {
            return rnode, 1 + rdepth
        } else {
            return lnode, 1 + ldepth
        }
    }
    ans, _ := dfs(root)
    return ans
}
```



```go
func treeHeight(root *TreeNode) int {
    if root == nil { return 0 }
    return 1 + max(treeHeight(root.Left), treeHeight(root.Right))
}

func subtreeWithAllDeepest(root *TreeNode) *TreeNode {
    h := treeHeight(root)
    var dfs func(*TreeNode, int) *TreeNode
    dfs = func(root *TreeNode, depth int) *TreeNode {
        if root == nil { return nil }
        depth ++
        if depth == h {
            return root
        }
        l := dfs(root.Left, depth)
        r := dfs(root.Right, depth)
        if l != nil && r != nil {
            return root
        } else if l != nil {
            return l
        } else if r != nil {
            return r
        } else {
            return nil
        }
    }
    return dfs(root, 0)
}
```



## [872. Leaf-Similar Trees](https://leetcode.cn/problems/leaf-similar-trees/)

#### Go

```go
func leafSimilar(root1 *TreeNode, root2 *TreeNode) bool {
    leafvals := []int{}
    var foo func(*TreeNode, bool) bool
    idx := 0
    foo = func(root *TreeNode, first bool) bool {
        if root == nil {
            return true
        }
        if root.Left == nil && root.Right == nil {
            if first {
                leafvals = append(leafvals, root.Val)
                return true
            } else {
                if idx == len(leafvals) || leafvals[idx] != root.Val {
                    return false
                } else {
                    idx ++
                }
            }
        }
        if !foo(root.Left, first) {
            return false
        }
        if !foo(root.Right, first) {
            return false
        }
        return true
    }
    foo(root1, true)
    return foo(root2, false) && idx == len(leafvals)
}
```



## [874. Walking Robot Simulation](https://leetcode.com/problems/walking-robot-simulation/)

#### Java

```java
class Solution {
    public int robotSim(int[] commands, int[][] obstacles) {
        Set<String> obs = new HashSet<>();
        for (int[] obstacle : obstacles) obs.add(obstacle[0] + " " + obstacle[1]); // 把坐标JSON化, 方便哈希
        int ans = 0;
        int x = 0, y = 0, d = 1;
        for (int command : commands) {
            int[] nxt = go(command, obs, x, y, d);
            int nx = nxt[0], ny = nxt[1], nd = nxt[2];
            x = nx;
            y = ny;
            d = nd;
            ans = Math.max(ans, x * x + y * y);
        }
        return ans;
    }
    private final int[] dx = {1, 0, -1, 0};
    private final int[] dy = {0, 1, 0, -1};
    private int[] go(int command, Set<String> obstacles, int x, int y, int d) {
        int nx = x, ny = y, nd = d;
        if (command < 0) {
            switch (command) {
                case -1: nd = (d - 1 + 4) % 4; break;
                case -2: nd = (d + 1) % 4; break;
            }
        } else {
            while (command > 0) {
                int nnx = x + dx[d], nny = y + dy[d];
                String pos = nnx + " " + nny;
                if (obstacles.contains(pos)) break;
                x = nnx;
                y = nny;
                command --;
            }
            nx = x;
            ny = y;
        }
        return new int[] {nx, ny, nd};
    }
}
```

事实上, Java 要是允许指针操作, 以上逻辑会更清晰



#### Go

使用位运算来优化哈希

```go
func robotSim(commands []int, obstacles [][]int) int {
    dx, dy := [4]int{0, 1, 0, -1}, [4]int{1, 0, -1, 0}
    d := 0 // 0: up, 1: right, 2: down, 3: left
    mp := map[int]bool{}
    for _, obstacle := range obstacles {
        ox, oy := obstacle[0], obstacle[1]
        mp[(ox << 15) + oy] = true
    }
    x, y := 0, 0
    dist := 0
    for _, cmd := range commands {
        if cmd < 0 {
            if cmd == -1 {
                d = (d + 1) % 4
            } else {
                d = (d + 3) % 4
            }
        } else {
            for cmd > 0 {
                nx, ny := x + dx[d], y + dy[d]
                k := (nx << 15) + ny
                if ok, _ := mp[k]; ok {
                    break
                }
                x = nx
                y = ny
                cmd --
            }
        }
        if x * x + y * y > dist {
            dist = x * x + y * y
        }
    }
    return dist
}
```



#### Rust

```rust
use std::collections::HashSet;
impl Solution {
    pub fn robot_sim(commands: Vec<i32>, obstacles: Vec<Vec<i32>>) -> i32 {
        let mut x: i32 = 0;
        let mut y: i32 = 0;
        let mut d: usize = 1;
        let mut obs: HashSet<(i32, i32)> = HashSet::new(); // rust 可用直接哈希元组
        for obstacle in obstacles { obs.insert((obstacle[0], obstacle[1])); }
        let mut dist: i32 = 0;
        for command in commands {
            Solution::go(command, &obs, &mut x, &mut y, &mut d);
            dist = i32::max(dist, x * x + y * y);
        }
        dist
    }

    const DX: [i32; 4] = [1, 0, -1, 0]; // const用蛇形大写
    const DY: [i32; 4] = [0, 1, 0, -1];

    fn go(command: i32, obstacles: &HashSet<(i32, i32)>, x: &mut i32, y: &mut i32, d: &mut usize) {
        if command < 0 {
            if command == -1 { *d = (*d - 1 + 4) % 4; }
            else { *d = (*d + 1) % 4; }
        } else {
            for _ in 0..command {
                let nx: i32 = *x + Solution::DX[*d];
                let ny: i32 = *y + Solution::DY[*d];
                if (obstacles.contains(&(nx, ny))) {
                    break;
                }
                *x = nx;
                *y = ny;
            }
        }
    }
}
```



## [876. Middle of the Linked List](https://leetcode.cn/problems/middle-of-the-linked-list/)

#### Go

```go
func middleNode(head *ListNode) *ListNode {
    fast, slow := head, head
    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
    }
    return slow
}
```



## [904. Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/) [Array]



## [922. Sort Array By Parity II](https://leetcode.com/problems/sort-array-by-parity-ii/) [Array]



## [925. Long Pressed Name](https://leetcode.com/problems/long-pressed-name/) [String]



## [941. Valid Mountain Array](https://leetcode.com/problems/valid-mountain-array/) [Array]



## [951. Flip Equivalent Binary Trees](https://leetcode.cn/problems/flip-equivalent-binary-trees/)

#### C++

```c++
class Solution {
    bool ok(TreeNode* root1, TreeNode* root2) {
        if (!root1 && !root2) return true;
        if (!root1 ^ !root2) return false;
        return root1->val == root2->val;
    }
public:
    bool flipEquiv(TreeNode* root1, TreeNode* root2) {
        if (!root1 && !root2) return true;
        if (ok(root1, root2)) {
            if (ok(root1->left, root2->left)) {
                if (!flipEquiv(root1->left, root2->left)) return false;
                if (!flipEquiv(root1->right, root2->right)) return false;
            } else {
                if (!flipEquiv(root1->left, root2->right)) return false;
                if (!flipEquiv(root1->right, root2->left)) return false;
            }
            return true;
        } else return false;
    }
};
```



#### Go

```go
func ok(root1, root2 *TreeNode) bool {
    if root1 == nil && root2 == nil {
        return true
    } else if root1 == nil && root2 != nil || root1 != nil && root2 == nil  {
        return false
    } else {
        return root1.Val == root2.Val
    }
}

func flipEquiv(root1 *TreeNode, root2 *TreeNode) bool {
    if root1 == nil && root2 == nil {
        return true
    }

    if !ok(root1, root2) {
        return false
    } else {
        if !ok(root1.Left, root2.Left) {
            if !flipEquiv(root1.Left, root2.Right) {
                return false
            }

            if !flipEquiv(root1.Right, root2.Left) {
                return false
            }
        } else {
            if !flipEquiv(root1.Left, root2.Left) {
                return false
            }

            if !flipEquiv(root1.Right, root2.Right) {
                return false
            }
        }
        return true
    }
}
```



## [965. Univalued Binary Tree](https://leetcode.cn/problems/univalued-binary-tree/)

#### Go

```go
func isUnivalTree(root *TreeNode) bool {
    v := root.Val
    var dfs func(*TreeNode) bool
    dfs = func(root *TreeNode) bool {
        if root == nil {
            return true
        }
        if root.Val != v {
            return false
        }
        if !dfs(root.Left) {
            return false
        }
        if !dfs(root.Right) {
            return false
        }
        return true
    }
    return dfs(root)
}
```



## [968. Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/)[Greedy]



## [971. Flip Binary Tree To Match Preorder Traversal](https://leetcode.cn/problems/flip-binary-tree-to-match-preorder-traversal/)

#### Go

```go
func flipMatchVoyage(root *TreeNode, voyage []int) []int {
    swap := func(a, b **TreeNode) {
        t := *a; *a = *b; *b = t
    }

    ans := []int{}
    idx := 0
    var dfs func(*TreeNode) bool 
    dfs = func(root *TreeNode) bool {
        if root == nil {
            return true
        }

        if idx == len(voyage) || root.Val != voyage[idx] {
            return false
        }
        idx ++
        
        if idx < len(voyage) { // 还有后继
            if root.Left != nil && root.Left.Val != voyage[idx] {
                swap(&root.Left, &root.Right)
                ans = append(ans, root.Val)
            }
        } else { // 无后继
            if root.Left != nil || root.Right != nil {
                return false
            }
        }

        if !dfs(root.Left) {
            return false
        }
        if !dfs(root.Right) {
            return false
        }
        return true
    }

    if !dfs(root) {
        return []int{-1}
    }
    return ans
}
```



更加简洁的写法, 不需要显示第交换左右孩子

```go
func flipMatchVoyage(root *TreeNode, voyage []int) []int {
    ans := []int{}
    idx := 0
    var dfs func(*TreeNode) bool 
    dfs = func(root *TreeNode) bool {
        if root == nil {
            return true
        }

        if idx == len(voyage) || root.Val != voyage[idx] {
            return false
        }
        idx ++
        
        if idx < len(voyage) {
            if root.Left != nil && root.Left.Val != voyage[idx] {
                ans = append(ans, root.Val)
                if !dfs(root.Right) {
                    return false
                }
                if !dfs(root.Left) {
                    return false
                }
            } else {
                if !dfs(root.Left) {
                    return false
                }
                if !dfs(root.Right) {
                    return false
                }
            }
        } else {
            if root.Left != nil || root.Right != nil {
                return false
            }
        }
        return true
    }

    if !dfs(root) {
        return []int{-1}
    }
    return ans
}
```



#### C++

```c++
class Solution {
    void swap(TreeNode** a, TreeNode** b) {
        TreeNode* t = *a; *a = *b; *b = t;
    }

    bool dfs(vector<int>& ans, vector<int>& voyage, TreeNode* root, int& idx) {
        if (!root) return true;
        if (idx < voyage.size() && root->val != voyage[idx]) return false;
        idx ++;
        if (idx < voyage.size()) {
            if (root->left && root->left->val != voyage[idx]) {
                swap(&root->left, &root->right);
                ans.push_back(root->val);
            }
        } else if (root->left || root->right) return false;
        if (!dfs(ans, voyage, root->left, idx)) return false;
        if (!dfs(ans, voyage, root->right, idx)) return false;
        return true;
    }
public:
    vector<int> flipMatchVoyage(TreeNode* root, vector<int>& voyage) {
        vector<int> ans;
        int idx = 0;
        if (!dfs(ans, voyage, root, idx)) return {-1};
        return ans;
    }
};
```



## [977. Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/) [Array]



## [988. Smallest String Starting From Leaf](https://leetcode.cn/problems/smallest-string-starting-from-leaf/)

#### Go

```go
func smallestFromLeaf(root *TreeNode) string {
    path := []int{}
    var ans []int = nil
    compare := func(a, b []int) int {
        for i := 0 ; i < len(a) && i < len(b); i ++ {
            ia, ib := len(a) - 1 - i, len(b) - 1 - i
            if a[ia] < b[ib] {
                return -1
            }
            if a[ia] > b[ib] {
                return 1
            }
        }
        if len(a) < len(b) {
            return -1
        } else if len(a) > len(b) {
            return 1
        } else {
            return 0
        }
    }

    var foo func(*TreeNode)
    foo = func(root *TreeNode) {
        if root == nil {
            return
        }

        path = append(path, root.Val)
        if root.Left == nil && root.Right == nil {
            if ans == nil || compare(path, ans) == -1 {
                ans = make([]int, len(path))
                copy(ans, path)
            }
        }
        foo(root.Left)
        foo(root.Right)
        path = path[: len(path) - 1 ]
    }
    foo(root)
    ansStr := ""
    for i := len(ans) - 1; i > -1; i -- {
        ansStr += string(rune(ans[i] + 'a'))
    }
    return ansStr
}
```



#### Java

```java
class Solution {
    public String smallestFromLeaf(TreeNode root) {
        foo(root);
        Collections.reverse(ans);
        return ans.stream()
            .map(x -> String.valueOf((char) ('a' + x)))
            .reduce("", String::concat);
    }

    private List<Integer> path = new ArrayList<>();
    private List<Integer> ans;

    private int compare(List<Integer> a, List<Integer> b) {
        for (int i = 0; i < a.size() && i < b.size(); i ++) {
            int ia = a.size() - 1 - i, ib = b.size() - 1 - i;
            if (a.get(ia) < b.get(ib)) return -1;
            if (a.get(ia) > b.get(ib)) return  1;
        }
        if (a.size() == b.size()) return 0;
        else if (a.size() < b.size()) return -1;
        else return 1;
    }

    private void foo(TreeNode root) {
        if (root == null) return;
        path.add(root.val);
        if (root.left == null && root.right == null) {
            if (ans == null || compare(path, ans) == -1) {
                ans = new ArrayList<>(path);
            }
        }
        foo(root.left);
        foo(root.right);
        path.removeLast();
    }
}
```

