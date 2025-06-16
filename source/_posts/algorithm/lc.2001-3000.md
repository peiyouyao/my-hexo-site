---
title: LeetCode 2001-3000
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---



## [2028. Find Missing Observations](https://leetcode.com/problems/find-missing-observations/)

```java
class Solution {
    public int[] missingRolls(int[] rolls, int mean, int n) {
        int m = rolls.length;
        int tar = mean * (m + n) - Arrays.stream(rolls).sum();
        return foo(tar, n);
    }
    private int[] foo(int tar, int n) {
        if (tar < n * 1 || tar > n * 6) return new int[0];
        int[] ret = new int[n];
        int q = tar / n;
        int r = tar - n * q;
        Arrays.fill(ret, q);
        int idx = 0;
        while (r > 0) {
            ret[idx] ++;
            r --;
            idx ++;
        }
        return ret;
    }
}
```



## [2058. Find the Minimum and Maximum Number of Nodes Between Critical Points](https://leetcode.cn/problems/find-the-minimum-and-maximum-number-of-nodes-between-critical-points/)

#### C++

```c++
class Solution {
public:
    vector<int> nodesBetweenCriticalPoints(ListNode* head) {
        int minDist = INT_MAX;
        int maxDist = -1;
        int firstPos = -1, lastPos = -1;
        int pos = 0;
        while (head && head->next && head->next->next) {
            int p = head->val;
            int c = head->next->val;
            int n = head->next->next->val;
            if (c < p && c < n || c > p && c > n) {
                if (firstPos == -1) firstPos = pos;
                else minDist = min(minDist, pos - lastPos);
                lastPos = pos;
            }
            head = head->next;
            pos ++;
        }
        if (minDist == INT_MAX) return {-1, -1};
        maxDist = lastPos - firstPos;
        return {minDist, maxDist};
    }
};
```



## [2074. Reverse Nodes in Even Length Groups](https://leetcode.cn/problems/reverse-nodes-in-even-length-groups/)

#### C++

```c++
class Solution {
    void revlst(ListNode* head, ListNode* l, ListNode* r) { // 原地翻转l,r之间的链表(不包括l,r)
        if (!l || !l->next) return;
        ListNode* rev = l;
        ListNode* p = l->next;
        while (p != r) {
            ListNode* t = p->next;
            p->next = rev;
            rev = p;
            p = t;
        }
        l->next->next = r;
        l->next = rev;
    }
public:
    ListNode* reverseEvenLengthGroups(ListNode* head) {
        ListNode* dummy = new ListNode(0, head);
        ListNode* l = dummy;
        for (int gsz = 1; l; gsz ++) {
            ListNode* r = l->next;
            int sz = 0;
            for (int i = 0; i < gsz; i ++) {
                if (!r) break;
                sz ++;
                r = r->next;
            }
            if ((sz & 1) == 0) revlst(dummy, l, r);
            for (int i = 0; i < gsz; i ++) {
                if (!l) break;
                l = l->next;
            }
        }
        ListNode* ans = dummy->next;
        delete dummy;
        return ans;
    }
};
```



#### Go

```go
func reverseEvenLengthGroups(head *ListNode) *ListNode {
    revlst := func(head, l, r *ListNode) {
        if l == nil || l.Next == nil {
            return
        }
        rev := l
        p := l.Next
        for p != r {
            nxt := p.Next
            p.Next = rev
            rev = p
            p = nxt
        }
        l.Next.Next = r
        l.Next = rev
    }
    dummy := &ListNode{0, head}
    l := dummy
    for gsz := 1; l != nil; gsz ++ {
        r := l.Next
        sz := 0
        for i := 0; i < gsz; i ++ {
            if r == nil {
                break
            }
            sz ++
            r = r.Next
        }
        if sz & 1 == 0 {
            revlst(dummy, l, r)
        }
        for i := 0; i < gsz; i ++ {
            if l == nil {
                break
            }
            l = l.Next
        }
    }
    return dummy.Next
}
```





