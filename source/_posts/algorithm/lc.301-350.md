---
title: LeetCode 301-350
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

---

## 301 - 302 ...



## [303. Range Sum Query - Immutable](https://leetcode.com/problems/range-sum-query-immutable/) [Array]



## [304. Range Sum Query 2D - Immutable](https://leetcode.com/problems/range-sum-query-2d-immutable/) [Array]



## [306. Additive Number](https://leetcode.com/problems/additive-number/)

*Backtrack*

#### Java

在 path 中存贮每一个切分点 + 1的索引, 一开始在 path 中存储 0 代表 一开始没有加入任何字符, 同时保证每一次切分的第一个字符串是从 0 索引处开始

```java
class Solution {
    private int convert(String str, int start, int end) {
        if (start >= end) return -1;
        if (end > start + 1 && str.charAt(start) == '0') return -1;
        int num = 0;
        for (int i = start; i < end; i ++) {
            num *= 10;
            num += (int)(str.charAt(i) - '0');
        }
        return num;
    }

    private boolean backtrack(List<Integer> path, String str, int idx) {
        if (idx == str.length() && path.size() >= 4) return true;
        if (idx >= str.length()) return false;
        for (int i = idx; i < str.length(); i ++) {
            if (i - idx > str.length() / 2) return false; // 截取的 idx - i 位太长, 肯定搞不了
            if (path.size() >= 3) { // 当 path.size() = 0, 1, 2 时, 无条件添加 i + 1 (截取)
                int end2 = path.get(path.size() - 1);
                int end1 = path.get(path.size() - 2);
                int start = path.get(path.size() - 3);
                int num1 = convert(str, start, end1);
                int num2 = convert(str, end1, end2);
                int num = convert(str, end2, i + 1);
                if (num1 + num2 != num) continue;
            }
            path.add(i + 1);
            if (backtrack(path, str, i + 1)) return true;
            path.removeLast();
        }
        return false;
    }

    public boolean isAdditiveNumber(String num) {
        if (num.length() < 3) return false;
        List<Integer> path = new ArrayList<>();
        path.add(0); // 确保第一段一定是从 0 开始截取的
        return backtrack(path, num, 0);
    }
}
```



#### C++

这个版本中, path 存储的已经截取的数字本身

```c++
class Solution {
    long long convert(string& str, int l, int r) {
        if (r < l || (l + 1 <= r && str[l] == '0')) return -1;
        long long num = 0;
        for (int i = l; i <= r; i ++) {
            num *= 10;
            num += str[i] - '0';
        }
        return num;
    }

    bool backtrack(string& str, vector<long long>& path, int idx) {
        if (idx == str.size() && path.size() >= 3) return true;
        if (idx >= str.size()) return false;
        for (int i = idx; i < str.size(); i ++) {
            if (i - idx > str.size() / 2) return false; // 截取的位数 i - idx 太大, 肯定搞不成
            long long num;
            if (path.size() == 0) num = convert(str, 0, i); // path.size() == 0, 只能从头开始截取 !!!
            else if (path.size() == 1) num = convert(str, idx, i); //sz == 1, 无需判断当前的num是否合法, 直接加入
            else {
                num = convert(str, idx, i);
                long long num2 = path[path.size() - 1];
                long long num1 = path[path.size() - 2];
                if (num1 + num2 != num) continue;
            }
            path.push_back(num);
            if (backtrack(str, path, i + 1)) return true;
            path.pop_back();
        }
        return false;
    }
public:
    bool isAdditiveNumber(string num) {
        vector<long long> path;
        return backtrack(num, path, 0);
    }
};
```



## [307. Range Sum Query - Mutable](https://leetcode.cn/problems/range-sum-query-mutable/)

*Segment Tree*

#### Java

