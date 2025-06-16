---
title: LeetCode 3001-
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

## [3217. Delete Nodes From Linked List Present in Array](https://leetcode.com/problems/delete-nodes-from-linked-list-present-in-array/)

#### Java

```java
class Solution {
    public ListNode modifiedList(int[] nums, ListNode head) {
        kills = new HashSet<>();
        for (int num : nums) kills.add(num);
        return remove(head);
    }
    private Set<Integer> kills;
    private ListNode remove(ListNode head) {
        if (head == null) return head;
        if (kills.contains(head.val)) return remove(head.next);
        else {
            head.next = remove(head.next);
            return head;
        }
    }
}
```



#### Go

```go
func modifiedList(nums []int, head *ListNode) *ListNode {
    mp := map[int]bool{}
    for _, num := range nums {
        mp[num] = true
    }
    var delNode func(*ListNode) *ListNode
    delNode = func(head *ListNode) *ListNode {
        if head == nil {
            return head
        }
        if ok, _ := mp[head.Val]; ok {
            return delNode(head.Next)
        } else {
            head.Next = delNode(head.Next)
            return head
        }
    }
    return delNode(head)
}
```



#### Rust

```rust
use std::collections::HashSet;
impl Solution {
    pub fn modified_list(nums: Vec<i32>, head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut kills: HashSet<i32> = HashSet::new();
        for num in nums {
            kills.insert(num);
        }
        println!("{:?}", kills);
        Solution::remove_node(head, &kills)
    }

    fn remove_node(head: Option<Box<ListNode>>, kills: &HashSet<i32>) -> Option<Box<ListNode>> {
        match head {
            None => None,
            Some(mut node) => {
                if kills.contains(&node.val) {
                    Solution::remove_node(node.next, kills)
                } else {
                    node.next = Solution::remove_node(node.next, kills);
                    Some(node)
                }
            }
        }
    }
}
```



## [3319. K-th Largest Perfect Subtree Size in Binary Tree](https://leetcode.cn/problems/k-th-largest-perfect-subtree-size-in-binary-tree/)

#### Java

```java
class Solution {
    public int kthLargestPerfectSubtree(TreeNode root, int k) {
        Queue<Integer> heap = new PriorityQueue<>();
        dfs(heap, k, root);
        return heap.size() == k ? heap.poll() : -1;
    }

    private int dfs(Queue<Integer> heap, int k, TreeNode root) {
        if (root == null) return 0;
        int lsz = dfs(heap, k, root.left), rsz = dfs(heap, k, root.right);

        int sz = -1;
        if (lsz != -1 && rsz != -1 && lsz == rsz) {
            sz = lsz + rsz + 1;
            heap.offer(sz);
            if (heap.size() > k) heap.poll();
        }
        return sz;
    }
}
```



## [LCP 03. 机器人大冒险](https://leetcode.cn/problems/programmable-robot/)

#### C++

```c++
class Solution {
    long state(int x, int y) { // 使用位运算来压缩坐标状态
        long st = ((long)y << 30);
        st |= (long)x;
        return st;
    }
public:
    bool robot(string command, vector<vector<int>>& obstacles, int tx, int ty) {
        unordered_set<long> uset;
        for (auto& obstacle : obstacles) {
            uset.insert(state(obstacle[0], obstacle[1]));
        }
        int x = 0, y = 0;
        while (true) {
            for (char c : command) {
                if (x == tx && y == ty) return true;
                if (c == 'U') y ++;    
                else x ++;
                if (x > tx || y > ty || uset.find(state(x, y)) != uset.end()) return false;
            }
        }
    }
};
```

优化版本

```c++
class Solution {
public:
    bool robot(string command, vector<vector<int>>& obstacles, int tx, int ty) {
        unordered_set<long> uset; // 记录robot在一次循环中可以到达的所有位置
        int x = 0, y = 0;
        uset.insert(0);
        for (char c : command) {
            if (c == 'U') y ++;
            else x ++;
            uset.insert(((long)x << 30) | y); // 这里一定要把 x 转成 long, 不然会精度丢失
        } // x, y 代表一次循环后的最远位置
        int d = min(tx / x, ty / y); // 将 tx, ty 压缩至 x, y 之内, 这里严谨的话要判断 x, y 是否为0
        int px = tx - x * d;
        int py = ty - y * d;
        if (uset.find(((long)px << 30) | py) == uset.end()) return false;
        
        for (auto& obstacle : obstacles) {
            int ox = obstacle[0], oy = obstacle[1];
            if (ox > tx || oy > ty) continue;
            d = min(ox / x, oy / y); // 将 ox, oy 压缩至 x, y 之内
            px = ox - x * d;
            py = oy - y * d;
            if (uset.find(((long)px << 30) | py) != uset.end()) return false;
        }

        return true;
    }
};
```



