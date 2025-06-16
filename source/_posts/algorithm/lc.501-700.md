---
title: LeetCode 501-700
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

---

## [501. Find Mode in Binary Search Tree](https://leetcode.com/problems/find-mode-in-binary-search-tree/) [Tree]



## [503. Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/) [Mono Stack]



## [508. Most Frequent Subtree Sum](https://leetcode.cn/problems/most-frequent-subtree-sum/)

#### C++

```c++
class Solution {
    unordered_map<int, int> sumFreqMap;

    int maxFreq = 0;

    int dfs(TreeNode* root) {
        if (!root) return 0;
        int subtreeSum = dfs(root->left) + dfs(root->right) + root->val;
        sumFreqMap[subtreeSum] ++;
        maxFreq = max(maxFreq, sumFreqMap[subtreeSum]);
        return subtreeSum;
    }
public:
    vector<int> findFrequentTreeSum(TreeNode* root) {
        dfs(root);
        vector<int> ans;
        for (auto it : sumFreqMap) {
            if (it.second == maxFreq) ans.push_back(it.first);
        }
        return ans;
    }
};
```



#### Go

```go
func findFrequentTreeSum(root *TreeNode) []int {
    mp := map[int]int{}
    maxfreq := 0
    var dfs func(*TreeNode) int
    dfs = func(root *TreeNode) int {
        if root == nil {
            return 0
        }
        sum := dfs(root.Left) + dfs(root.Right) + root.Val
        mp[sum] ++
        if mp[sum] > maxfreq {
            maxfreq = mp[sum]
        }
        return sum
    }
    dfs(root)
    ans := []int{}
    for sum, freq := range mp {
        if freq == maxfreq {
            ans = append(ans, sum)
        }
    }
    return ans
}
```



## [509. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/) [DP]



## [513. Find Bottom Left Tree Value](https://leetcode.com/problems/find-bottom-left-tree-value/) [Tree]



## [515. Find Largest Value in Each Tree Row](https://leetcode.com/problems/find-largest-value-in-each-tree-row/) [Tree]



## [516. Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/) [DP-Sub]



## [518. Coin Change II](https://leetcode.com/problems/coin-change-ii/) [DP-Knapsack]



## [530. Minimum Absolute Difference in BST](https://leetcode.com/problems/minimum-absolute-difference-in-bst/) [Tree]



## [538. Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/) [Tree]



## [539. Minimum Time Difference](https://leetcode.com/problems/minimum-time-difference/)

###### Java

```java
class Solution {
    public int findMinDifference(List<String> timePoints) {
        int[] times = timePoints.stream().map(s -> toMinutes(s)).mapToInt(x->x).toArray();
        Arrays.sort(times);
        System.out.println(Arrays.toString(times));
        int dist = 12 * 60;
        int d = 0;
        for (int i = 0; i < times.length - 1; i ++) {
            int mm1 = times[i];
            int mm2 = times[i + 1];
            d = timeDist(mm1, mm2);
            dist = Math.min(dist, d);
            if (dist == 0) return 0;
        }
        d = timeDist(times[times.length - 1], times[0]); // 不要忘了比较头尾两个元素, 这本质上是一个环
        dist = Math.min(dist, d);
        return dist;
    }
    private int timeDist(int mm1, int mm2) {
        int abs = Math.abs(mm2 - mm1);
        return Math.min(abs, 24 * 60 - abs);
    }
    private int toMinutes(String time) {
        String[] tmp = time.split(":");
        int h = Integer.parseInt(tmp[0]);
        int m = Integer.parseInt(tmp[1]);
        return h * 60 + m;
    }
}
```

## [541. Reverse String II](https://leetcode.com/problems/reverse-string-ii/) [String]



## [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)

与 [124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/) 类似

#### Java

```java
class Solution {
    public int diameterOfBinaryTree(TreeNode root) {
        diameter = 0;
        int gain = traversal(root);
        return Math.max(diameter, gain) - 1;
    }
    private int diameter;
    private int traversal(TreeNode root) {
        if (root == null) return 0;
        int gainL = traversal(root.left);
        int gainR = traversal(root.right);
        diameter = Math.max(diameter, gainL + gainR + 1);
        return Math.max(gainL, gainR) + 1;
    }
}
```



#### C++

```c++
class Solution {
    int diameter;
    int dfs(TreeNode* root) {
        if (!root) return 0;
        int l = dfs(root->left), r = dfs(root->right);
        diameter = max(diameter, 1 + l + r);
        return 1 + max(l, r);
    }
public:
    int diameterOfBinaryTree(TreeNode* root) {
        diameter = 0;
        dfs(root);
        return diameter - 1;
    }
};
```