```java
class NumArray {
    private int[] segmentTree;
    private int n;
    private void buildTree(int[] nums, int cnd, int l, int r) {
        if (l == r) {
            segmentTree[cnd] = nums[l];
            return;
        }
        int m = (l + r) >> 1;
        int lnd = cnd * 2 + 1, rnd = cnd * 2 + 2;
        buildTree(nums, lnd, l, m);
        buildTree(nums, rnd, m + 1, r);
        segmentTree[cnd] = segmentTree[lnd] + segmentTree[rnd];
    }

    private void updateTree(int val, int idx, int cnd, int l, int r) {
        if (idx < l || idx > r) return;
        if (l == r) {
            segmentTree[cnd] = val;
            return;
        }
        int m = (l + r) >> 1;
        int lnd = cnd * 2 + 1, rnd = cnd * 2 + 2;
        updateTree(val, idx, lnd, l, m);
        updateTree(val, idx, rnd, m + 1, r);
        segmentTree[cnd] = segmentTree[lnd] + segmentTree[rnd];
    }

    private int readTree(int tl, int tr, int cnd, int l, int r) { // l, r 与 cnd 是绑定的
        if (tl == l && tr == r) return segmentTree[cnd];
        int m = (l + r) >> 1;
        int lnd = cnd * 2 + 1, rnd = cnd * 2 + 2;
        if (tr <= m) {
            return readTree(tl, tr, lnd, l, m);
        } else if (m < tl) {
            return readTree(tl, tr, rnd, m + 1, r);
        } else {
            return readTree(tl, m, lnd, l, m) + readTree(m + 1, tr, rnd, m + 1, r);
        }
    }

    public NumArray(int[] nums) {
        n = nums.length;
        segmentTree = new int[4 * n]; // 4*n是tree节点最大估计
        buildTree(nums, 0, 0, n - 1);
    }
    
    public void update(int index, int val) {
        updateTree(val, index, 0, 0, n - 1);
    }
    
    public int sumRange(int left, int right) {
        return readTree(left, right, 0, 0, n - 1);
    }
}
```

#### Go

```go
type NumArray struct {
    segtree []int
    n int
}

func (me *NumArray) updateTree(val, idx, cnd, l, r int) {
    if idx < l || idx > r {
        return
    }
    if idx == l && idx == r {
        me.segtree[cnd] = val
        return
    }
    m := (l + r) >> 1
    lnd := cnd * 2 + 1
    rnd := cnd * 2 + 2
    me.updateTree(val, idx, lnd, l, m)
    me.updateTree(val, idx, rnd, m + 1, r)
    me.segtree[cnd] = me.segtree[lnd] + me.segtree[rnd]
}

func (me *NumArray) readTree(tl, tr, cnd, l, r int) int {
    if l == tl && r == tr {
        return me.segtree[cnd]
    }
    m := (l + r) >> 1
    lnd := cnd * 2 + 1
    rnd := cnd * 2 + 2
    if tr <= m {
        return me.readTree(tl, tr, lnd, l, m)
    } else if m < tl {
        return me.readTree(tl, tr, rnd, m + 1, r)
    } else {
        return me.readTree(tl, m, lnd, l, m) + me.readTree(m + 1, tr, rnd, m + 1, r)
    }
}

func Constructor(nums []int) NumArray {
    n := len(nums)
    segtree := make([]int, 4 * n)
    var buildTree func(cnd, l, r int)
    buildTree = func(cnd, l, r int) {
        if l == r {
            segtree[cnd] = nums[l]
            return
        }
        m := (l + r) >> 1
        lnd := cnd * 2 + 1
        rnd := cnd * 2 + 2
        buildTree(lnd, l, m)
        buildTree(rnd, m + 1, r)
        segtree[cnd] = segtree[lnd] + segtree[rnd]
    }
    buildTree(0, 0, n - 1)

    numArray := NumArray {segtree, n}
    return numArray
}


func (me *NumArray) Update(index int, val int)  {
    me.updateTree(val, index, 0, 0, me.n - 1)
}


func (me *NumArray) SumRange(left int, right int) int {
    return me.readTree(left, right, 0, 0, me.n - 1)
}
```



## [309. Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) [DP]



## [310. Minimum Height Trees](https://leetcode.cn/problems/minimum-height-trees/)

#### Java

*BFS*

```
谁能作为根节点? 
- 在这个树的最大"直径"上的中间一个点或两个点
如何找到树中构成最大路径的两点(x, y)?
- 这两个点必然位于集合的"边界"上 \partial
- 从任意一点开始(0), bfs, 找离0最远的点x, x必然位于边界上
- 从x出发, 找离x最远的点y
```



