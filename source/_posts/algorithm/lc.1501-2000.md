---
title: LeetCode 1501-2000
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

## [1512. Number of Good Pairs](https://leetcode.com/problems/number-of-good-pairs/)

#### C++

```c++
class Solution {
public:
    int numIdenticalPairs(vector<int>& nums) {
        unordered_map<int, int> mp;
        int cnt = 0;
        for (int num : nums) {
            if (mp.find(num) != mp.end()) cnt += mp[num];
            mp[num] ++;
        }
        return cnt;
    }
};
```



## [1530. Number of Good Leaf Nodes Pairs](https://leetcode.cn/problems/number-of-good-leaf-nodes-pairs/)

#### Go

*Time Limit Exceed*

```go
// 类似最近公共祖先, 使用该函数除了获取公共祖先, 还可以获取两节点之间的pathSz
func commonPath(root, root1, root2 *TreeNode) (findCnt int, pathSz int) {
    findCnt, pathSz = 0, 0
    if root == nil { return }
    if root == root1 || root == root2 {
        findCnt ++
        pathSz ++
        return
    }
    findCntL, pathSzL := commonPath(root.Left, root1, root2)
    if findCntL == 2 {
        findCnt = 2
        pathSz = pathSzL
        return
    }
    findCntR, pathSzR := commonPath(root.Right, root1, root2)
    if findCntR == 2 {
        findCnt = 2
        pathSz = pathSzR
        return
    }

    if findCntL == 1 && findCntR == 1 {
        findCnt = 2
        pathSz = pathSzL + pathSzR + 1
        return
    }

    if findCntL == 1 {
        findCnt = 1
        pathSz = pathSzL + 1
        return
    }

    if findCntR == 1 {
        findCnt = 1
        pathSz = pathSzR + 1
        return
    }

    return
}

func countPairs(root *TreeNode, distance int) int {
    leafnodes := []*TreeNode{}
    var dfs func(*TreeNode)
    dfs = func(root *TreeNode) {
        if root == nil { return }
        if root.Left == nil && root.Right == nil {
            leafnodes = append(leafnodes, root)
            return
        }
        dfs(root.Left)
        dfs(root.Right)
    }
    dfs(root)
    cnt := 0
    for i := 0; i < len(leafnodes); i ++ {
        for j := i + 1; j < len(leafnodes); j ++ {
            _, pathSz := commonPath(root, leafnodes[i], leafnodes[j])
            if pathSz <= distance + 1 {
                cnt ++
            }
        }
    }
    return cnt
}
```



关键一点: pair中的两个叶子节点必然来自于某一公共节点的左右子树

```go
func countPairs(root *TreeNode, distance int) int {
    cnt := 0
    var dfs func(*TreeNode) []int // 返回root到所有它下属的leaf node的距离
    dfs = func(root *TreeNode) ([]int) {
        if root == nil { return []int{} }
        if root.Left == nil && root.Right == nil { return []int{0} }
        
        lens := []int{}
        left := dfs(root.Left)
        right := dfs(root.Right)
        for i := range left {
            left[i] ++
            if left[i] > distance {
                continue
            }
            lens = append(lens, left[i])
        }
        for i := range right {
            right[i] ++
            if right[i] > distance {
                continue
            }
            lens = append(lens, right[i])
        }
        for _, l := range left {
            for _, r := range right {
                if l + r <= distance {
                    cnt ++
                }
            }
        }
        return lens
    }
    dfs(root)
    return cnt
}
```



## [1669. Merge In Between Linked Lists](https://leetcode.cn/problems/merge-in-between-linked-lists/)

#### C++

```c++
class Solution {
public:
    ListNode* mergeInBetween(ListNode* list1, int a, int b, ListNode* list2) {
        ListNode* dummy = new ListNode(0, list1);
        ListNode* p = dummy;
        b = b - a + 1; // 要删除的长度
        while (p && a > 0) {
            p = p->next;
            a --;
        }
        while (p && p->next && b > 0) {
            ListNode* del = p->next;
            p->next = p->next->next;
            delete del;
            b --;
        }
        ListNode* back = p->next;
        p->next = list2;
        while (p && p->next) p = p->next;
        p->next = back;
        ListNode* ans = dummy->next; dummy->next = nullptr;
        delete dummy;
        return ans;
    }
};
```



## [1684. Count the Number of Consistent Strings](https://leetcode.com/problems/count-the-number-of-consistent-strings/)

```java
class Solution {
    public int countConsistentStrings(String allowed, String[] words) {
        boolean[] allowedSet = new boolean[26];
        for (char c : allowed.toCharArray()) allowedSet[(int)(c - 'a')] = true;
        int cnt = 0;
        for (String word : words) {
            int flg = 1;
            for (char c : word.toCharArray()) {
                int idx = (int)(c - 'a');
                if (!allowedSet[idx]) {
                    flg = 0;
                    break;
                }
            }
            cnt += flg;
        }
        return cnt;
    }
}
```



## [1721. Swapping Nodes in a Linked List](https://leetcode.com/problems/swapping-nodes-in-a-linked-list/)

#### Go

```go
func swapNodes(head *ListNode, k int) *ListNode {
    p := head
    l := head
    r := head
    for i := 0; i < k - 1; i ++ {
        p = p.Next
    }
    l = p
    for p != nil && p.Next != nil { // p比r先行, p触底
        r = r.Next
        p = p.Next
    }
    tv := l.Val
    l.Val = r.Val
    r.Val = tv
    return head
}
```



## [1894. Find the Student that Will Replace the Chalk](https://leetcode.com/problems/find-the-student-that-will-replace-the-chalk/)

```java
class Solution {
    public int chalkReplacer(int[] chalk, int k) {
        // int sum = Arrays.stream(chalk).sum(); // 会溢出
        int sum = 0;
        for (int num : chalk) {
            sum += num;
            if (sum > k) break;
        }
        if (sum <= k) k %= sum;
        int idx = 0;
        while (k >= chalk[idx]) {
            k -= chalk[idx];
            idx ++;
        }
        return idx;
    }
}
```



## [1945. Sum of Digits of String After Convert](https://leetcode.com/problems/sum-of-digits-of-string-after-convert/)

```java
class Solution {
    public int getLucky(String s, int k) {
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            sb.append(String.valueOf((int)(c - 'a' + 1)));
        }
        int ans = 0;
        String num = sb.toString();
        for (int i = 0; i < k; i ++) {
            ans = beLucky(num);
            num = String.valueOf(ans);
        }
        return ans;
    }
    private int beLucky(String num) {
        int ret = 0;
        for (char c : num.toCharArray()) {
            ret += (int)(c - '0');
        }
        return ret;
    }
}
```