## [LCP 07. 传递信息](https://leetcode.cn/problems/chuan-di-xin-xi/)

#### C++

*BFS*

```c++
class Solution {
public:
    int numWays(int n, vector<vector<int>>& relation, int k) {
        vector<vector<int>> adj(n, vector<int>{});
        for (auto& r : relation) adj[r[0]].push_back(r[1]);
        queue<int> q;
        q.push(0);
        while (k > 0) {
            int sz = q.size();
            for (int i = 0; i < sz; i ++) {
                int u = q.front(); q.pop();
                for (int v : adj[u]) q.push(v);
            }
            k --;
        }
        int cnt = 0;
        while (!q.empty()) {
            if (q.front() == n - 1) cnt ++;
            q.pop();
        }
        return cnt;
    }
};
```



## [LCP 08. 剧情触发时间](https://leetcode.cn/problems/ju-qing-hong-fa-shi-jian/)

#### C++

尽管超时, 但也很重要, 使用了搜索最左边的二分查找

```c++
class Solution {
    int bs(vector<int> arr, int tar) {
        int l = 0, r = arr.size() - 1;
        while (l <= r) {
            int m = l + ((r - l) >> 1);
            if (arr[m] >= tar) r = m - 1;
            else l = m + 1;
        }
        return l;
    }
public:
    vector<int> getTriggerTime(vector<vector<int>>& increase, vector<vector<int>>& requirements) {
        vector<vector<int>> states(3, vector<int>(increase.size() + 1)); // 3行, 每一行分别代表 n+1 天的C R H
        for (int i = 0; i < increase.size(); i ++) {
            for (int j = 0; j < 3; j ++) states[j][i + 1] = states[j][i] + increase[i][j];
        }
        vector<int> triggerTime(requirements.size(), -1);
        for (int i = 0; i < requirements.size(); i ++) {
            int time = -1;
            for (int j = 0; j < 3; j ++) {
                int t = bs(states[j], requirements[i][j]);
                time = max(time, t);
            }
            if (time < states[0].size()) triggerTime[i] = time;
        }
        return triggerTime;
    }
};
```



## [LCP 08. 剧情触发时间](https://leetcode.cn/problems/ju-qing-hong-fa-shi-jian/)

#### Java

```java
class Solution {
    public int[] getTriggerTime(int[][] increase, int[][] requirements) {
        int rqN = requirements.length;
        int[] triggerTime = new int[rqN];
        int[] triggleCnt = new int[rqN];
        
        int[][] zippedRq = new int[rqN][4];
        for (int rqId = 0; rqId < rqN; rqId ++) {
            zippedRq[rqId][0] = requirements[rqId][0];
            zippedRq[rqId][1] = requirements[rqId][1];
            zippedRq[rqId][2] = requirements[rqId][2];
            zippedRq[rqId][3] = rqId;
        }

        for (int t = 0; t < 3; t ++) {
            final int sortType = t;
            Arrays.sort(zippedRq, (a1, a2)-> a1[sortType] - a2[sortType]);            
            int sum = 0;
            int zpRqId = 0;
            for (int day = 0; day <= increase.length; day ++) {
                while (zpRqId < rqN && sum >= zippedRq[zpRqId][t]) {
                    int rqId = zippedRq[zpRqId][3];
                    triggerTime[rqId] = Math.max(day, triggerTime[rqId]);
                    triggleCnt[rqId] ++;
                    zpRqId ++;
                }
                if (day < increase.length) sum += increase[day][t];
            }
        }
        for (int rqId = 0; rqId < rqN; rqId ++) {
            if (triggleCnt[rqId] != 3) triggerTime[rqId] = -1;
        }
        return triggerTime;
    }
}
```