```java
class Solution {
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        List<Integer> ans = new ArrayList<>();
        if (n == 1) {
            ans.add(0);
            return ans;
        }
        List<Integer>[] adj = new List[n];
        for (int i = 0; i < n; i ++) adj[i] = new ArrayList<>();
        for (int[] edge : edges) {
            adj[edge[0]].add(edge[1]);
            adj[edge[1]].add(edge[0]);
        }

        int[] parent = new int[n];
        Arrays.fill(parent, -1);
        int x = findFarthestNode(adj, parent, 0); // 0 -> x
        int y = findFarthestNode(adj, parent, x); // x -> y
        parent[x] = -1; // 从x扩张到y, 所以, 这里一定要令x的父节点为-1, 不能令y的父节点为-1
        List<Integer> x2y = new ArrayList<>();
        while (y != -1) {
            x2y.add(y);
            y = parent[y];
        }
        int m = x2y.size();
        if ((m & 1) == 0) ans.add(x2y.get((m >> 1) - 1));
        ans.add(x2y.get((m >> 1)));
        
        return ans;
    }

    private int findFarthestNode(List<Integer>[] adj, int[] parent, int start) {
        Queue<Integer> q = new ArrayDeque<>();
        boolean[] vis = new boolean[adj.length];
        q.offer(start);
        vis[start] = true;
        int farthestNode = -1;

        while (!q.isEmpty()) {
            int u = q.poll();
            farthestNode = u;
            for (int v : adj[u]) {
                if (vis[v]) continue;
                vis[v] = true;
                q.offer(v);
                parent[v] = u; // v 是从 u 过来的
            }
        }
        return farthestNode;
    }

}
```



#### Go

*BFS*

```go
func findMinHeightTrees(n int, edges [][]int)(ans []int) {
    if n == 1 {
        ans = append(ans, 0);
        return
    }
    adj := make([][]int, n);
    for _, edge := range edges {
        adj[edge[0]] = append(adj[edge[0]], edge[1])
        adj[edge[1]] = append(adj[edge[1]], edge[0])
    }

    parent := make([]int, n)
    for i := range parent {
        parent[i] = -1;
    }

    getFarthestNode := func(st int)(farthestNode int) {
        q := []int{st}
        vis := make([]bool, n)
        vis[st] = true
        farthestNode = -1

        for (len(q) > 0) {
            u := q[0];
            farthestNode = u;
            q = q[1:];
            for _, v := range adj[u] {
                if vis[v] {
                    continue
                }
                q = append(q, v)
                vis[v] = true
                parent[v] = u
            }
        }
        return
    }

    x := getFarthestNode(0)
    y := getFarthestNode(x)

    x2y := []int{}
    parent[x] = -1
    for y != -1 {
        x2y = append(x2y, y)
        y = parent[y]
    }

    m := len(x2y)
    if m & 1 == 0 {
        ans = append(ans, x2y[m >> 1 - 1])
    }
    ans = append(ans, x2y[m >> 1]);
    return
}
```



*DFS*

```go
func findMinHeightTrees(n int, edges [][]int)(ans []int) {
    if n == 1 {
        ans = append(ans, 0);
        return
    }
    adj := make([][]int, n);
    for _, edge := range edges {
        adj[edge[0]] = append(adj[edge[0]], edge[1])
        adj[edge[1]] = append(adj[edge[1]], edge[0])
    }
	
    // path-vis-dfs
    path := []int{}
    vis := make([]bool, n)
    var dfs func(*[]int, *int, int)
    dfs = func(farthestPath *[]int, farthestNode *int, u int) {
        if vis[u] {
            return
        }
        vis[u] = true
        path = append(path, u)
        ok := true
        for _, v := range adj[u] {
            if !vis[v] {
                ok = false
                dfs(farthestPath, farthestNode, v)
            }
        }
        if ok {
            if len(path) > len(*farthestPath) {
                *farthestPath = make([]int, len(path))
                copy(*farthestPath, path)
                *farthestNode = u
            }
        }
        vis[u] = false
        path = path[:len(path) - 1]
    }
	
    /*
    x := -1
    fp := []int{} // 0 -> x
    dfs(&fp, &x, 0)
    */
    x := -1
    dfs(&[]int{}, &x, 0) // go允许匿名变量

    y := -1
    x2y := []int{}
    dfs(&x2y, &y, x)

    m := len(x2y)
    if m & 1 == 0 {
        ans = append(ans, x2y[m >> 1 - 1])
    }
    ans = append(ans, x2y[m >> 1]);
    return
}
```



*Topological Sort*