## [2090. K Radius Subarray Averages](https://leetcode.cn/problems/k-radius-subarray-averages/)

#### Go

```go
func getAverages(nums []int, k int) []int {
    avgs := make([]int, len(nums))
    for i := range avgs {
        avgs[i] = -1
    }
    sum := 0
    for i, num := range nums {
        sum += num
        if i - (2 * k) >=0 {
            avgs[i - k] = sum / (2 * k + 1)
            sum -= nums[i - (2 * k)]
        }
    }
    return avgs
}
```



## [2095. Delete the Middle Node of a Linked List](https://leetcode.cn/problems/delete-the-middle-node-of-a-linked-list/)

#### C++

```c++
class Solution {
public:
    ListNode* deleteMiddle(ListNode* head) {
        ListNode* dummy = new ListNode(0, head);
        ListNode* slow = dummy;
        ListNode* fast = dummy->next;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        ListNode* del = slow->next;
        slow->next = del->next;
        // delete del;
        head = dummy->next;
        delete dummy;
        return head;
    }
};
```



## [2130. Maximum Twin Sum of a Linked List](https://leetcode.cn/problems/maximum-twin-sum-of-a-linked-list/)

#### Go

```go
func pairSum(head *ListNode) int {
    s, f := head, head.Next
    for f != nil && f.Next != nil {
        s = s.Next
        f = f.Next.Next
    }
    back := s.Next
    s.Next = nil
    var rev *ListNode = nil
    for back != nil {
        nxt := back.Next
        back.Next = rev
        rev = back
        back = nxt
    }
    ans := 0
    for head != nil {
        if head.Val + rev.Val > ans {
            ans = head.Val + rev.Val
        }
        head = head.Next
        rev = rev.Next
    }
    return ans
}
```



## [2181. Merge Nodes in Between Zeros](https://leetcode.cn/problems/merge-nodes-in-between-zeros/)

#### C++

新建链表, 慢

```c++
class Solution {
public:
    ListNode* mergeNodes(ListNode* head) {
        head = head->next;
        ListNode* ans = new ListNode();
        ListNode* p = ans;
        int sum = 0;
        while (head) {
            if (head->val == 0) {
                p->next = new ListNode(sum);
                p = p->next;
                sum = 0;
            } else sum += head->val;
            head = head->next;
        }
        p = ans->next;
        ans->next = nullptr;
        delete ans;
        return p;
    }
};
```

原地修改, 快

```c++
class Solution {
public:
    ListNode* mergeNodes(ListNode* head) {
        ListNode* p = head->next;
        ListNode* p0 = head;
        int sum = 0;
        while (p) {
            if (p->val == 0) {
                p->val = sum;
                p0->next = p;
                p0 = p;
                sum = 0;
            } else sum += p->val;
            p = p->next;
        }
        return head->next;
    }
};
```





## [2220. Minimum Bit Flips to Convert Number](https://leetcode.com/problems/minimum-bit-flips-to-convert-number/)