## [559. Maximum Depth of N-ary Tree](https://leetcode.com/problems/maximum-depth-of-n-ary-tree/)



## [563. Binary Tree Tilt](https://leetcode.com/problems/binary-tree-tilt/)

#### Go

```go
func abs (x int) int {
    if x < 0 { x *= -1 }
    return x
}
func findTilt(root *TreeNode) int {
    sumTitle := 0
    var dfs func(*TreeNode) int
    dfs = func(root *TreeNode) int {
        if root == nil {
            return 0
        }
        sumLeft := dfs(root.Left)
        sumRight := dfs(root.Right)
        sumTitle += abs(sumLeft - sumRight)
        return root.Val + sumLeft + sumRight
    }
    dfs(root)
    return sumTitle
}
```



## [572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/)

#### Go

暴力匹配

```go
func isSameTree(root1, root2 *TreeNode) bool {
    if root1 == nil && root2 == nil { 
        return true 
    } else if root1 == nil && root2 != nil || root1 != nil && root2 == nil { 
        return false 
    } else if root1.Val != root2.Val { 
        return false 
    } else { 
        return isSameTree(root1.Left, root2.Left) && isSameTree(root1.Right, root2.Right) 
    }
}

func isSubtree(root *TreeNode, subRoot *TreeNode) bool {
    if root == nil && subRoot == nil {
        return true
    }
    if root == nil && subRoot != nil {
        return false
    }
    if isSameTree(root, subRoot) {
        return true 
    } else { 
        return isSubtree(root.Left, subRoot) || isSubtree(root.Right, subRoot) 
    }
}
```



加入高度判断搜索, 减少`isSameTree`调用次数

```go
func treeHeight(root *TreeNode) int {
    if root == nil { return 0 }
    return 1 + max(treeHeight(root.Left), treeHeight(root.Right))
}

func isSameTree(p, q *TreeNode) bool {
    if p == nil || q == nil {
        return p == q
    }
    return p.Val == q.Val && isSameTree(p.Left, q.Left) && isSameTree(p.Right, q.Right)
}

func isSubtree(root *TreeNode, subRoot *TreeNode) bool {
    h := treeHeight(subRoot)
    var dfs func(*TreeNode) (bool, int)
    dfs = func(root *TreeNode) (found bool, height int) {
        found = false
        height = 0
        if root == nil { return }
        leftFound, leftHeight := dfs(root.Left)
        rightFound, rightHeight := dfs(root.Right)
        found = leftFound || rightFound
        height = 1 + max(leftHeight, rightHeight)
        if found { return }
        if height == h && isSameTree(root, subRoot) { found = true }
        return
    }
    found, _ := dfs(root)
    return found
}
```





## [583. Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/) [DP-Sub]



## [606. Construct String from Binary Tree](https://leetcode.cn/problems/construct-string-from-binary-tree/)

#### C++

```c++
class Solution {
    string ans;

    void dfs(TreeNode* root) {
        if (!root) return;
        ans += std::to_string(root->val);
        if (!root->left && !root->right) return;
        ans += "(";
        dfs(root->left);
        ans += ")";

        if (!root->right) return;

        ans += "(";
        dfs(root->right);
        ans += ")";
    }
public:
    string tree2str(TreeNode* root) {
        dfs(root);
        return ans;
    }
};
```



#### Go

```go
func tree2str(root *TreeNode) (ans string) {
    ans = ""
    var dfs func(*TreeNode)
    dfs = func(root *TreeNode) {
        if root == nil { return }
        ans += strconv.Itoa(root.Val)
        if root.Left == nil && root.Right == nil { return }
        ans += "("
        dfs(root.Left)
        ans += ")"
        if root.Right == nil { return }
        ans += "("
        dfs(root.Right)
        ans += ")"
    }
    dfs(root)
    return ans
}
```



## [617. Merge Two Binary Trees](https://leetcode.com/problems/merge-two-binary-trees/) [Tree]



## [623. Add One Row to Tree](https://leetcode.cn/problems/add-one-row-to-tree/)

#### C++