```go
func findMinHeightTrees(n int, edges [][]int)(ans []int) {
    if n == 1 {
        ans = append(ans, 0)
        return
    }
    adj := make([][]int, n)
    deg := make([]int, n)
    for _, edge := range edges {
        adj[edge[0]] = append(adj[edge[0]], edge[1])
        adj[edge[1]] = append(adj[edge[1]], edge[0])
        deg[edge[0]] ++
        deg[edge[1]] ++
    }

    q := []int{}
    for i := 0; i < n; i ++ {
        if deg[i] == 1 {
            q = append(q, i)
            deg[i] = 0
        }
    }

    cnt := n
    for cnt > 2 {
        sz := len(q)
        cnt -= sz
        for i := 0; i < sz; i ++ {
            u := q[0]; q = q[1:]
            for _, v := range adj[u] {
                if deg[v] == 0 {
                    continue
                }
                deg[v] --
                if deg[v] == 1 {
                    q = append(q, v)
                    deg[v] = 0
                }
            }
        }
    }

    for len(q) > 0 {
        ans = append(ans, q[0])
        q = q[1:]
    }
    return
}
```



#### C++

*DFS*

```c++
class Solution {
    vector<bool> vis;
    vector<int> path;
    vector<vector<int>> adj;
    void dfs(vector<int>& farthrestPath, int& farthrestNode, int u) {
        vis[u] = true;
        path.push_back(u);
        
        bool end = true;
        for (int v : adj[u]) {
            if (!vis[v]) {
                end = false;
                dfs(farthrestPath, farthrestNode, v);
            }
        }
        if (end && farthrestPath.size() < path.size()) {
            farthrestPath = path;
            farthrestNode = u;
        }

        vis[u] = false;
        path.pop_back();
    }
public:
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        vector<int> ans;
        if (n == 1) {
            ans.push_back(0);
            return ans;
        }
        adj = vector<vector<int>>(n, vector<int>{});
        for (auto& e : edges) {
            adj[e[0]].push_back(e[1]);
            adj[e[1]].push_back(e[0]);
        }
        vis = vector<bool>(n);

        vector<int> fp{};
        int x = -1;
        dfs(fp, x, 0);
        fp = {};
        int y = -1;
        dfs(fp, y, x);

        int m = fp.size();
        if ((m & 1) == 0) ans.push_back(fp[(m >> 1) - 1]);
        ans.push_back(fp[(m >> 1)]);
        return ans;
    }
};
```



*Topological Sort*

低效写法(47ms)

```c++
class Solution {
public:
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        vector<int> ans;
        if (n == 1) {
            ans.push_back(0);
            return ans;
        }
        vector<vector<int>> adj = vector<vector<int>>(n, vector<int>{});
        vector<int> deg(n);
        for (auto& e : edges) {
            adj[e[0]].push_back(e[1]);
            adj[e[1]].push_back(e[0]);
            deg[e[0]] ++;
            deg[e[1]] ++;
        }

        int cnt = n;
        queue<int> q;
        for (int i = 0; i < n; i ++) {
            if (deg[i] == 1) q.push(i); // 加入队列时没有及时置deg = 0, 效率低
        }
        
        while (cnt > 2) {
            int sz = q.size();
            cnt -= sz;
            for (int i = 0; i < sz; i ++) {
                int u = q.front(); q.pop();
                deg[u] = 0;
                for (int v : adj[u]) {
                    if (deg[v] == 0) continue;
                    deg[v] --;
                    if (deg[v] == 1) q.push(v);
                }
            }
        }
        while (!q.empty()) {
            ans.push_back(q.front()); q.pop();
        }
        return ans;
    }
};
```

高效写法(36ms)

```c++
class Solution {
public:
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        vector<int> ans;
        if (n == 1) {
            ans.push_back(0);
            return ans;
        }
        vector<vector<int>> adj = vector<vector<int>>(n, vector<int>{});
        vector<int> deg(n);
        for (auto& e : edges) {
            adj[e[0]].push_back(e[1]);
            adj[e[1]].push_back(e[0]);
            deg[e[0]] ++;
            deg[e[1]] ++;
        }

        int cnt = n;
        queue<int> q;
        for (int i = 0; i < n; i ++) {
            if (deg[i] == 1) {
                q.push(i);
                deg[i] = 0; // 加入队列立刻置deg=0, 效率高
            }
        }
        
        while (cnt > 2) {
            int sz = q.size();
            cnt -= sz; // 把最外面一层的点删除
            for (int i = 0; i < sz; i ++) {
                int u = q.front(); q.pop();
                for (int v : adj[u]) {
                    if (deg[v] == 0) continue;
                    deg[v] --;
                    if (deg[v] == 1) {
                        q.push(v);
                        deg[v] = 0;
                    }
                }
            }
        }
        while (!q.empty()) {
            ans.push_back(q.front()); q.pop();
        }
        return ans;
    }
};
```