同 [461. Hamming Distance](https://leetcode.com/problems/hamming-distance/) 汉明距离

```java
class Solution {
    public int minBitFlips(int start, int goal) {
        int xor = start ^ goal;
        int cnt = 0;
        for (int i = 0; i < 32; i ++) {
            cnt += ((xor >>> i) & 1);
        }
        return cnt;
    }
}
```



## [2265. Count Nodes Equal to Average of Subtree](https://leetcode.com/problems/count-nodes-equal-to-average-of-subtree/)

#### Go

```go
func averageOfSubtree(root *TreeNode) int {
    ans := 0
    var dfs func(*TreeNode) (int, int)
    dfs = func(root *TreeNode) (cnt, sum int) {
        cnt = 0
        sum = 0
        if root == nil { return }
        lcnt, lsum := dfs(root.Left)
        rcnt, rsum := dfs(root.Right)
        cnt = lcnt + rcnt + 1
        sum = lsum + rsum + root.Val
        if root.Val == sum / cnt { ans ++ }
        return
    }
    dfs(root)
    return ans
}
```



## [2266. Count Number of Texts](https://leetcode.com/problems/count-number-of-texts/)

*Math + DP*

``` 
结果无法是一些 dp[i_{1}] * dp[i_{2}] * ... * dp[i_{s}]
每一个 dp[i_{a}] 又可以写成一些 dp[i_{a, 1}] + dp[i_{a, 2}] + ... + dp[i_{a, t}] 相加

因此事实上, 结果就是一些 dp[i] 相乘或相加

只要说明

(a % M) + (b % M) = (a + b) % M
(a % M) * (b % M) = (a*b) % M

即可
```



#### Java

```java
class Solution {
    public int countTexts(String pressedKeys) {
        int M = 1000000007;
        int L = Math.max(pressedKeys.length(), 4); // 生成足够的 DP 数组长度
        long[] dp3 = new long[L + 1]; // 1. 使用 long; 2. 注意 L + 1
        dp3[0] = 1L; dp3[1] = 1L; dp3[2] = 2L;
        for (int i = 3; i <= L; i ++) {
            dp3[i] = dp3[i - 3] + dp3[i - 2] + dp3[i - 1];
            dp3[i] %= M; // 这里要取模
        }

        long[] dp4 = new long[L + 1];
        dp4[0] = 1L; dp4[1] = 1L; dp4[2] = 2L; dp4[3] = 4L;
        for (int i = 4; i <= L; i ++) {
            dp4[i] = dp4[i - 4] + dp4[i - 3] + dp4[i - 2] + dp4[i - 1];
            dp4[i] %= M;
        }

        int cnt = 0;
        long ans = 1; // 使用 long
        for (int i = 0; i < pressedKeys.length(); i ++) {
            cnt ++;
            if (i == pressedKeys.length() - 1 || pressedKeys.charAt(i) != pressedKeys.charAt(i + 1)) {
                if (pressedKeys.charAt(i) == '7' || pressedKeys.charAt(i) == '9') ans *= dp4[cnt];
                else ans *= dp3[cnt];
                ans %= M; // 这里也要取模
                cnt = 0;
            }
        }
        return (int)ans;
    }
}
```



#### Go

```go
func countTexts(s string) int {
    var (
        M int64 = 1000000007
        L = len(s)
        ans int64 = 1
    )
    if L < 4 {
        L = 4
    }

    dp3 := make([]int64, L + 1)
    dp3[0] = 1
    dp3[1] = 1
    dp3[2] = 2
    for i := 3; i <= L; i ++ {
        dp3[i] = dp3[i - 1] + dp3[i - 2] + dp3[i - 3]
        dp3[i] %= M
    }

    dp4 := make([]int64, L + 1)
    dp4[0] = 1
    dp4[1] = 1
    dp4[2] = 2
    dp4[3] = 4
    for i := 4; i <= L; i ++ {
        dp4[i] = dp4[i - 1] + dp4[i - 2] + dp4[i - 3] + dp4[i - 4]
        dp4[i] %= M
    }


    cnt := 0
    for i := 0; i < len(s); i ++ {
        cnt ++
        if i == len(s) - 1 || s[i] != s[i + 1] {
            if s[i] == '7' || s[i] == '9' {
                ans *= dp4[cnt]
            } else {
                ans *= dp3[cnt]
            }
            ans %= M
            cnt = 0
        }
    }

    return int(ans)
}
```



## [2326. Spiral Matrix IV](https://leetcode.com/problems/spiral-matrix-iv/)

```java
class Solution {
    public int[][] spiralMatrix(int m, int n, ListNode head) {
        int[][] ans = new int[m][n];
        for (int[] row : ans) Arrays.fill(row, -1);
        int[] di = {0, 1, 0, -1}, dj = {1, 0, -1, 0};
        int d = 0;
        int i = 0, j = 0;
        while (head != null) {
            ans[i][j] = head.val;
            head = head.next;
            int ni = i + di[d], nj = j + dj[d];
            if (ni == -1 || ni == m || nj == -1 || nj == n || ans[ni][nj] != -1) {
                d = (d + 1) % 4;
                ni = i + di[d];
                nj = j + dj[d];
            }
            i = ni;
            j = nj;
        }
        return ans;
    }
}
```

###### Rust

```rust
impl Solution {
    pub fn spiral_matrix(m: i32, n: i32, mut head: Option<Box<ListNode>>) -> Vec<Vec<i32>> {
        let mut ans: Vec<Vec<i32>> = vec![vec![-1; n as usize]; m as usize];
        let di: [i32; 4] = [0, 1, 0, -1];
        let dj: [i32; 4] = [1, 0, -1, 0];
        let mut i: i32 = 0;
        let mut j: i32 = 0;
        let mut d: i32 = 0;
        while head.as_ref().is_some() {
            ans[i as usize][j as usize] = head.as_ref().unwrap().val;
            head = head.unwrap().next;
            let mut ni = i + di[d as usize];
            let mut nj = j + dj[d as usize];
            if ni == -1 || ni == m || nj == -1 || nj == n || ans[ni as usize][nj as usize] != -1 {
                d = (d + 1) % 4;
                ni = i + di[d as usize];
                nj = j + dj[d as usize];
            }
            i = ni;
            j = nj;
        }
        ans
    }
}
```



## [2331. Evaluate Boolean Binary Tree](https://leetcode.com/problems/evaluate-boolean-binary-tree/)

#### C++

```c++
class Solution {
public:
    bool evaluateTree(TreeNode* root) {
        if (root->val == 0) return false;
        else if (root->val == 1) return true;
        else {
            bool left = evaluateTree(root->left);
            bool right = evaluateTree(root->right);
            if (root->val == 2) return left || right;
            else return left && right;
        }
    }
};
```



## [2379. Minimum Recolors to Get K Consecutive Black Blocks](https://leetcode.cn/problems/minimum-recolors-to-get-k-consecutive-black-blocks/)

#### Go

```go
func minimumRecolors(blocks string, k int) int {
    cnt := 0
    maxCnt := 0
    for i, c := range blocks {
        if c == 'B' {
            cnt ++
        }
        if i >= k - 1 {
            if maxCnt < cnt {
                maxCnt = cnt
                if maxCnt == k {
                    return 0
                }
            }
            if blocks[i - (k - 1)] == 'B' && cnt > 0 {
                cnt --
            }
        }
    }
    return k - maxCnt
}
```



## [2419. Longest Subarray With Maximum Bitwise AND](https://leetcode.com/problems/longest-subarray-with-maximum-bitwise-and/)

> **原理**: 
>
> 按位与运算只会让数字越来越小
>
> 所以按位与结果最大的子串, 必然是一个连续的, 重复元素的子串
>
> 而其中的元素就是 nums 中的最大值 (max)
>
> 故算法只要确定max 连续出现的最大次数即可
>
> 以下一两种方法确定连续元素出现的最大次数

```java
class Solution {
    public int longestSubarray(int[] nums) {
        int max = Arrays.stream(nums).max().getAsInt();
        int i = -1, j = 0;
        int ans = 1;
        for (; j < nums.length; j ++) {
            if (nums[j] != max) {
                ans = Math.max(ans, j - i - 1);
                i = j;
            }
        }
        ans = Math.max(ans, j - i - 1);
        return ans;
    }
}
```

```java
class Solution {
    public int longestSubarray(int[] nums) {
        int max = Arrays.stream(nums).max().getAsInt();
        int cnt = 0, res = 0;
        for (int i = 0; i < nums.length; i ++) {
            if (nums[i] == max) {
                cnt ++;
                res = Math.max(cnt, res);
            } else cnt = 0;
        }
        return res;
    }
}
```



## [2487. Remove Nodes From Linked List](https://leetcode.cn/problems/remove-nodes-from-linked-list/)

#### C++

```c++
class Solution {
public:
    ListNode* removeNodes(ListNode* head) {
        vector<ListNode*> stk;
        while (head) {
            while (!stk.empty() && stk.back()->val < head->val) {
                // delete stk.back();
                stk.pop_back();
            }
            stk.push_back(head);
            head = head->next;
        }
        ListNode* p = stk[0];
        for (int i = 1; i < stk.size(); i ++) {
            p->next = stk[i];
            p = p->next;
        }
        p->next = nullptr;
        return stk[0];
    }
};
```



#### Go

```go
func removeNodes(head *ListNode) *ListNode {
    stk := []*ListNode{}
    for head != nil {
        for len(stk) > 0 && stk[ len(stk)-1 ].Val < head.Val {
            stk = stk[: len(stk)-1 ]
        }
        stk = append(stk, head)
        head = head.Next
    }
    p := stk[0]
    for i := 1; i < len(stk); i ++ {
        p.Next = stk[i]
        p = p.Next
    }
    p.Next = nil
    return stk[0]
}
```



## [2807. Insert Greatest Common Divisors in Linked List](https://leetcode.com/problems/insert-greatest-common-divisors-in-linked-list/)

#### Java

```java
class Solution {
    public ListNode insertGreatestCommonDivisors(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode p = head;
        while (p != null && p.next != null) {
            insert(p, p.next);
            p = p.next.next;
        }
        return head;
    }
    private void insert(ListNode p, ListNode q) {
        ListNode node = new ListNode(gcd(p.val, q.val), q);
        p.next = node;
    }

    private int gcd(int x, int y) {
        return y > 0 ? gcd(y, x % y) : x;
    }
}
```

#### Rust

```rust
impl Solution {
    pub fn insert_greatest_common_divisors(
        mut head: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        let mut p = &mut head;
        while p.as_ref().is_some() && p.as_ref().unwrap().next.is_some() {
            Solution::insert_node(p);
            p = &mut p.as_mut().unwrap().next.as_mut().unwrap().next;
        }
        head
    }

    fn insert_node(cur: &mut Option<Box<ListNode>>) {
        if let Some(cur) = cur {
            if let Some(nxt) = &mut cur.next {
                let gcd_val: i32 = Solution::gcd(cur.val, nxt.val);
                let new: Option<Box<ListNode>> = Some(Box::new(ListNode {
                    val: gcd_val,
                    next: cur.next.take(),
                }));
                cur.next = new;
            }
        }
    }

    fn gcd(x: i32, y: i32) -> i32 {
        return if y == 0 { x } else { Solution::gcd(y, x % y) };
    }
}
```



## [2816. Double a Number Represented as a Linked List](https://leetcode.cn/problems/double-a-number-represented-as-a-linked-list/)

#### Go

后序遍历, 避免翻转, 类似[445. Add Two Numbers II](https://leetcode.com/problems/add-two-numbers-ii/) 

```go
func doubleIt(head *ListNode) *ListNode {
    var foo func(*ListNode) int
    foo = func(head *ListNode) int {
        if head == nil {
            return 0
        }
        carry := foo(head.Next)
        head.Val *= 2
        head.Val += carry
        carry = head.Val / 10
        head.Val %= 10
        return carry
    }
    carry := foo(head)
    if carry > 0 {
        head = &ListNode{carry, head}
    }
    return head
}
```



## [2841. Maximum Sum of Almost Unique Subarray](https://leetcode.com/problems/maximum-sum-of-almost-unique-subarray/)

#### Go

```go
func maxSum(nums []int, m int, k int) (ms int64) {
    mp := map[int]int{}
    ms = 0
    s := 0
    for i, num := range nums {
        mp[num] ++
        s += num
        if i >= k - 1 {
            if int64(s) > ms && len(mp) >= m {
                ms = int64(s)
            }
            mp[nums[i - (k - 1)]] --
            if mp[nums[i - (k - 1)]] == 0 {
                delete(mp, nums[i - (k - 1)])
            }
            s -= nums[i - (k - 1)]
        }
    }
    return
}
```