```c++
class Solution {
public:
    TreeNode* addOneRow(TreeNode* root, int val, int depth) {
        if (depth == 1) {
            return new TreeNode(val, root, nullptr);
        }
        deque<TreeNode*> levelq;
        levelq.push_back(root);
        int lvlcnt = 1;
        while (lvlcnt < depth - 1) {
            int lvlsz = levelq.size();
            for (int i = 0; i < lvlsz; i ++ ) {
                TreeNode* cur = levelq.front(); levelq.pop_front();
                if (cur->left) levelq.push_back(cur->left);
                if (cur->right) levelq.push_back(cur->right);
            }
            lvlcnt ++;
        }
        for (auto node : levelq) {
            node->left = new TreeNode(val, node->left, nullptr);
            node->right = new TreeNode(val, nullptr, node->right);
        }
        return root;
    }
};
```



#### Go

```go
func addOneRow(root *TreeNode, val int, depth int) *TreeNode {
    if depth == 1 {
        return &TreeNode{val, root, nil}
    }
    lvlq := []*TreeNode{root}
    lvlcnt := 1
    for lvlcnt < depth - 1 {
        lvlsz := len(lvlq)
        for i := 0; i < lvlsz; i ++ {
            cur := lvlq[0]; lvlq = lvlq[1:]
            if cur.Left != nil {
                lvlq = append(lvlq, cur.Left)
            }
            if cur.Right != nil {
                lvlq = append(lvlq, cur.Right)
            }
        }
        lvlcnt ++
    }
    for _, node := range lvlq {
        node.Left = &TreeNode{val, node.Left, nil}
        node.Right = &TreeNode{val, nil, node.Right}
    }
    return root
}
```



## [637. Average of Levels in Binary Tree](https://leetcode.com/problems/average-of-levels-in-binary-tree/) [Tree]



## [643. Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/)

#### Go

```go
func findMaxAverage(nums []int, k int) float64 {
    maxSum := math.MinInt
    sum := 0
    l := 0
    for r := 0; r < len(nums); r ++ {
        sum += nums[r]
        if r - l + 1 == k {
            if maxSum < sum {
                maxSum = sum
            }
            sum -= nums[l]
            l ++
        }
    }
    return float64(maxSum) / float64(k)
}
```



## [647. Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/) [DP-Sub]



## [649. Dota2 Senate](https://leetcode.com/problems/dota2-senate/) [Greedy]



## [654. Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/) [Tree]



## [657. Robot Return to Origin](https://leetcode.com/problems/robot-return-to-origin/) [Simulation]



## [669. Trim a Binary Search Tree](https://leetcode.com/problems/trim-a-binary-search-tree/) [Tree]



## [671. Second Minimum Node In a Binary Tree](https://leetcode.com/problems/second-minimum-node-in-a-binary-tree/)

#### Go

```go
func findSecondMinimumValue(root *TreeNode) int {
    firstMin := -1
    secondMin := -1
    var foo func(*TreeNode)
    foo = func(root *TreeNode) {
        if root == nil {
            return
        }
        
        v := root.Val

        if firstMin == -1 || firstMin > v {
            secondMin = firstMin
            firstMin = v
        } else if (secondMin == -1 || secondMin > v) && v != firstMin {
            secondMin = v
        }

        foo(root.Left)
        foo(root.Right)
    }
    foo(root)
    return secondMin
}
```



## [673. Number of Longest Increasing Subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/) [DP-Sub]

参考 [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)



## [674. Longest Continuous Increasing Subsequence](https://leetcode.com/problems/longest-continuous-increasing-subsequence/) [DP-Sub]



## [687. Longest Univalue Path](https://leetcode.cn/problems/longest-univalue-path/)

#### C++

```c++
class Solution {
    int dfs(TreeNode* root, int& longest) {
        if (!root) return 0;
        int l = dfs(root->left, longest);
        if (root->left && root->val != root->left->val) l = 0;
        int r = dfs(root->right, longest);
        if (root->right && root->val != root->right->val) r = 0;
        longest = max(longest, 1 + l + r);
        return 1 + max(l, r);
    }

public:
    int longestUnivaluePath(TreeNode* root) {
        if (!root) return 0;
        int longest = 0;
        dfs(root, longest);
        return longest - 1;
    }
};
```



#### Go

```go
func longestUnivaluePath(root *TreeNode) int {
    longestSz := 0
    if root == nil { return longestSz }
    var dfs func(*TreeNode) int
    dfs = func(root *TreeNode) int {
        if root == nil { return 0 }
        l := dfs(root.Left)
        if root.Left != nil && root.Val != root.Left.Val { l = 0 }
        r := dfs(root.Right)
        if root.Right != nil && root.Val != root.Right.Val { r = 0 }
        longestSz = max(longestSz, 1 + l + r)
        return 1 + max(l, r)
    }
    dfs(root)
    return longestSz - 1
}
```



## [700. Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/) [Tree]