## [313. Super Ugly Number](https://leetcode.cn/problems/super-ugly-number/)

#### C++

```c++
class Solution {
public:
    int nthSuperUglyNumber(int n, vector<int>& primes) {
        vector<long> dp(n);
        dp[0] = 1;
        vector<int> idxs(primes.size()); // idxs[j] 表示下一次在 dp[idxs[j]] 上乘以的素数是 primes[idxs[j]]
        for (int i = 1; i < n; i ++) {
            dp[i] = INT_MAX;
            for (int pid = 0; pid < primes.size(); pid ++) {
                dp[i] = min(dp[i], dp[idxs[pid]] * primes[pid]);
            }
            for (int pid = 0; pid < primes.size(); pid ++) {
                if (dp[i] == dp[idxs[pid]] * primes[pid]) idxs[pid] ++;
            }
        }
        return (int)dp[n - 1];
    }
};
```



#### Go

```go
func nthSuperUglyNumber(n int, primes []int) int {
    dp := make([]int, n)
    dp[0] = 1
    idxs := make([]int, len(primes)) // idxs[j] 表示下一次在 dp[idxs[j]] 上乘以的素数是 primes[idxs[j]]
    for i := 1; i < n; i ++ {
        dp[i] = math.MaxInt
        for j, p := range primes {
            if dp[i] > dp[idxs[j]] * p {
                dp[i] = dp[idxs[j]] * p
            }
        }

        for j, p := range primes {
            if dp[idxs[j]] * p == dp[i] {
                idxs[j] ++
            }
        }
    }
    return dp[n - 1]
}
```



## [316. Remove Duplicate Letters](https://leetcode.cn/problems/remove-duplicate-letters/)

*Greedy + Mono Stack*

#### C++

```c++
class Solution {
public:
    string removeDuplicateLetters(string s) {
        int cnt[26]{};
        bool added[26]{}; // 最关键的一步, cnt==0,则added==T; 反之不然
        for (char c : s) cnt[c - 'a'] ++;
        string stk;
        for (char c : s) {
            if (!added[c - 'a']) {
                while (!stk.empty() && stk.back() > c && cnt[stk.back() - 'a'] > 0) {
                    added[stk.back() - 'a'] = false;
                    stk.pop_back();
                }
                added[c - 'a'] = true;
                stk.push_back(c);
            }
            cnt[c - 'a'] --; // c 用一个少一个
        }
        return stk;
    }
};
```



#### Go

```go
func removeDuplicateLetters(s string) string {
    cnt := [26]int{};
    added := [26]bool{};
    stk := []byte{}
    for _, c := range s {
        cnt[c - 'a'] ++
    }
    for i := range s {
        c := s[i]
        if !added[c - 'a'] {
            for len(stk) > 0 && stk[len(stk) - 1] > c && cnt[stk[len(stk) - 1] - 'a'] > 0 {
                added[stk[len(stk) - 1] - 'a'] = false
                stk = stk[:len(stk) - 1]
            }

            stk = append(stk, c)
            added[c - 'a'] = true
        }
        cnt[c - 'a'] --;
    }

    return string(stk)
}
```



## [318. Maximum Product of Word Lengths](https://leetcode.cn/problems/maximum-product-of-word-lengths/)

*Bitwise + State Compression*

#### Java

```java
class Solution {
    private int getState(String word) {
        int st = 0;
        for (char c : word.toCharArray()) {
            int d = (int)(c - 'a');
            st = st | (1 << d);
        }
        return st;
    }

    public int maxProduct(String[] words) {
        int[] states = Arrays.stream(words).map(word -> getState(word)).mapToInt(x->x).toArray();
        int n = words.length;
        int ans = 0;
        for (int i = 0; i < n; i ++) {
            for (int j = i + 1; j < n; j ++) {
                if ((states[i] & states[j]) == 0) ans = Math.max(ans, words[i].length() * words[j].length());
            }
        }
        return ans;
    }
}
```



#### Go

使用Hash压缩更多的状态

```go
func maxProduct(words []string) int {
    m := map[int]int{}
    for _, word := range words {
        st := 0
        for _, c := range word {
            st |= 1 << (c - 'a')
        }
        if len(word) > m[st] {
            m[st] = len(word)
        }
    }
    ans := 0
    for st1, len1 := range m {
        for st2, len2 := range m {
            if st1 & st2 == 0 && len1 * len2 > ans {
                ans = len1 * len2
            }
        }
    }
    return ans
}
```



## [319. Bulb Switcher](https://leetcode.cn/problems/bulb-switcher/)

#### Java

考虑第n行中第i个灯(1 <= i <= n), 如果存在d<i并且d|i, 则(i/d)|i, 如果d==(i/d), 则第i位置得以保留, 不然, d和(i/d)成对出现, i会熄灭

于是答案即为1...n中的完全平方数的个数

```java
class Solution {
    public int bulbSwitch(int n) {
        int cnt = 0;
        for (int i = 1; i * i <= n; i ++) cnt ++;
        return cnt;
    }
}
```



## [322. Coin Change](https://leetcode.com/problems/coin-change/) [DP-Knapsack]





## [324. Wiggle Sort II](https://leetcode.cn/problems/wiggle-sort-ii/) **

```
难点在于如何 "in-place"

当n为偶数时, 要把索引 i 元素移动到 j 处:
0 -> 0

1 -> 2
2 -> 4
3 -> 6
...
n/2 - 1 -> n - 2

n/2     -> 1
n/2 + 1 -> 3
...
n - 2   -> n - 3

n - 1   -> n - 1

idx -> 2 * idx < n ? 2 * idx : 2 * idx - n + 1

当n为奇数
0 -> 0

1 -> 2
2 -> 4
3 -> 6
...
(n-1)/2     -> n - 1
(n-1)/2 + 1 -> 1
(n-1)/2 + 2 -> 3
...
n - 1 -> n - 2

idx -> 2 * idx < n ? 2 * idx : 2 * idx - n
```

```java
class Solution {
    private void rolling(int[] nums, int idx) {
        int n = nums.length;
        int i = idx;
        int t = nums[i];
        while (true) {
            int ni = 2 * i;
            if (ni >= n) {
                if ((n & 1) == 0) ni = ni - n + 1;
                else ni = ni - n;
            }

            int tt = nums[ni];
            nums[ni] = t;
            t = tt;
            i = ni;
            if (i == idx) break;
        }
    }

    public void wiggleSort(int[] nums) {
        Arrays.sort(nums);
        int n = nums.length;
        rolling(nums, 1);
        rolling(nums, n / 2);
    }
}
```



## [326. Power of Three](https://leetcode.com/problems/power-of-three/)

#### Go

*Math*

```go
func isPowerOfThree(n int) bool {
    return n > 0 && 1162261467 % n == 0 // 1162261467 = 3 ^ 19 是小于 MaxInt 的最大值
}
```

```
判断是否是 4 的幂则不能这样写: 4^M / 2^n (误判)

只有当问题是 "判断是否是 p 的幂" p 为素数, 才可以这样写
```



## [328. Odd Even Linked List](https://leetcode.cn/problems/odd-even-linked-list/)

#### C++

```c++
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        ListNode* odd = new ListNode();
        ListNode* even = new ListNode();
        ListNode* oddp = odd;
        ListNode* evenp = even;

        while (head) {
            oddp->next = head;
            head = head->next;

            oddp = oddp->next;
            oddp->next = nullptr;

            if (!head) break;
            evenp->next = head;
            head = head->next;

            evenp = evenp->next;
            evenp->next = nullptr;
        }

        oddp->next = even->next;
        even->next = nullptr;
        delete even;

        ListNode* ans = odd->next;
        odd->next = nullptr;
        delete odd;
        return ans;
    }
};
```



#### Go

```go
func oddEvenList(head *ListNode) *ListNode {
    odd  := new(ListNode)
    oddPtr := odd

    even := new(ListNode)
    evenPtr := even

    for head != nil {
        oddPtr.Next = head
        head = head.Next

        oddPtr = oddPtr.Next
        oddPtr.Next = nil
        if head != nil {
            evenPtr.Next = head
            head = head.Next

            evenPtr = evenPtr.Next
            evenPtr.Next = nil
        }
    }
    oddPtr.Next = even.Next
    return odd.Next
}
```



## [331. Verify Preorder Serialization of a Binary Tree](https://leetcode.cn/problems/verify-preorder-serialization-of-a-binary-tree/)

#### Java

```java
class Solution {
    public boolean isValidSerialization(String preorder) {
        int l = 0;
        for (int i = preorder.length() - 1; i > -1; i --) {
            char c = preorder.charAt(i);
            if (c == ',') continue;
            if (c == '#') l ++;
            else {
                if (i < preorder.length() - 1 && preorder.charAt(i + 1) != ',') continue; // 排除遇到两位数
                if (l < 2) return false;
                l --;
            }
        }
        return l == 1;
    }
}
```

```
相当于维护一个栈, l 是栈的长度

9,3,4,#,#,1,#,#,2,#,6,#,#
从后往前遍历, 遇到 # 就加入, 遇到数字就弹出两个#, 再压入一个 # (相当于删除已经 ok 的节点)

[ # # ] <- 6
[ # ] <- #
[ # # ] <- 2
[ # ] <- # #
[ # # # ] <- 1
[ # # ] <- # #
[ # # # # ] <- 4
[ # # # ] <- 3
[ # # ] <- 1
[ # ] ok!
```



#### Go

```go
func isValidSerialization(preorder string) bool {
    l := 0
    for i := len(preorder) - 1; i > -1; i -- {
        if preorder[i] == ',' {
            continue
        } else if preorder[i] == '#' {
            l ++
        } else {
            if i < len(preorder) - 1 && preorder[i + 1] != ',' {
                continue
            }
            if l < 2 {
                return false
            }
            l --
        }
    }
    return l == 1
}
```





## [332. Reconstruct Itinerary](https://LeetCode.com/problems/reconstruct-itinerary/) [Backtrack]



## [334. Increasing Triplet Subsequence](https://leetcode.com/problems/increasing-triplet-subsequence/)

#### Go

*Greedy*

```go
func increasingTriplet(nums []int) bool {
    if len(nums) < 3 {
        return false
    }
    first := nums[0]
    second := math.MaxInt
    for i := 1; i < len(nums); i ++ {
        if (nums[i] > second) {
            return true
        } else if (nums[i] > first) { // 保持 first < second
            second = nums[i]
        } else { // nums[i] <= first < second
            first = nums[i]
        }
    }
    return false
}
```

```
nums[i] \in (-\infty, first] first = nums[i]
nums[i] \in (first, second] second = nums[i]
nums[i] \in (second, +\infty) return true
```



*Prefix Min / Max* just like Prefix Sum

```go
func increasingTriplet(nums []int) bool {
    leftMin := make([]int, len(nums)) // leftMin[i] : nums[0..=i]最小值
    rightMax := make([]int, len(nums)) // rightMax[i] : nums[i..=len - 1] 最小值
    for i := 0; i < len(nums); i ++ {
        if i == 0 || leftMin[i - 1] > nums[i] {
            leftMin[i] = nums[i]
        } else {
            leftMin[i] = leftMin[i - 1]
        }
    }

    for i := len(nums) - 1; i > -1; i -- {
        if i == len(nums) - 1 || rightMax[i + 1] < nums[i] {
            rightMax[i] = nums[i]
        } else {
            rightMax[i] = rightMax[i + 1]
        }
    }


    for i := 1; i <= len(nums) - 2; i ++ {
        if leftMin[i - 1] < nums[i] && nums[i] < rightMax[i + 1] { // **
            return true
        }
    }
    return false
}
```



## [337. House Robber III](https://leetcode.com/problems/house-robber-iii/) [DP]



## [338. Counting Bits](https://leetcode.com/problems/counting-bits/)

#### Go

*DP*

```go
func countBits(n int) (ans []int) {
    mask := 0
    ans = make([]int, n + 1)
    for i := 1; i <= n; i ++ {
        if i & (i - 1) == 0 {
            mask = i
        }
        ans[i] = 1 + ans[i % mask]
    }
    return
}
```

```
n | bits | num of 1
0 | 0000 | 0
------------
1 | 0001 | 1 = 1 + 0 = 1 + ans[1 % 1]
------------
2 | 0010 | 1 = 1 + 0 = 1 + ans[2 % 2]
3 | 0011 | 2 = 1 + 1 = 1 + ans[3 % 2]
------------
4 | 1000 | 1 = 1 + 0 = 1 + ans[4 % 4]
5 | 1001 | 2 = 1 + 1 = 1 + ans[5 % 4]
...
```



## [341. Flatten Nested List Iterator](https://leetcode.com/problems/flatten-nested-list-iterator/)

#### Java

```java
public class NestedIterator implements Iterator<Integer> {
    private List<Integer> list;
    private Iterator<Integer> itr;
    private void foo(List<NestedInteger> nestedList) {
        for (NestedInteger c : nestedList) {
            if (c.isInteger()) list.add(c.getInteger());
            else foo(c.getList());
        }
    }

    public NestedIterator(List<NestedInteger> nestedList) {
        list = new ArrayList<>();
        foo(nestedList);
        itr = list.iterator();
    }

    @Override
    public Integer next() { return itr.next(); }

    @Override
    public boolean hasNext() { return itr.hasNext(); }
}

```



#### Go

```go
type NestedIterator struct {
    list []int
}

func Constructor(nestedList []*NestedInteger) *NestedIterator {
    list := []int{}
    var flap func(nestedList []*NestedInteger)
    flap = func(nestedList []*NestedInteger) {
        if nestedList == nil {
            return
        }
        for _, c := range nestedList {
            if c.IsInteger() {
                list = append(list, c.GetInteger())
            } else {
                flap(c.GetList())
            }
        }
    }
    flap(nestedList)
    return &NestedIterator{ list }
}

func (this *NestedIterator) Next() int {
    defer func(){ this.list = this.list[1:]} ()
    return this.list[0]
}

func (this *NestedIterator) HasNext() bool {
    return len(this.list) > 0
}
```



## [342. Power of Four](https://leetcode.com/problems/power-of-four/)

联系 [231. Power of Two](https://leetcode.com/problems/power-of-two/)

#### Java

```java
class Solution {
    public boolean isPowerOfFour(int n) {
        if (n <= 0) return false;
        if (n == 1) return true;
        if (n % 4 != 0) return false;
        else return isPowerOfFour(n / 4);
    }
}
```



#### C++

*Bitwise*

```c++
class Solution {
public:
    bool isPowerOfFour(int n) {
        if (n <= 0) return false;
        int mask = 0b01010101010101010101010101010101;
        return (n & -n) == n && (n & mask) == n; // 位运算优先级特别低, 一定记得打括号
    }
};
```



```c++
class Solution {
public:
    bool isPowerOfFour(int n) {
        if (n <= 0) return false;
        int mask = 0x55555555; // 也可以用 16 进制来写 mask
        return (n & (n - 1)) == 0 && (n & mask) == n;
    }
};
```



## [343. Integer Break](https://leetcode.com/problems/integer-break/) [DP]



## [344. Reverse String](https://leetcode.com/problems/reverse-string/) [String]



## [345. Reverse Vowels of a String](https://leetcode.com/problems/reverse-vowels-of-a-string/)

#### Java

```java
class Solution {
    public String reverseVowels(String s) {
        StringBuilder sb = new StringBuilder(s);
        int l = 0, r = sb.length() - 1;
        while (l < r) {
            char lc = sb.charAt(l), rc = sb.charAt(r);
            if (!isVowel(lc)) l ++;
            if (!isVowel(rc)) r --;
            if (isVowel(lc) && isVowel(rc)) {
                sb.setCharAt(l, rc);
                sb.setCharAt(r, lc);
                l ++; r --;
            }
        }
        return sb.toString();
    }

    boolean isVowel(char c) {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u' || c == 'A' || c == 'E' || c == 'I' || c == 'O' || c == 'U';
    }
}
```



#### Go

```go
func reverseVowels(s string) string {
    ss := []rune(s)
    l, r := 0, len(ss) - 1
    isVowel := func(c rune) bool {
        switch c {
        case 'a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U':
            return true
        default:
            return false
        }
    }
    for l < r {
        if !isVowel(ss[l]) {
            l ++
        }
        if !isVowel(ss[r]) {
            r --
        }

        if isVowel(ss[l]) && isVowel(ss[r]) {
            t := ss[l]
            ss[l] = ss[r]
            ss[r] = t
            l ++
            r --
        }
    }
    return string(ss)
}
```



## [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/) [Stack-Queue]



## [349. Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/) [Hash]



## [350. Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/)

#### Java

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        List<Integer> union = new ArrayList<>();
        for (int num : nums1) map.put(num, map.getOrDefault(num, 0) + 1);
        for (int num : nums2) {
            if (map.containsKey(num)) {
                union.add(num);
                map.put(num, map.get(num) - 1);
                if (map.get(num) == 0) map.remove(num);
            }
        }
        return union.stream().mapToInt(x -> x).toArray();
    }
}
```



#### Go

```go
func intersect(nums1 []int, nums2 []int) []int {
    mp := map[int]int{}
    union := []int{}
    for _, num := range nums1 {
        if _, ok := mp[num]; ok {
            mp[num] ++;
        } else {
            mp[num] = 1
        }
    }
    for _, num := range nums2 {
        if _, ok := mp[num]; ok {
            union = append(union, num)
            mp[num] --
            if mp[num] == 0 {
                delete(mp, num)
            }
        }
    }
    return union
}
```


