---
title: Tree Node
date: 2024-05-07 00:00:00
tags: key-algorithm
categories: algorithm
---

---

# Definition of Tree Node



#### Java

```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode() {}
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

#### C++

```c++
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};
```

#### Rust

```rust
use std::rc::Rc;
use std::cell::RefCell;
#[derive(Debug, PartialEq, Eq)]
pub struct TreeNode {
    pub val: i32,
    pub left: Option<Rc<RefCell<TreeNode>>>,
    pub right: Option<Rc<RefCell<TreeNode>>>,
}

impl TreeNode {
    #[inline]
    pub fn new(val: i32) -> Self {
        TreeNode {
            val,
            left: None,
            right: None
        }
    }
}
```

# Examples

## Depth-First Traversal of Tree

### [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)

#### Java

*Recursion*

```java
class Solution {
    private List<Integer> res = new ArrayList<>();
    public List<Integer> preorderTraversal(TreeNode root) {
        if (root == null) return res;
        res.add(root.val);
        if (root.left != null) preorderTraversal(root.left);
        if (root.right != null) preorderTraversal(root.right);
        return res;
    }
}
```

*Stack + Iteration*

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        Stack<TreeNode> st = new Stack<>();
        st.push(root);
        while (!st.isEmpty()) {
            TreeNode tmp = st.pop();
            res.add(tmp.val);
            if (tmp.right != null) st.push(tmp.right);
            if (tmp.left != null) st.push(tmp.left);
        }
        return res;
    }
}
```

#### C++

```c++
class Solution {
    void traversal(TreeNode* root, vector<int>& ans) {
        if (!root) return;
        ans.push_back(root->val);
        traversal(root->left, ans);
        traversal(root->right, ans);
    }
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> ans{};
        traversal(root, ans);
        return ans;
    }
};
```

```c++
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> ans{};
        if (!root) return ans;
        vector<TreeNode*> stk{root};
        while (!stk.empty()) {
            TreeNode* cur = stk.back();
            stk.pop_back();
            ans.push_back(cur->val);
            if (cur->right) stk.push_back(cur->right);
            if (cur->left) stk.push_back(cur->left);
        }
        return ans;
    }
};
```

#### Rust

```rust
impl Solution {
    pub fn preorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = Vec::new();
        Self::traversal(&root, &mut ans);
        ans
    }
    fn traversal(root: &Option<Rc<RefCell<TreeNode>>>, ans: &mut Vec<i32>) {
        if let Some(root) = root {
            ans.push(root.borrow().val);
            Self::traversal(&root.borrow().left, ans);
            Self::traversal(&root.borrow().right, ans);
        }
    }
}
```

```rust
impl Solution {
    pub fn preorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = Vec::new();
        if let Some(root) = root {
            let mut stk: Vec<Rc<RefCell<TreeNode>>> = vec![root];
            // 正确
            while !stk.is_empty() {
                if let Some(cur) = stk.pop() { // cur: Rc<RefCell<TreeNode>>
                    ans.push(cur.borrow().val);
                    if let Some(right) = cur.borrow_mut().right.take() { stk.push(right); }
                    if let Some(left) = cur.borrow_mut().left.take() { stk.push(left); }
                }
            }

            // 错误
            // while !stk.is_empty() {
            //     let cur = stk.pop().unwrap();
            //     ans.push(cur.borrow().val);
            //     if let Some(right) = cur.borrow_mut().right.take() { stk.push(right); }
            //     if let Some(left) = cur.borrow_mut().left.take() { stk.push(left); }
            // }
        }
        ans
    }
}
```

使用 `RefCell<T> `允许我们对**同一变量**的**不同部分的数据**进行写借用 (或同时存在写借用和读借用). 例如代码中的两行 `borrow_mut()`.

但一般的 `&mut `则不行, 因为同一变量的不同部分数据的所有者仍然是那个变量, 同时使用 `&mut` 借用同一变量的不同部分数据则违反了编译规则 (**编译时报错**). 也就是说, 在使用 `&mut` 时, Rust 编译器在编译时会严格检查, **只允许一个可变借用**或**多个不可变借用**, 并且这个检查是针对**整个变量**的.

但是, 使用 `RefCell<T>`, 如果对**同一变量**的**同一部分数据**进行了多次写借用或读写借用共存, 这样就会**运行时报错**.

总之, `RefCell<T>` 允许我们对变量的**不同部分数据**进行更加细粒度的借用.

### [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)

#### Java

```java
class Solution {
    private List<Integer> res = new ArrayList<>();

    public List<Integer> postorderTraversal(TreeNode root) {
        if ( root == null ) return res;
        if ( root.left != null ) postorderTraversal(root.left);
        if ( root.right != null ) postorderTraversal(root.right);
        res.add(root.val);
        return res;
    }
}
```

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if ( root == null ) return res;
        List<TreeNode> stk = new ArrayList<>();
        stk.addLast(root);
        while ( !stk.isEmpty() ) {
            TreeNode cur = stk.removeLast();
            if ( cur != null ) {
                stk.addLast(cur);
                stk.addLast(null);  // label that cur has been visited
                if (cur.right != null) stk.addLast(cur.right);
                if (cur.left != null) stk.addLast(cur.left);
            } else {
                cur = stk.removeLast();
                res.add(cur.val);
            }
        }
        return res;
    }
}
```

#### Rust

```rust
impl Solution {
    pub fn postorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        fn traversal(root: Rc<RefCell<TreeNode>>, ans: &mut Vec<i32>) {
            if let Some(left) = root.borrow_mut().left.take() {
                traversal(left, ans);
            }
            if let Some(right) = root.borrow_mut().right.take() {
                traversal(right, ans);
            }
            ans.push(root.borrow().val);
        }
        let mut ans = Vec::new();
        if let Some(root) = root {
            traversal(root, &mut ans);
        }
        ans
    }
}
```

```rust
impl Solution {
    pub fn postorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = Vec::new();
        if root.is_none() { return ans; }
        let mut stk: Vec<Option<Rc<RefCell<TreeNode>>>> = vec![root];
        while !stk.is_empty() {
            let mut cur = stk.pop().unwrap(); // cur: Option<...>
            if let Some(cur) = cur {
                let right = if let Some(r) = cur.borrow_mut().right.take() { Some(r) } else { None };
                let left = if let Some(l) = cur.borrow_mut().left.take() { Some(l) } else { None };
                
                stk.push(Some(cur));
                stk.push(None);
                if right.is_some() { stk.push(right); }
                if left.is_some() { stk.push(left); }
            } else {
                cur = stk.pop().unwrap();
                ans.push(cur.unwrap().borrow().val);
            }
        }
        ans
    }
}
```

### [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)

#### Java

```java
class Solution {
    private List<Integer> res = new ArrayList<>();

    public List<Integer> inorderTraversal(TreeNode root) {
        if ( root == null ) return res;
        if ( root.left != null ) inorderTraversal(root.left);
        res.add(root.val);
        if ( root.right != null ) inorderTraversal(root.right);
        return res;
    }
}
```

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if ( root == null ) return res;
        List<TreeNode> stk = new ArrayList<>();
        stk.addLast(root);
        while ( !stk.isEmpty() ) {
            TreeNode cur = stk.removeLast();
            if ( cur != null ) {
                if (cur.right != null) stk.addLast(cur.right);
                stk.addLast(cur);
                stk.addLast(null);  // label
                if (cur.left != null) stk.addLast(cur.left);
            } else {
                cur = stk.removeLast();
                res.add(cur.val);
            }
        }
        return res;
    }
}
```

*Label States with Hash*

该方法更加通用, 例如 [207. Course Schedule](https://leetcode.com/problems/course-schedule/) 也可以使用该方法

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        if (root == null) return ans;

        Map<TreeNode, Integer> state = new HashMap<>();  // 0: 未访问, 1: 正在访问, 2: 访问过的
        LinkedList<TreeNode> stk = new LinkedList<>();
        stk.addLast(root);
        while (!stk.isEmpty()) {
            TreeNode cur = stk.removeLast();
            if (state.getOrDefault(cur, 0) == 0) {
                state.put(cur, 1);
                if (cur.right != null) stk.addLast(cur.right);
                stk.addLast(cur);
                if (cur.left != null) stk.addLast(cur.left);
            } else if (state.getOrDefault(cur, 0) == 1) {
                state.put(cur, 2);
                ans.add(cur.val);
            } else {}
        }
        return ans;
    }
}
```

#### Rust

```rust
impl Solution {
    pub fn inorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = Vec::new();
        fn traversal(root: Option<Rc<RefCell<TreeNode>>>, ans: &mut Vec<i32>) {
            if let Some(root) = root {
                let left = root.borrow_mut().left.take();
                let right = root.borrow_mut().right.take();
                traversal(left, ans);
                ans.push(root.borrow().val);
                traversal(right, ans);
            }
        }
        traversal(root, &mut ans);
        ans
    }
}
```

```rust
impl Solution {
    pub fn inorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = Vec::new();
        if root.is_none() { return ans; }
        let mut stk: Vec<Option<Rc<RefCell<TreeNode>>>> = vec![root];
        while !stk.is_empty() {
            let cur: Option<Rc<RefCell<TreeNode>>> = stk.pop().unwrap();
            if let Some(cur) = cur { // cur: Rc<RefCell<TreeNode>>
                let left = if let Some(l) = cur.borrow_mut().left.take() { Some(l) } else { None };
                let right = if let Some(r) = cur.borrow_mut().right.take() { Some(r) } else { None };
                if right.is_some() { stk.push(right);}
                stk.push(Some(cur));
                stk.push(None);
                if left.is_some() { stk.push(left); }
            } else {
                let cur: Option<Rc<RefCell<TreeNode>>> = stk.pop().unwrap();
                ans.push(cur.unwrap().borrow().val);
            }
        }
        ans
    }
}
```

## Width-First Traversal of Tree

### [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)

#### Java

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res;
        Queue<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        while (!q.isEmpty()) {
            List<Integer> level = new ArrayList<>();
            int lvlSz = q.size();
            for (int i = 0; i < lvlSz; i ++) {
                TreeNode cur = q.poll();
                level.add(cur.val);
                if (cur.left != null) q.offer(cur.left);
                if (cur.right != null) q.offer(cur.right);
            }
            res.add(level);
        }
        return res;
    }
}
```

```java
class Solution {
    private List<List<Integer>> res = new ArrayList<>();
    private void levelTraversal(TreeNode node, int deep) {
        if ( node == null ) return;
        deep ++;  // 如果 node 不空, 深度 deep ++ (res中的加一层)
        if ( res.size() < deep ) res.add(new ArrayList<>());
        res.get(deep - 1).add(node.val);  // res.get(deep - 1) 节点当前所在的层        
        this.levelTraversal(node.left, deep);
        this.levelTraversal(node.right, deep);
    }

    public List<List<Integer>> levelOrder(TreeNode root) {
        this.levelTraversal(root, 0);
        return res;
    }
}
```

#### C++

```c++
class Solution {
    void traversal(vector<vector<int>>& ans, TreeNode* root, int depth) {
        if (root == nullptr) return;
        depth ++;
        if (ans.size() < depth) ans.push_back(vector<int> {});
        ans[depth - 1].push_back(root->val);
        traversal(ans, root->left, depth);
        traversal(ans, root->right, depth);
    }
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> ans{};
        traversal(ans, root, 0);
        return ans;
    }
};
```

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> levels;
        if (root == nullptr) return levels;
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            int lvlSize = q.size();
            vector<int> level;
            while (lvlSize --) {
                TreeNode* cur = q.front();
                q.pop();
                level.push_back(cur->val);
                if (cur->left) q.push(cur->left);
                if (cur->right) q.push(cur->right);
            }
            levels.push_back(level);
        }
        return levels;
    }
};
```

#### Rust

```rust
impl Solution {
    pub fn level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        fn traversal(
            ans: &mut Vec<Vec<i32>>, 
            root: Option<Rc<RefCell<TreeNode>>>, 
            mut depth: usize
        ) {
            if let Some(root) = root {
                depth += 1;
                if ans.len() < depth { ans.push(Vec::new()); }
                ans[depth - 1].push(root.borrow().val);
                traversal(ans, root.borrow_mut().left.take(), depth);
                traversal(ans, root.borrow_mut().right.take(), depth);
            }
        }
        let mut ans: Vec<Vec<i32>> = Vec::new();
        traversal(&mut ans, root, 0);
        ans
    }
}
```

```rust
use std::collections::VecDeque;
impl Solution {
    pub fn level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        let mut ans: Vec<Vec<i32>> = Vec::new();
        if let Some(root) = root {
            let mut q: VecDeque<Rc<RefCell<TreeNode>>> = VecDeque::new();
            q.push_back(root);
            while !q.is_empty() {
                let lvl_sz = q.len();
                let mut level = Vec::new();
                for _ in 0..lvl_sz {
                    let cur = q.pop_front().unwrap();
                    let mut node = cur.borrow_mut();
                    level.push(node.val);
                    if let Some(left) = node.left.take() { q.push_back(left); }
                    if let Some(right) = node.right.take() { q.push_back(right); }
                }
                ans.push(level);
            }
        }
        ans
    }
}
```

```rust
use std::collections::VecDeque;
impl Solution {
    pub fn level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        let mut ans: Vec<Vec<i32>> = Vec::new();
        if let Some(root) = root {
            let mut q: VecDeque<Rc<RefCell<TreeNode>>> = VecDeque::new();
            q.push_back(root);
            while !q.is_empty() {
                let lvl_sz = q.len();
                let mut level = Vec::new();
                for _ in 0..lvl_sz {
                    if let Some(cur) = q.pop_front() { // 使用 if let, 正确
                        level.push(cur.borrow().val);
                        if let Some(left) = cur.borrow_mut().left.take() { q.push_back(left); }
                        if let Some(right) = cur.borrow_mut().right.take() { q.push_back(right); }
                    }
                }
                ans.push(level);
            }
        }
        ans
    }
}
```

```rust
// Wrong Code
use std::collections::VecDeque;
impl Solution {
    pub fn level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
        let mut ans: Vec<Vec<i32>> = Vec::new();
        if let Some(root) = root {
            let mut q: VecDeque<Rc<RefCell<TreeNode>>> = VecDeque::new();
            q.push_back(root);
            while !q.is_empty() {
                let lvl_sz = q.len();
                let mut level = Vec::new();
                for _ in 0..lvl_sz {
                    let mut cur = q.pop_front().unwrap(); // 直接 unwrap, 错误
                    level.push(cur.borrow().val);
                    if let Some(left) = cur.borrow_mut().left.take() { q.push_back(left); }
                    if let Some(right) = cur.borrow_mut().right.take() { q.push_back(right); } // `cur` does not live long enough
                }
                ans.push(level);
            }
        }
        ans
    }
}
```

### [107. Binary Tree Level Order Traversal II](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/)

#### Java

```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        if (root == null) return ans;
        Deque<TreeNode> dq = new ArrayDeque<>();
        dq.addLast(root);
        while (!dq.isEmpty()) {
            int lvlSz = dq.size();
            List<Integer> level = new ArrayList<>();
            for (int i = 0; i < lvlSz; i ++) {
                TreeNode cur = dq.removeFirst();
                level.add(cur.val);
                if (cur.left != null) dq.addLast(cur.left);
                if (cur.right != null) dq.addLast(cur.right);
            }
            ans.add(level);
        }
        Collections.reverse(ans);
        return ans;
    }
}
```

```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> ans = new LinkedList<>();
        traversal(ans, root, 0);
        return ans;
    }

    private void traversal(List<List<Integer>> ans, TreeNode root, int depth) {
        if (root == null) return;
        depth ++;
        if (ans.size() < depth) ans.addFirst(new ArrayList<>());
        ans.get(ans.size() - depth).add(root.val);
        traversal(ans, root.left, depth);
        traversal(ans, root.right, depth);
    }
}
```

#### C++

```c++
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> ans;
        traversal(ans, root, 0);
        reverse(ans.begin(), ans.end());
        return ans;
    }
private:
    void traversal(vector<vector<int>>& ans, TreeNode* root, int depth) {
        if (!root) return;
        depth ++;
        if (ans.size() < depth) ans.push_back(vector<int> {});
        ans[depth - 1].push_back(root->val);
        traversal(ans, root->left, depth);
        traversal(ans, root->right, depth);
    }
};
```

```c++
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> ans;
        if (!root) return ans;
        deque<TreeNode*> dq;
        dq.push_back(root);
        while (!dq.empty()) {
            int lvl_sz = dq.size();
            vector<int> level;
            for (int i = 0; i < lvl_sz; i ++) {
                TreeNode* cur = dq.front();
                dq.pop_front();
                level.push_back(cur->val);
                if (cur->left) dq.push_back(cur->left);
                if (cur->right) dq.push_back(cur->right);
            }
            ans.push_back(level);
        }
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```



### [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)

#### Java

```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        traversal(ans, root, 0);
        return ans;
    }
    private void traversal(List<Integer> ans, TreeNode root, int depth) {
        if (root == null) return;
        depth ++;
        if (ans.size() < depth) ans.add(0);
        ans.set(depth - 1, root.val);
        traversal(ans, root.left, depth);
        traversal(ans, root.right, depth);
    }
}
```

```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        if (root == null) return ans;
        Queue<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int lvlSz = q.size();
            int lvlVal = 0;
            for (int i = 0; i < lvlSz; i ++) {
                TreeNode cur = q.poll();
                lvlVal = cur.val;
                if (cur.left != null) q.offer(cur.left);
                if (cur.right != null) q.offer(cur.right);
            }
            ans.add(lvlVal);
        }
        return ans;
    }
}
```

#### C++

```c++
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> ans;
        if (!root) return ans;
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            int lvl_sz = q.size();
            int lvl_val = 0;
            for (int i = 0; i < lvl_sz; i ++) {
                TreeNode* cur = q.front();
                q.pop();
                lvl_val = cur->val;
                if (cur->left) q.push(cur->left);
                if (cur->right) q.push(cur->right);
            }
            ans.push_back(lvl_val);
        }
        return ans;
    }
};
```

```c++
class Solution {
    void traversal(vector<int>& ans, TreeNode* root, int depth) {
        if (!root) return;
        depth ++;
        if (ans.size() < depth) ans.push_back(0);
        ans[depth - 1] = root->val;
        traversal(ans, root->left, depth);
        traversal(ans, root->right, depth);
    }
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> ans;
        traversal(ans, root, 0);
        return ans;
    }
};
```

### [637. Average of Levels in Binary Tree](https://leetcode.com/problems/average-of-levels-in-binary-tree/)

#### Java

```java
class Solution {
    public List<Double> averageOfLevels(TreeNode root) {
        List<Double> ans = new ArrayList<>();
        if (root == null) return ans;
        Queue<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int lvlSz = q.size();
            double lvlAns = 0.0;
            for (int i = 0; i < lvlSz; i ++) {
                TreeNode cur = q.poll();
                lvlAns += (double) cur.val;
                if (cur.left != null) q.offer(cur.left);
                if (cur.right != null) q.offer(cur.right);
            }
            lvlAns /= (double) lvlSz;
            ans.add(lvlAns);
        }
        return ans;
    }
}
```

```java
class Solution {
    private List<Double> res = new ArrayList<>();
    private List<Integer> lvlSizes = new ArrayList<>(); // 记录一层的长度

    private void levelTraversal(TreeNode node, int deep) {
        if (node == null) return;
        deep ++;
        if ( res.size() < deep ) {
            res.add(0.0);
            lvlSizes.add(0);
        }
        res.set(deep - 1, res.get(deep - 1) + node.val);
        lvlSizes.set(deep - 1, lvlSizes.get(deep - 1) + 1);
        levelTraversal(node.left, deep);
        levelTraversal(node.right, deep);
    }

    public List<Double> averageOfLevels(TreeNode root) {
        levelTraversal(root, 0);
        for (int i = 0; i < res.size(); i ++) {
            res.set(i, res.get(i) / lvlSizes.get(i));
        }
        return res;
    }
}
```

#### C++

```c++
class Solution {
    void traversal(vector<vector<int>>& levels, TreeNode* root, int depth) {
        if (!root) return;
        depth ++;
        if (levels.size() < depth) levels.push_back(vector<int>{});
        levels[depth - 1].push_back(root->val);
        traversal(levels, root->left, depth);
        traversal(levels, root->right, depth);
    }
public:
    vector<double> averageOfLevels(TreeNode* root) {
        vector<vector<int>> levels {};
        traversal(levels, root, 0);
        vector<double> ans(levels.size());
        for (int i = 0; i < ans.size(); i ++) ans[i] = (double) accumulate(levels[i].begin(), levels[i].end(), 0) / levels[i].size(); // error: 要处理溢出
        return ans;
    }
};
```

```c++
class Solution {
public:
    vector<double> averageOfLevels(TreeNode* root) {
        vector<double> ans;
        if (!root) return ans;
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            long long lvl_sum = 0;
            int lvl_sz = q.size();
            for (int i = 0; i < lvl_sz; i ++) {
                TreeNode* cur = q.front();
                q.pop();
                lvl_sum += cur->val;
                if (cur->left) q.push(cur->left);
                if (cur->right) q.push(cur->right);
            }
            ans.push_back((double) lvl_sum / lvl_sz);
        }
        return ans;
    }
};
```

### [429. N-ary Tree Level Order Traversal](https://leetcode.com/problems/n-ary-tree-level-order-traversal/)

#### Java

```java
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        List<List<Integer>> levels = new LinkedList<>();
        if (root == null) return levels;
        Deque<Node> q = new ArrayDeque<>();
        q.addLast(root);
        while ( !q.isEmpty() ) {
            int levelLen = q.size();
            List<Integer> level = new ArrayList<>();
            for (int i = 0; i < levelLen; i ++) {
                Node cur = q.removeFirst();
                level.add(cur.val);
                for (Node child : cur.children) if (child != null) q.addLast(child);
            }
            levels.add(level);
        }
        return levels;
    }
}
```

```java
class Solution {
    private List<List<Integer>> ans = new ArrayList<>();
    private void levelTraversal(Node node, int deep) {
        if (node == null) return;
        deep ++;  // 如果 node 不空, 深度 deep ++ (res中的加一层)
        if (ans.size() < deep) ans.add(new ArrayList<Integer>());
        ans.get(deep - 1).add(node.val);  // ans.get(deep - 1) 节点当前所在的层
        for (Node child : node.children) levelTraversal(child, deep);
    }

    public List<List<Integer>> levelOrder(Node root) {
        this.levelTraversal(root, 0);
        return ans;
    }
}
```

#### C++

```c++
class Solution {
    void traversal(vector<vector<int>>& ans, Node* root, int depth) {
        if (!root) return;
        depth ++;
        if (ans.size() < depth) ans.push_back(vector<int>{});
        ans[depth - 1].push_back(root->val);
        for (Node*& chlid : root->children) traversal(ans, chlid, depth);
    }
public:
    vector<vector<int>> levelOrder(Node* root) {
        vector<vector<int>> ans;
        traversal(ans, root, 0);
        return ans;
    }
};
```

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        vector<vector<int>> levels;
        if (!root) return levels;
        queue<Node*> q;
        q.push(root);
        while (!q.empty()) {
            int lvl_sz = q.size();
            vector<int> level;
            for (int i = 0; i < lvl_sz; i ++) {
                Node* cur = q.front();
                q.pop();
                level.push_back(cur->val);
                for (Node*& chlid : cur->children) if (chlid) q.push(chlid);
            }
            levels.push_back(level);
        }
        return levels;
    }
};
```

### [515. Find Largest Value in Each Tree Row](https://leetcode.com/problems/find-largest-value-in-each-tree-row/)

#### Java

```java
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        Deque<TreeNode> q = new ArrayDeque<>();
        q.addLast(root);
        while (!q.isEmpty()) {
            int lvlSize = q.size();
            int lvlMax = Integer.MIN_VALUE;
            for (int i = 0; i < lvlSize; i ++) {
                TreeNode cur = q.removeFirst();
                lvlMax = Math.max(cur.val, lvlMax);
                if (cur.left != null) q.addLast(cur.left);
                if (cur.right != null) q.addLast(cur.right);
            }
            res.add(lvlMax);
        }
        return res;
    }
}
```

```java
class Solution {
    private List<Integer> res = new ArrayList<>();
    private void lvlTraversal(TreeNode cur, int deep) {
        if (cur == null) return;
        deep ++;
        if (res.size() < deep) res.add(Integer.MIN_VALUE);
        res.set(deep - 1 , Math.max( res.get(deep - 1), cur.val));
        lvlTraversal(cur.left, deep);
        lvlTraversal(cur.right, deep);
    }

    public List<Integer> largestValues(TreeNode root) {
        this.lvlTraversal(root, 0);
        return res;
    }
}
```

#### C++

```c++
class Solution {
public:
    vector<int> largestValues(TreeNode* root) {
        vector<int> ans;
        if (!root) return ans;
        deque<TreeNode*> q;
        q.push_back(root);
        while (!q.empty()) {
            int lvl_sz = q.size();
            int lvl_max = INT_MIN;
            for (int i = 0; i < lvl_sz; i ++) {
                TreeNode* cur = q.front();
                q.pop_front();
                lvl_max = max(lvl_max, cur->val);
                if (cur->left) q.push_back(cur->left);
                if (cur->right) q.push_back(cur->right);
            }
            ans.push_back(lvl_max);
        }
        return ans;
    }
};
```

```c++
class Solution {
    void traversal(vector<int>& ans, TreeNode* root, int depth) {
        if (!root) return;
        depth ++;
        if (ans.size() < depth) ans.push_back(INT_MIN);
        ans[depth - 1] = max(ans[depth - 1], root->val);
        traversal(ans, root->left, depth);
        traversal(ans, root->right, depth);
    }
public:
    vector<int> largestValues(TreeNode* root) {
        vector<int> ans;
        traversal(ans, root, 0);
        return ans;
    }
};
```

### [116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)

#### Java

```java
class Solution {
    public Node connect(Node root) {
        if (root == null) return root;
        List<Node> q = new LinkedList<>();
        q.addLast(root);
        while (!q.isEmpty()) {
            int lvlSize = q.size();
            for (int i = 0; i < lvlSize; i ++) {
                Node cur1 = q.removeFirst();
                Node cur2 = (i == lvlSize - 1) ? null : q.getFirst();  // can't use removeFirst() here !!!
                cur1.next = cur2;
                if (cur1.left != null) q.addLast(cur1.left);
                if (cur1.right != null) q.addLast(cur1.right);
            }
        }
        return root;
    }
}
```

遍历之后实现内联操作

```java
class Solution {
    private List<List<Node>> nodeLst = new ArrayList<>();

    private void lvlTraversal(Node cur, int deep) {
        if (cur == null) return;
        deep ++;
        if (nodeLst.size() < deep) nodeLst.add(new ArrayList<Node>());
        nodeLst.get(deep - 1).add(cur);
        
        lvlTraversal(cur.left, deep);
        lvlTraversal(cur.right, deep);
    }

    public Node connect(Node root) {
        this.lvlTraversal(root, 0);
        for (List<Node> level : nodeLst) {
            for (int i = 0; i < level.size(); i ++) {
                Node cur1 = level.get(i), cur2 = (i == level.size() - 1) ? null : level.get(i + 1);
                cur1.next = cur2;
            }
        }
        return root;
    }
}
```

在递归时内联

```java
class Solution {
    private List<List<Node>> nodeLst = new ArrayList<>();

    private void lvlTraversal(Node cur, int deep) {
        if (cur == null) return;
        deep ++;
        if (nodeLst.size() < deep) nodeLst.add(new ArrayList<Node>());
        List<Node> curLvl = nodeLst.get(deep - 1);
        if (curLvl.size() > 0) {
            Node pre = curLvl.get(curLvl.size() - 1);
            pre.next = cur;
            cur.next = null;
        }
        curLvl.add(cur);
        
        lvlTraversal(cur.left, deep);
        lvlTraversal(cur.right, deep);
    }

    public Node connect(Node root) {
        lvlTraversal(root, 0);
        return root;
    }
}
```

在递归时内联, 使用最少的额外空间 (最优)

```java
class Solution {
    private void traversal(List<Node> rec, Node root, int depth) {
        if (root == null) return;
        depth ++;
        if (rec.size() < depth) rec.add(root);
        else {
            rec.get(depth - 1).next = root;
            rec.set(depth - 1, root);
        }
        traversal(rec, root.left, depth);
        traversal(rec, root.right, depth);
    }
    public Node connect(Node root) {
        List<Node> rec = new ArrayList<>();
        traversal(rec, root, 0);
        return root;
    }
}
```

#### C++

```c++
class Solution {
public:
    Node* connect(Node* root) {
        vector<vector<Node*>> levels; 
        traversal(levels, root, 0);  
        return root;
    }
private:
    void traversal(vector<vector<Node*>>& levels, Node* root, int deep) {
        if (root == nullptr) return;
        if (deep >= levels.size()) levels.push_back(vector<Node*>());  // 添加新的一层
        vector<Node*>& level = levels[deep];
        if (!level.empty()) level[level.size() - 1]->next = root;
        level.push_back(root);
        traversal(levels, root->left, deep + 1);
        traversal(levels, root->right, deep + 1);
    }
};
```

```c++
class Solution {
public:
    Node* connect(Node* root) {
        vector<Node*> rec; 
        traversal(rec, root, 0);  
        return root;
    }
private:
    void traversal(vector<Node*>& rec, Node* root, int deep) {
        if (!root) return;
        if (deep == rec.size()) rec.push_back(root);
        else {
            rec[deep]->next = root;
            rec[deep] = root;
        }
        traversal(rec, root->left, deep + 1);
        traversal(rec, root->right, deep + 1);
    }
};
```

### [117. Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/)

完全同上

### [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

#### Java

*level traversal*

```java
class Solution {
    public int maxDepth(TreeNode root) {
        int maxDepth = 0;
        if (root == null) return maxDepth;
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            maxDepth ++;
            int lvlSz = queue.size();
            for (int i = 0; i < lvlSz; i ++) {
                TreeNode cur = queue.poll();
                if (cur.left != null) queue.offer(cur.left);
                if (cur.right != null) queue.offer(cur.right);
            }
        }
        return maxDepth;
    }
}
```

递归 (深度优先搜索)

```java
class Solution {
    private int res = 0;
    private void dfs(TreeNode cur, int deep) {
        if (cur == null) return;
        deep ++;
        res = Math.max(deep, res);
        dfs(cur.left, deep);
        dfs(cur.right, deep);
    }

    public int maxDepth(TreeNode root) {
        dfs(root, 0);
        return res;
    }
}
```

递归 (数学角度)

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        return 1 + Math.max( maxDepth(root.left) , maxDepth(root.right) );
    }
}
```

#### C++

```c++
class Solution {
    void deepFirstTraversal(int& maxDepth, int curDept, TreeNode* root) {
        if (!root) return;
        curDept ++;
        maxDepth = max(maxDepth, curDept);
        deepFirstTraversal(maxDepth, curDept, root->left);
        deepFirstTraversal(maxDepth, curDept, root->right);
    }
public:
    int maxDepth(TreeNode* root) {
        int maxDepth = 0;
        deepFirstTraversal(maxDepth, 0, root);
        return maxDepth;
    }
};
```



### [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/)

#### Java

```java
class Solution {
    public int minDepth(TreeNode root) {
        int minDepth = 0;
        if (root == null) return minDepth;
        Queue<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        while (!q.isEmpty()) {
            minDepth ++;
            int lvlSize = q.size();
            for (int i = 0; i < lvlSize; i++) {
                TreeNode cur = q.poll();
                if (cur.left == null && cur.right == null) return minDepth;  // 一旦发现叶子节点就返回
                else {
                    if (cur.left != null) q.offer(cur.left);
                    if (cur.right != null) q.offer(cur.right);
                }
            }
        }
        return minDepth;
    }
}
```

递归 (深度优先)

```java
class Solution {
    private int res = Integer.MAX_VALUE;
    private void dfs(TreeNode cur, int deep) {
        if (cur == null) return;
        deep ++;
        if (cur.left == null && cur.right == null) res = Math.min(res, deep);
        dfs(cur.left, deep);
        dfs(cur.right, deep);
    }
    public int minDepth(TreeNode root) {
        dfs(root, 0);
        return (res == Integer.MAX_VALUE) ? 0 : res;
    }
}
```

递归 (数学角度)

```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        if (root.left == null && root.right == null) return 1;
        int leftMinDepth = (root.left == null) ? Integer.MAX_VALUE : minDepth(root.left);
        int rightMinDepth = (root.right == null) ? Integer.MAX_VALUE : minDepth(root.right);
        return 1 + Math.min(leftMinDepth, rightMinDepth);
    }
}
```

#### C++

```c++
class Solution {
    void traversal(int& minDepth, int curDepth, TreeNode* root) {
        if (!root) return;
        curDepth ++;
        if (!root->left && !root->right) minDepth = min(minDepth, curDepth);
        traversal(minDepth, curDepth, root->left);
        traversal(minDepth, curDepth, root->right);
    }
public:
    int minDepth(TreeNode* root) {
        int minDepth = INT_MAX;
        traversal(minDepth, 0, root);
        return minDepth == INT_MAX ? 0 : minDepth;
    }
};
```



## [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)

#### Java

```java
class Solution {
    private void exchange(TreeNode node) {  // 对每个节点进行翻转
        if (node == null) return;
        TreeNode tmp = node.left;
        node.left = node.right;
        node.right = tmp;
    }

    public TreeNode invertTree(TreeNode root) {
        if (root == null) return root;
        exchange(root);
        invertTree(root.left);
        invertTree(root.right);
        return root;
    }
}
```

#### C++

```c++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == nullptr) return root;
        TreeNode *tmp = root->left;
        root->left = root->right;
        root->right = tmp;
        tmp = nullptr;
        root->left = invertTree(root->left);
        root->right = invertTree(root->right);
        return root;
    }
};
```



## [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)

#### Java

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        Deque<TreeNode> dq = new LinkedList<>(); // 不能用ArrayDeque, ArrayDeque要求其元素非null !!!!
        dq.offerFirst(root.left);
        dq.offerLast(root.right);
        while (!dq.isEmpty()) {
            TreeNode left = dq.pollFirst();
            TreeNode right = dq.pollLast();
            if (left == null && right == null) continue;
            if (left == null ^ right == null) return false;
            if (left.val != right.val) return false;

            dq.offerFirst(left.right);
            dq.offerLast(right.left);
            dq.offerFirst(left.left);
            dq.offerLast(right.right);
        }
        return true;
    }
}
```

```java
class Solution {
    private boolean compareTwoTrees(TreeNode left, TreeNode right) {
        if (left == null ^ right == null) return false;
        if (left == null & right == null) return true;
        if (left.val != right.val) return false;
        if (!compareTwoTrees(left.left, right.right)) return false;
        if (!compareTwoTrees(left.right, right.left)) return false;
        return true;
    }

    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return compareTwoTrees(root.left, root.right);
    }
}
```

#### C++

```c++
class Solution {
    bool ok(TreeNode* left, TreeNode* right) {
        if (left == nullptr ^ right == nullptr) return false;
        if (!left & !right) return true;
        if (left->val != right->val) return false;
        if (!ok(left->left, right->right)) return false;
        if (!ok(left->right, right->left)) return false;
        return true;
    }
public:
    bool isSymmetric(TreeNode* root) {
        if (!root) return true;
        return ok(root->left, root->right);
    }
};
```



## [222. Count Complete Tree Nodes](https://leetcode.com/problems/count-complete-tree-nodes/)

#### Java

```java
class Solution {
    private int cnt = 0;
    private void dfs(TreeNode node) {
        if (node == null) return;
        cnt ++;
        dfs(node.left);
        dfs(node.right);
    }
    public int countNodes(TreeNode root) {
        dfs(root);
        return cnt;
    }
}
```

#### C++

```c++
class Solution {
    void traversal(int& cnt, TreeNode* root) {
        if (!root) return;
        cnt ++;
        traversal(cnt, root->left);
        traversal(cnt, root->right);
    }
public:
    int countNodes(TreeNode* root) {
        int cnt = 0;
        traversal(cnt, root);
        return cnt;
    }
};
```



## [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)

#### Java

```java
class Solution {
    private int getDepth(TreeNode node) {  // get tree's max depth
        if (node == null) return 0;
        return 1 + Math.max(getDepth(node.left), getDepth(node.right));
    }

    public boolean isBalanced(TreeNode root) {  // a tree is Balanced <=> its all subtrees are balanced
        if (root == null) return true;
        int leftDepth = getDepth(root.left);
        int rightDepth = getDepth(root.right);
        if (Math.abs(leftDepth - rightDepth) > 1) return false;
        boolean leftRes = isBalanced(root.left);
        boolean rightRes = isBalanced(root.right);
        return leftRes && rightRes;
    }
}
```

*Pruning Optimization*

calculating height and judge balance happen at same time

```java
class Solution {
    private int getDepth(TreeNode node) {
        if (node == null) return 0;
        int leftDepth = getDepth(node.left);
        if (leftDepth == -1) return -1;
        int rightDepth = getDepth(node.right);
        if (rightDepth == -1) return -1;
        if (Math.abs(leftDepth - rightDepth) > 1) return -1;
        return 1 + Math.max(leftDepth, rightDepth);
    }

    public boolean isBalanced(TreeNode root) {
        return getDepth(root) != -1;
    }
}
```

#### C++

```c++
class Solution {
    int getHeight(TreeNode* root) {
        if (!root) return 0;
        int leftHeight = getHeight(root->left);
        if (leftHeight == -1) return -1;
        int rightHeight = getHeight(root->right);
        if (rightHeight == -1) return -1;
        if (abs(leftHeight - rightHeight) <= 1) return max(leftHeight, rightHeight) + 1;
        else return -1;
    }
public:
    bool isBalanced(TreeNode* root) {
        int height = getHeight(root);
        return height != -1;
    }
};
```



## [257. Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/)

#### Java

*backtrack*

```java
class Solution {
    private void traversal(List<String> ans, List<Integer> path, TreeNode root) {
        if (root == null) return;
        path.add(root.val);
        if (root.left == null && root.right == null) ans.add(path.stream().map(String::valueOf).collect(Collectors.joining("->")));
        else {
            traversal(ans, path, root.left);
            traversal(ans, path, root.right);
        }
        path.removeLast();
    }

    public List<String> binaryTreePaths(TreeNode root) {
        List<String> ans = new ArrayList<>();
        traversal(ans, new ArrayList<Integer>(), root);
        return ans;
    }
}
```

```java
class Solution {
    private void traversal(List<String> ans, String path, TreeNode root) {
        if (root == null) return;
        if (root.left == null && root.right == null) {
            path += root.val;
            ans.add(path);
        } else {
            path += root.val + "->";
            traversal(ans, path, root.left);
            traversal(ans, path, root.right);
        }
    }

    public List<String> binaryTreePaths(TreeNode root) {
        List<String> ans = new ArrayList<>();
        traversal(ans, "", root);
        return ans;
    }
}
```



use two stacks, one for recording TreeNodes, other for records paths

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        if (root == null) return res;
        List<TreeNode> stkTree = new ArrayList<>();
        List<String> stkPath = new ArrayList<>();
        stkTree.add(root);
        stkPath.add("" + root.val);
        while (!stkTree.isEmpty()) {
            TreeNode cur = stkTree.removeLast();
            String curPath = stkPath.removeLast();
            if (cur.right != null) {
                stkTree.add(cur.right);
                stkPath.add(curPath + "->" + cur.right.val);
            }
            if (cur.left != null) {
                stkTree.add(cur.left);
                stkPath.add(curPath + "->" + cur.left.val);
            }
            
            if (cur.left == null && cur.right == null) res.add(curPath);
        }
        return res;
    }
}
```

#### C++

```c++
class Solution {
    void traversal(vector<vector<int>>& ans, vector<int>& path, TreeNode* root) { // path 传引用需要恢复现场
        if (!root) return;
        path.push_back(root->val);
        if (!root->left && !root->right) ans.push_back(path);
        else {
            traversal(ans, path, root->left);
            traversal(ans, path, root->right);
        }
        path.pop_back();
    }
public:
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<vector<int>> ans;
        vector<int> path;
        traversal(ans, path, root);
        
        vector<string> result;
        for (vector<int>& path : ans) {
            stringstream ss;
            for (size_t i = 0; i < path.size(); ++i) {
                if (i != 0) ss << "->";
                ss << path[i];
            }
            result.push_back(ss.str());
        }
        return result;
    }
};
```

```c++
class Solution {
    void traversal(vector<vector<int>>& ans, vector<int> path, TreeNode* root) { // path 传值则不需要恢复现场
        if (!root) return;
        path.push_back(root->val);
        if (!root->left && !root->right) ans.push_back(path);
        else {
            traversal(ans, path, root->left);
            traversal(ans, path, root->right);
        }
    }
public:
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<vector<int>> ans;
        vector<int> path;
        traversal(ans, path, root);

        vector<string> result;
        for (vector<int>& path : ans) {
            stringstream ss;
            for (size_t i = 0; i < path.size(); ++i) {
                if (i != 0) ss << "->";
                ss << path[i];
            }
            result.push_back(ss.str());
        }

        return result;
    }
};
```



## [404. Sum of Left Leaves](https://leetcode.com/problems/sum-of-left-leaves/)

#### Java

递归 (深度遍历)

```java
class Solution {
    private int res;

    private void dfs(TreeNode node) {
        if (node == null) return;
        if (node.left != null && node.left.left == null && node.left.right == null) res += node.left.val;  // 判断当前节点的左孩子是不是目标
        if (node.left != null) dfs(node.left);
        if (node.right != null) dfs(node.right);
    }

    public int sumOfLeftLeaves(TreeNode root) {
        res = 0;
        dfs(root);
        return res;
    }
}
```

递归

```java
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null) return 0;
        int leftRes = sumOfLeftLeaves(root.left);
        if (root.left != null && root.left.left == null && root.left.right == null) leftRes = root.left.val;
        int rightRes = sumOfLeftLeaves(root.right);
        return rightRes + leftRes;
    }
}
```

stack + 迭代

```java
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        int res = 0;
        if (root == null) return res;
        List<TreeNode> stk = new ArrayList<>();
        stk.addLast(root);
        while (!stk.isEmpty()) {
            TreeNode cur = stk.removeLast();
            if (cur.left != null && cur.left.left == null && cur.left.right == null) res += cur.left.val;
            if (cur.left != null) stk.addLast(cur.left);
            if (cur.right != null) stk.addLast(cur.right);
        }
        return res;
    }
}
```

#### C++

```c++
class Solution {
    void traversal(int& sumLeft, TreeNode* root) {
        if (!root) return;
        if (root->left && !root->left->left && !root->left->right) sumLeft += root->left->val;
        traversal(sumLeft, root->left);
        traversal(sumLeft, root->right);
    }
public:
    int sumOfLeftLeaves(TreeNode* root) {
        int sumLeft = 0;
        traversal(sumLeft, root);
        return sumLeft;
    }
};
```



## [513. Find Bottom Left Tree Value](https://leetcode.com/problems/find-bottom-left-tree-value/)

#### Java

层序遍历

```java
class Solution {
    private int firstEle = 0;
    private int lvlCnt = 0;
    private void traversal(TreeNode node, int deep) {
        if (node == null) return;
        deep ++;
        if (lvlCnt < deep) {  // 当条件不满足, 说明已经是访问过的 level, 故该节点不是最左
            firstEle = node.val;
            lvlCnt ++;
        }
        traversal(node.left, deep); // watch out order
        traversal(node.right, deep);
    }

    public int findBottomLeftValue(TreeNode root) {
        traversal(root, 0);
        return firstEle;
    }
}
```



```java
class Solution {
    private void traversal(List<Integer> levelLeftVal, TreeNode node, int depth) {
        if (node == null) return;
        depth ++;
        if (levelLeftVal.size() < depth) levelLeftVal.add(node.val);
        traversal(levelLeftVal, node.left, depth);
        traversal(levelLeftVal, node.right, depth);
    }

    public int findBottomLeftValue(TreeNode root) {
        List<Integer> levelLeftVal = new ArrayList<>();
        traversal(levelLeftVal, root, 0);
        return levelLeftVal.getLast();
    }
}
```



#### C++

```c++
class Solution {
    void traversal(int& lvlCnt, int& bottomLeftVal, TreeNode* root, int depth) {
        if (!root) return;
        depth ++;
        if (depth > lvlCnt) lvlCnt ++;
        if (depth == lvlCnt) bottomLeftVal = root->val;
        traversal(lvlCnt, bottomLeftVal, root->right, depth); // watch out order
        traversal(lvlCnt, bottomLeftVal, root->left, depth);
    }
public:
    int findBottomLeftValue(TreeNode* root) {
        int bottomLeftVal = 0;
        int lvlCnt = 0;
        traversal(lvlCnt, bottomLeftVal, root, 0);
        return bottomLeftVal;
    }
};
```



```c++
class Solution {
    void traversal(vector<int>& levelLeftVal, TreeNode* root, int depth) {
        if (!root) return;
        depth ++;
        if (depth > levelLeftVal.size()) levelLeftVal.push_back(0);
        levelLeftVal[depth - 1] = root->val;
        traversal(levelLeftVal, root->right, depth);
        traversal(levelLeftVal, root->left, depth);
    }
public:
    int findBottomLeftValue(TreeNode* root) {
        vector<int> levelLeftVal;
        traversal(levelLeftVal, root, 0);
        return levelLeftVal.back();
    }
};
```



## [112. Path Sum](https://leetcode.com/problems/path-sum/)

#### Java

维护一个公共的pathSum, 因此需要回溯

```java
class Solution {
    private int pathSum = 0;
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) return false;
        pathSum += root.val;
        if (root.left == null && root.right == null) {
            if (pathSum == targetSum) return true;
            else {
                pathSum -= root.val;
                return false;
            }
        } else {
            if (hasPathSum(root.left, targetSum)) return true; // Pruning Optimization
            if (hasPathSum(root.right, targetSum)) return true;
            pathSum -= root.val;
            return false;
        }
    }
}
```

让每一个节点都保存targetSum, 因此不需要回溯

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) return false;
        targetSum -= root.val;
        if (root.left == null && root.right == null) return targetSum == 0;
        if (hasPathSum(root.left, targetSum)) return true;
        if (hasPathSum(root.right, targetSum)) return true;
        return false;
    }
}
```

stack 方法, 用两个stack, 一个维护treenode, 一个维护当前节点的路径和, 二者要对应

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) return false;
        List<TreeNode> treeStk = new ArrayList<>();
        List<Integer> sumStk = new ArrayList<>();
        treeStk.add(root);
        sumStk.add(0);
        while (!treeStk.isEmpty()) {
            TreeNode cur = treeStk.removeLast();
            int curSum = sumStk.removeLast();
            curSum += cur.val;
            if (cur.left == null && cur.right == null && curSum == targetSum) return true;
            if (cur.left != null) {
                sumStk.add(curSum);
                treeStk.add(cur.left);
            }
            if (cur.right != null) {
                sumStk.add(curSum);
                treeStk.add(cur.right);
            }
        }
        return false;
    }
}
```

#### C++

```c++
class Solution {
    bool traversal(int& targetSum, int curSum, TreeNode* root) {
        if (!root) return false;
        curSum += root->val;
        if (!root->left && !root->right) {
            if (curSum == targetSum) return true;
            else return false;
        } else {
            if (traversal(targetSum, curSum, root->left)) return true;
            if (traversal(targetSum, curSum, root->right)) return true;
            return false;
        }
    }
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        return traversal(targetSum, 0, root);
    }
};
```



## Derive a binary tree from the results in pre-, post-, and in- order

### [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

#### Java

provide index of the start and end index of inoreder and postorder

```java
class Solution {
    private int[] inorder, postorder;

    private TreeNode buildTreeByInds(int ins, int ine, int pos, int poe) {  // 传入开始, 结束的下标, [ins, ine), [pos, poe)
        if (ins == ine || pos == poe) return null;
        int rootVal = postorder[poe - 1];
        int rootInd = ins;
        for (rootInd = ins; rootInd < ine; rootInd ++) if (inorder[rootInd] == rootVal) break;
        int leftIns = ins;
        int leftIne = rootInd;
        int rightIns = rootInd + 1;
        int rightIne = ine;

        int leftPos = pos;
        int leftPoe = pos - ins + rootInd;
        int rightPos = leftPoe;
        int rightPoe = poe - 1;
        return new TreeNode(rootVal, buildTreeByInds(leftIns, leftIne, leftPos, leftPoe), buildTreeByInds(rightIns, rightIne, rightPos, rightPoe));
    }

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        this.inorder = inorder; this.postorder = postorder;
        return buildTreeByInds(0, inorder.length, 0, postorder.length);
    }
}
```

slow, because copy the whole array as param

```java
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        if (inorder.length == 0) return null;
        int rootVal = postorder[postorder.length - 1];
        int rootInd = 0;
        for (rootInd = 0; rootInd < inorder.length; rootInd ++) if (inorder[rootInd] == rootVal) break;
        int[] leftInorder = Arrays.copyOfRange(inorder, 0, rootInd);
        int[] leftPostorder = Arrays.copyOfRange(postorder, 0, rootInd);
        int[] rightInorder = Arrays.copyOfRange(inorder, rootInd + 1, inorder.length);
        int[] rightPostorder = Arrays.copyOfRange(postorder, leftInorder.length, postorder.length - 1);
        return new TreeNode(rootVal, buildTree(leftInorder, leftPostorder), buildTree(rightInorder, rightPostorder));
    }
}
```

#### C++

```c++
class Solution {
    TreeNode* build(vector<int>& inorder, int inL, int inR, vector<int>&postorder, int postL, int postR) {
        if (inL > inR || postL > postR) return nullptr;
        TreeNode* root = new TreeNode(postorder[postR]);
        int inM = 0; // 先确定 inorder 中的root idx
        for (; inM < inorder.size(); inM ++) if (inorder[inM] == root->val) break;
        int leftSz = inM - inL; // 在 inorder 中计算左子树的size
        int rightSz = inR - inM; // 右子树的 size

        int leftInR = inM - 1; // 根据 inM 确定 leftInR
        int leftInL = leftInR - leftSz + 1; // 结合 size 确定leftInL
        int rightInL = inM + 1;
        int rightInR = rightInL + rightSz - 1;

        int rightPostR = postR - 1; // 根据 postR 确定 rightPostR
        int rightPostL = rightPostR - rightSz + 1;
        int leftPostL = postL; // 根据 postL 确定 leftPostL
        int leftPostR = leftPostL + leftSz - 1;

        root->left = build(inorder, leftInL, leftInR, postorder, leftPostL, leftPostR);
        root->right = build(inorder, rightInL, rightInR, postorder, rightPostL, rightPostR);
        return root;
    }
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        return build(inorder, 0, inorder.size() - 1, postorder, 0, postorder.size() - 1);
    }
};

/*
inorder:   left[9] root[3] right[15 20 7]
postorder: left[9] right[15 7 20] root[3]
*/
```



### [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

#### Java

```java
class Solution {
    private int[] preorder, inorder;

    private TreeNode buildByInds(int preS, int preE, int inS, int inE) {
        if (preS == preE || inS == inE) return null;
        int rootVal = preorder[preS];
        int rootInd = 0;
        for (rootInd = 0; rootInd < inorder.length; rootInd ++) if (inorder[rootInd] == rootVal) break;
        // left tree
        int leftPreS = preS + 1;
        int leftPreE = (rootInd - inS) + preS + 1;  // 由inorder获取left tree 的总元素个数(rootInd - inS), 将其加上 leftPreS (preS + 1) 就是preE !!!!!
        int leftInS = inS;
        int leftInE = rootInd;

        // right tree
        int rightPreS = leftPreE;
        int rightPreE = preE;
        int rightInS = rootInd + 1;
        int rightInE = inE;

        return new TreeNode(rootVal, buildByInds(leftPreS, leftPreE, leftInS, leftInE), buildByInds(rightPreS, rightPreE, rightInS, rightInE));
    }

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        this.inorder = inorder;
        return buildByInds(0, preorder.length, 0, inorder.length);
    }
}
```

#### C++

```c++
class Solution {
    TreeNode* build(vector<int>& preorder, int preL, int preR, vector<int>& inorder, int inL, int inR) {
        if (preL > preR || inL > inR) return nullptr;
        TreeNode* root = new TreeNode(preorder[preL]);
        int inM = 0;
        for (; inM < inorder.size(); inM ++) if (inorder[inM] == root->val) break;
        int leftSize = inM - inL;
        int rightSize = inR - inM;

        int leftInR = inM - 1;
        int leftInL = leftInR - leftSize + 1;
        int rightInL = inM + 1;
        int rightInR = rightInL + rightSize - 1;

        int leftPreL = preL + 1;
        int leftPreR = leftPreL + leftSize - 1;
        int rightPreR = preR;
        int rightPreL = rightPreR - rightSize + 1;

        root->left = build(preorder, leftPreL, leftPreR, inorder, leftInL, leftInR);
        root->right = build(preorder, rightPreL, rightPreR, inorder, rightInL, rightInR);
        return root;
    }
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return build(preorder, 0, preorder.size() - 1, inorder, 0, inorder.size() - 1);
    }
};
```



## [654. Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/)

#### Java

```java
class Solution {
    private TreeNode build(int[] nums, int l, int r) {
        if (l > r) return null;
        int m = l;
        int max = nums[m];
        for (int i = l; i <= r; i ++) {
            if (nums[i] > max) {
                max = nums[i];
                m = i;
            }
        }
        TreeNode root = new TreeNode(max);
        root.left = build(nums, l, m - 1);
        root.right = build(nums, m + 1, r);
        return root;
    }
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        return build(nums, 0, nums.length - 1);
    }
}
```

#### C++

```c++
class Solution {
    TreeNode* build(vector<int>& nums, int l , int r) {
        if (l > r) return nullptr;
        int m = l;
        int max = nums[m];
        for (int i = l; i <= r; i ++) {
            if (nums[i] > max) {
                max = nums[i];
                m = i;
            }
        }
        TreeNode* root = new TreeNode(max);
        root->left = build(nums, l, m - 1);
        root->right = build(nums, m + 1, r);
        return root;
    }
public:
    TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
        return build(nums, 0, nums.size() - 1);
    }
};
```



## [617. Merge Two Binary Trees](https://leetcode.com/problems/merge-two-binary-trees/)

#### Java

不修改原数据

```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if (root1 == null && root2 == null) return null;
        TreeNode root = new TreeNode();
        root.val = (root1 == null ? 0 : root1.val) + (root2 == null ? 0 : root2.val);
        root.left = mergeTrees(root1 == null ? null : root1.left, root2 == null ? null : root2.left);
        root.right = mergeTrees(root1 == null ? null : root1.right, root2 == null ? null : root2.right);
        return root;
    }
}
```



修改了原数据

```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if (root1 == null && root2 == null) return null;
        else if (root1 != null && root2 == null) return root1;
        else if (root1 == null && root2 != null) return root2;
        else {
            root1.val += root2.val;
            root1.left = mergeTrees(root1.left, root2.left);
            root1.right = mergeTrees(root1.right, root2.right);
            return root1;
        }
    }
}
```



不修改原数据

```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if (root1 == null && root2 == null) return null;
        TreeNode root = new TreeNode();
        Queue<TreeNode> q = new LinkedList<>(); // 这里只能用 LinkedList 实现, 因为 ArrayDeque 元素不能为 null
        q.offer(root);
        q.offer(root1);
        q.offer(root2);
        while (!q.isEmpty()) {
            TreeNode cur = q.poll();
            TreeNode cur1 = q.poll();
            TreeNode cur2 = q.poll();
            cur.val = (cur1 == null ? 0 : cur1.val) + (cur2 == null ? 0 : cur2.val); // 先把val搞定
            // 处理 left
            if (cur1 != null && cur1.left != null || cur2 != null && cur2.left != null) {
                cur.left = new TreeNode();
                q.offer(cur.left);
                q.offer(cur1 == null || cur1.left == null ? null : cur1.left);
                q.offer(cur2 == null || cur2.left == null ? null : cur2.left);
            }
			// 处理 right
            if (cur1 != null && cur1.right != null || cur2 != null && cur2.right != null) {
                cur.right = new TreeNode();
                q.offer(cur.right);
                q.offer(cur1 == null || cur1.right == null ? null : cur1.right);
                q.offer(cur2 == null || cur2.right == null ? null : cur2.right);
            }
        }
        return root;
    }
}
```



修改了原数据

```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if (root1 == null || root2 == null) return root1 == null ? root2 : root1;
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root1);
        q.offer(root2);
        while (!q.isEmpty()) {
            TreeNode cur1 = q.poll();
            TreeNode cur2 = q.poll();
            cur1.val += cur2.val;
            if (cur2 != null && cur2.left != null) {
                if (cur1.left == null) cur1.left = new TreeNode();
                q.offer(cur1.left);
                q.offer(cur2.left);
            }
            if (cur2 != null && cur2.right != null) {
                if (cur1.right == null) cur1.right = new TreeNode();
                q.offer(cur1.right);
                q.offer(cur2.right);
            }
        }
        return root1;
    }
}
```

#### C++

```c++
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if (!root1 && !root2) return nullptr;
        TreeNode* root = new TreeNode((root1 ? root1->val : 0) + (root2 ? root2->val : 0));
        root->left = mergeTrees((root1 ? root1->left : nullptr), (root2 ? root2->left : nullptr));
        root->right = mergeTrees((root1 ? root1->right : nullptr), (root2 ? root2->right : nullptr));
        return root;
    }
};
```



## [700. Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/)

#### Java

```java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        if (root == null) return null;
        else if (root.val == val) return root;
        else {
            if (root.val > val) return searchBST(root.left, val);
            if (root.val < val) return searchBST(root.right, val);
        }
        return null;
    }
}
```

#### C++

```c++
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if (!root) return nullptr;
        if (root->val == val) return root;
        else if (root->val > val) return searchBST(root->left, val);
        else return searchBST(root->right, val);
    }
};
```



## [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)

#### Java

二叉搜索树把它中序遍历出来就是一个递增序列

```java
class Solution {
    private TreeNode prev;
    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;
        if (!isValidBST(root.left)) return false;
        if (prev != null && prev.val >= root.val) return false;
        prev = root;
        if (!isValidBST(root.right)) return false;
        return true;
    }
}
```



```java
class Solution {
    private Integer preVal = null;

    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;
        
        boolean resLeft = true;
        if (root.left != null) resLeft = isValidBST(root.left);
        if (!resLeft) return false;

        boolean curRes = true;
        if (preVal == null || root.val > preVal) preVal = root.val;
        else curRes = false;
        if (!curRes) return false;

        boolean resRight = true;
        if (root.right != null) resRight = isValidBST(root.right);
        if (!resRight) return false;

        return true;
    }
}
```



```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;
        Integer preVal = null;
        LinkedList<TreeNode> stk = new LinkedList<>();
        stk.add(root);
        while (!stk.isEmpty()) {
            TreeNode cur = stk.removeLast();
            if (cur != null) {
                if (cur.right != null) stk.add(cur.right);
                stk.add(cur);
                stk.add(null);  // label
                if (cur.left != null) stk.add(cur.left);
            } else {
                cur = stk.removeLast();
                if (preVal == null || preVal < cur.val) preVal = cur.val;
                else return false;
            }
        }
        return true;
    }
}
```



#### C++

```c++
class Solution {
    TreeNode* pre = nullptr;
public:
    bool isValidBST(TreeNode* root) {
        if (!root) return true;
        if (!isValidBST(root->left)) return false;
        if (pre && pre->val >= root->val) return false;
        pre = root;
        if (!isValidBST(root->right)) return false;
        return true;
    }
};
```



#### Kotlin

```kotlin
class Solution {
    var prev: TreeNode? = null
    fun isValidBST(root: TreeNode?): Boolean {
        if (root == null) return true
        if (!isValidBST(root.left)) return false
        if (prev != null && prev!!.`val` >= root!!.`val`) return false // 这里不使用!!会报错, 因为实现没有检测非空就不能比较大小
        prev = root
        if (!isValidBST(root?.right)) return false
        return true
    }
}
```



## [530. Minimum Absolute Difference in BST](https://leetcode.com/problems/minimum-absolute-difference-in-bst/)

#### Java

```java
class Solution {
    private Integer preVal = null;
    private int minDiff = Integer.MAX_VALUE;
    private void traversal(TreeNode root) {
        if (root == null) return;
        if (root.left != null) traversal(root.left);
        if (preVal != null) {
            int curDiff = root.val - preVal;
            minDiff = Math.min(curDiff, minDiff);
        }
        preVal = root.val;
        if (root.right != null) traversal(root.right);
    }

    public int getMinimumDifference(TreeNode root) {
        traversal(root);
        return minDiff;
    }
}
```



#### C++

```c++
class Solution {
    void traversal(int& minDiff, TreeNode*& pre, TreeNode* root) {
        if (!root) return;
        traversal(minDiff, pre, root->left);
        if (pre) minDiff = min(minDiff, root->val - pre->val);
        pre = root;
        traversal(minDiff, pre, root->right);
    }
public:
    int getMinimumDifference(TreeNode* root) {
        int minDiff = INT_MAX;
        TreeNode* pre = nullptr;
        traversal(minDiff, pre, root);
        return minDiff;
    }
};
```



## [501. Find Mode in Binary Search Tree](https://leetcode.com/problems/find-mode-in-binary-search-tree/)

#### Java

二叉搜索树相当于链表 (中序遍历)

```java
class Solution {
    private TreeNode pre;
    private List<Integer> modes;
    private int maxCnt;
    private int cnt;
    private void traversal(TreeNode root) {
        if (root == null) return;
        traversal(root.left);
        if (pre != null && pre.val == root.val) cnt ++;
        else cnt = 1;
        pre = root;
        if (cnt > maxCnt) {
            modes.clear();
            maxCnt = cnt;
        }
        if (maxCnt == cnt) modes.add(root.val);
        traversal(root.right);
    }

    public int[] findMode(TreeNode root) {
        pre = null;
        modes = new ArrayList<>();
        maxCnt = 0;
        cnt = 0;
        traversal(root);
        return modes.stream().mapToInt(x->x).toArray(); // .mapToInt(Integer::intValue) also be ok
    }
}
```

#### C++

```c++
class Solution {
    void traversal(vector<int>& modes, int& maxCnt, int& cnt, TreeNode*& pre, TreeNode* root) {
        if (!root) return;
        traversal(modes, maxCnt, cnt, pre, root->left);
        if (pre && pre->val == root->val) cnt ++;
        else cnt = 1;
        pre = root;
        if (maxCnt < cnt) {
            modes.clear();
            maxCnt = cnt;
        }
        if (maxCnt == cnt) modes.push_back(root->val);
        traversal(modes, maxCnt, cnt, pre, root->right);
    }
public:
    vector<int> findMode(TreeNode* root) {
        vector<int> modes;
        int maxCnt = 0;
        int cnt = 0;
        TreeNode* pre = nullptr;
        traversal(modes, maxCnt, cnt, pre, root);
        return modes;
    }
};
```



## [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

#### Java

slower

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) return root;
        TreeNode leftRes = lowestCommonAncestor(root.left, p, q);
        TreeNode rightRes = lowestCommonAncestor(root.right, p, q);
        if (leftRes != null && rightRes != null) return root;
        else if (leftRes == null) return rightRes;
        else return leftRes;
    }
}
```



faster

```java
class Solution {
    private boolean find(TreeNode root, TreeNode p) {
        if (root == null) return false;
        if (root == p) return true;
        if (find(root.left, p)) return true;
        if (find(root.right, p)) return true;
        return false;
    }
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) return null;
        TreeNode leftRes = lowestCommonAncestor(root.left, p, q);
        if (leftRes != null) return leftRes;
        TreeNode rightRes = lowestCommonAncestor(root.right, p, q);
        if (rightRes != null) return rightRes;
        if (find(root, p) && find(root, q)) return root;
        return null;
    }
}
```



#### C++

slower

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root == p || root == q) return root;
        TreeNode* res_left = lowestCommonAncestor(root->left, p, q);
        TreeNode* res_right = lowestCommonAncestor(root->right, p, q);
        if (res_left && res_right) return root;
        else if (res_left) return res_left;
        else return res_right;
    }
};
```



faster

```c++
class Solution {
    bool find(TreeNode* root, TreeNode* p) {
        if (!root) return false;
        if (root == p) return true;
        if (find(root->left, p)) return true;
        if (find(root->right, p)) return true;
        return false;
    }
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root) return nullptr;
        TreeNode* leftRes = lowestCommonAncestor(root->left, p, q);
        if (leftRes) return leftRes;
        TreeNode* rightRes = lowestCommonAncestor(root->right, p, q);
        if (rightRes) return rightRes;
        if (find(root, p) && find(root, q)) return root;
        return nullptr;
    }
};
```



## [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

#### Java

前序遍历, 当 $root.val \in (p.val, q.val)$ or $(q.val, p.val)$ 时 root 就是答案



```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root.val > p.val && root.val > q.val) return lowestCommonAncestor(root.left, p, q);
        if (root.val < p.val && root.val < q.val) return lowestCommonAncestor(root.right, p, q);
        return root;
    }
}
```



```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        while (true) {
            if (root.val > p.val && root.val > q.val) root = root.left;
            else if (root.val < p.val && root.val < q.val) root = root.right;
            else return root;
        }
    }
}
```



#### C++

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root->val >= p->val && root->val <= q->val || root->val >= q->val && root->val <= p->val) return root;
        if (root->val > p->val && root->val > q->val) return lowestCommonAncestor(root->left, p, q);
        if (root->val < p->val && root->val < q->val) return lowestCommonAncestor(root->right, p, q);
        return NULL;
    }
};
```



## [701. Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/)

#### Java

insert in-place

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) return new TreeNode(val);
        if (root.val > val) root.left = insertIntoBST(root.left, val);
        if (root.val < val) root.right = insertIntoBST(root.right, val);
        return root;
    }
}
```



```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) return new TreeNode(val);
        TreeNode parent = null;
        TreeNode cur = root;
        while (cur != null) {
            parent = cur;
            if (cur.val > val) cur = cur.left;    
            else cur = cur.right;
        }
        if (parent.val > val) parent.left = new TreeNode(val);
        if (parent.val < val) parent.right = new TreeNode(val);
        return root;
    }
}
```



#### C++

```c++
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if (!root) return new TreeNode(val);
        else if (val < root->val) root->left = insertIntoBST(root->left, val);
        else root->right = insertIntoBST(root->right, val);
        return root;
    }
};
```



```c++
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if (!root) return new TreeNode(val);
        TreeNode* pre = nullptr;
        TreeNode* cur = root;
        while (cur) {
            pre = cur;
            if (val < cur->val) cur = cur->left;
            else cur = cur->right;
        }
        if (val < pre->val) pre->left = new TreeNode(val);
        else pre->right = new TreeNode(val);
        return root;
    }
};
```



## [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)

#### Java

删除逻辑: 找到 key, 把 key 的右子树接到 key 上 (替换), 把 key 的左子树接到右子树的最左端

递归 (可以直接对 cur 修改)

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) return null;
        if (root.val == key) {
            TreeNode reservedLeft = root.left;
            TreeNode reservedRight = root.right;
            if (reservedRight != null) {
                TreeNode tmp = reservedRight;
                while (tmp != null && tmp.left != null) tmp = tmp.left;
                tmp.left = reservedLeft;
                return reservedRight;
            } else return reservedLeft;
        } else if (root.val > key) root.left = deleteNode(root.left, key);
		else root.right = deleteNode(root.right, key);
        return root;
    }
}
```

迭代 (需要搞一个 pre, 来完成对 cur 的修改)

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        TreeNode dummyRoot = new TreeNode(0, root, null);
        TreeNode pre = dummyRoot;
        TreeNode cur = root;
        while (cur != null && cur.val != key) {
            pre = cur;
            if (cur.val > key) {
                cur = cur.left;
            } else {
                cur = cur.right;
            }
        }
        if (cur == null) return dummyRoot.left;
        else {
            TreeNode reservedLeft = cur.left;
            TreeNode reservedRight = cur.right;
            if (reservedRight != null) {
                TreeNode tmp = reservedRight;
                while (tmp != null && tmp.left != null) tmp = tmp.left;
                tmp.left = reservedLeft;
                if (pre.left == cur) pre.left = reservedRight;
                else pre.right = reservedRight;
            } else {
                if (pre.left == cur) pre.left = reservedLeft;
                else pre.right = reservedLeft;
            }
        }
        return dummyRoot.left;
    }
}
```

#### C++

```c++
class Solution {
public:
    TreeNode* deleteNode(TreeNode* root, int val) {
        if (!root) return nullptr;
        if (root->val == val) {
            TreeNode* left = root->left;
            TreeNode* right = root->right;
            delete root; // delete data in heap
            if (!right) return left;
            TreeNode* p = right;
            while (p && p->left) p = p->left;
            p->left = left;
            p = nullptr;
            return right;
        } else if (val < root->val) root->left = deleteNode(root->left, val);
        else root->right = deleteNode(root->right, val);
        return root;
    }
};
```



## [669. Trim a Binary Search Tree](https://leetcode.com/problems/trim-a-binary-search-tree/)

#### Java

```java
class Solution {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        if (root == null) return null;
        else if (root.val < low) return trimBST(root.right, low, high);
        else if (high < root.val) return trimBST(root.left, low, high);
        else {
            root.left = trimBST(root.left, low, high);
            root.right = trimBST(root.right, low, high);
            return root;
        }
    }
}
```



```java
class Solution {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        if (root == null) return null;
        // deal with root
        while (root != null && (root.val < low || root.val > high)) {
            if(root.val < low) root = root.right;
            else root = root.left;
        }
        if (root == null) return null;
        TreeNode cur = null;
        TreeNode pre = null;

        // to left
        pre = root;
        cur = root.left;
        while (cur != null) {
            if (low <= cur.val) {
                pre = cur;
                cur = cur.left;
            } else {
                pre.left = cur.right;
                cur = cur.right;
            }
        }
        // reset
        pre = root;
        cur = root.right;
        // to right
        while (cur != null) {
            if (cur.val <= high) {
                pre = cur;
                cur = cur.right;
            } else {
                pre.right = cur.left;
                cur = cur.left;
            }
        }
        return root;
    }
}
```



#### C++

```c++
class Solution {
    void deleteTree(TreeNode* root) { // Due to the problem solving mechanism of leetcode, adding code to delete data on the heap cannot be AC, but it is necessary in practical writing
        // if (!root) return;
        // TreeNode* left = root->left;
        // TreeNode* right = root->right;
        // delete root; root = nullptr;
        // deleteTree(left); left = nullptr;
        // deleteTree(right); right = nullptr;
    }
public:
    TreeNode* trimBST(TreeNode* root, int low, int high) {
        if (!root) return nullptr;
        else if (low <= root->val && root->val <= high) {
            root->left = trimBST(root->left, low, high);
            root->right = trimBST(root->right, low, high);
            return root;
        } else if (root->val < low) {
            TreeNode* right = root->right;
            root->right = nullptr;
            if (!right) return nullptr;
            deleteTree(root); root = nullptr;
            return trimBST(right, low, high);
        } else { // high < root->val
            TreeNode* left = root->left;
            root->left = nullptr;
            if (!left) return nullptr;
            deleteTree(root); root = nullptr;
            return trimBST(left, low, high);
        }
    }
};
```



```c++
class Solution {
    void deleteTree(TreeNode* root) {
        // if (!root) return;
        // TreeNode* left = root->left;
        // TreeNode* right = root->right;
        // delete root; root = nullptr;
        // deleteTree(left); left = nullptr;
        // deleteTree(right); right = nullptr;
    }
public:
    TreeNode* trimBST(TreeNode* root, int low, int high) {
        while (root && !(low <= root->val && root->val <= high)) {
            if (root->val < low) {
                TreeNode* right = root->right;
                root->right = nullptr;
                deleteTree(root);
                root = right;
            } else {
                TreeNode* left = root->left;
                root->left = nullptr;
                deleteTree(root);
                root = left;
            }
        }
        if (!root) return nullptr;
        
        TreeNode* pre = root;
        TreeNode* cur = root->left;
        while (cur) {
            if (cur->val < low) {
                TreeNode* right = cur->right;
                cur->right = nullptr;
                deleteTree(cur);
                cur = right;
                pre->left = cur;
            } else {
                pre = cur;
                cur = cur->left;
            }
        }
        pre = root;
        cur = root->right;
        while (cur) {
            if (high < cur->val) {
                TreeNode* left = cur->left;
                cur->left = nullptr;
                deleteTree(cur);
                cur = left;
                pre->right = cur;
            } else {
                pre = cur;
                cur = cur->right;
            }
        }
        return root;
    }
};
```



## [108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

#### Java

```java
class Solution {
    private TreeNode build(int[] nums, int l, int r) {
        if (l > r) return null;
        int m = (l + r) / 2;
        return new TreeNode(nums[m], build(nums, l, m - 1), build(nums, m + 1, r));
    }
    public TreeNode sortedArrayToBST(int[] nums) {
        return build(nums, 0, nums.length - 1);
    }
}
```



```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        Queue<Integer> qL = new ArrayDeque<>();
        qL.offer(0);
        Queue<Integer> qR = new ArrayDeque<>();
        qR.offer(nums.length - 1);
        Queue<TreeNode> qT = new ArrayDeque<>();
        TreeNode root = new TreeNode();
        qT.offer(root);
        while (!qT.isEmpty()) {
            TreeNode cur = qT.poll();
            int l = qL.poll();
            int r = qR.poll();
            int m = (l + r) / 2;
            cur.val = nums[m];
            if (l <= m - 1) {
                cur.left = new TreeNode();
                qT.offer(cur.left);
                qL.offer(l);
                qR.offer(m - 1);
            }
            if (m + 1 <= r) {
                cur.right = new TreeNode();
                qT.offer(cur.right);
                qL.offer(m + 1);
                qR.offer(r);
            }
        }
        return root;
    }
}
```



#### C++

```c++
class Solution {
    TreeNode* build(vector<int>& nums, int l, int r) {
        if (l > r) return nullptr;
        int m = (l + r) / 2;
        return new TreeNode(nums[m], build(nums, l, m - 1), build(nums, m + 1, r));
    }
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return build(nums, 0, nums.size() - 1);
    }
};
```



```c++
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        TreeNode* root = new TreeNode();
        queue<TreeNode*> qT;
        qT.push(root);
        queue<int> qL;
        qL.push(0);
        queue<int> qR;
        qR.push(nums.size() - 1);
        while (!qT.empty()) {
            TreeNode* cur = qT.front();
            int l = qL.front();
            int r = qR.front();
            qT.pop();
            qL.pop();
            qR.pop();
            int m = (l + r) / 2;
            cur->val = nums[m];
            if (l <= m - 1) {
                cur->left = new TreeNode();
                qT.push(cur->left);
                qL.push(l);
                qR.push(m - 1);
            }
            if (m + 1 <= r) {
                cur->right = new TreeNode();
                qT.push(cur->right);
                qL.push(m + 1);
                qR.push(r);
            }
        }
        return root;
    }
};
```



## [538. Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/)

#### Java

*reversing-inorder traversal*

```java
class Solution {
    private int curSum = 0; // global

    private void traversal(TreeNode root) {
        if (root == null) return;
        traversal(root.right);
        root.val += curSum;
        curSum = root.val;
        traversal(root.left);
    }

    public TreeNode convertBST(TreeNode root) {
        traversal(root);
        return root;
    }
}

```



```java
class Solution { // curSum is not global, they are independent in each node
    private int traversal(TreeNode root, int curSum) {  // 每一个节点向上返回当前的累加和
        if (root == null) return curSum;
        if (root.right != null) curSum = traversal(root.right, curSum);  // curSum 更新成右子树的累加和
        root.val += curSum;  // 更新 当前结点 要在处理左子树之前完成对当前结点的更新
        curSum = root.val;  // 更新 累加和
        if (root.left != null) curSum = traversal(root.left, curSum);  // 更新左子树的累加和
        return curSum;  // 向上返回的值应是处理完左子树之后的 curSum
    }
    public TreeNode convertBST(TreeNode root) {
        traversal(root, 0);
        return root;
    }
}
```



```java
class Solution {
    public TreeNode convertBST(TreeNode root) {
        if (root == null) return null;
        List<TreeNode> stk = new ArrayList<>();
        stk.add(root);
        int curSum = 0;
        while (!stk.isEmpty()) {
            TreeNode cur = stk.removeLast();
            if (cur != null) {
                if (cur.left != null) stk.add(cur.left);
                stk.add(cur);
                stk.add(null);
                if (cur.right != null) stk.add(cur.right);
            } else {
                cur = stk.removeLast();
                cur.val += curSum;
                curSum = cur.val;
            }
        }
        return root;
    }
}
```



#### C++

```c++
class Solution {
    void traversal (TreeNode* root, int& curSum) {
        if (!root) return;
        traversal(root->right, curSum);
        root->val += curSum;
        curSum = root->val;
        traversal(root->left, curSum);
    }
public:
    TreeNode* convertBST(TreeNode* root) {
        int curSum = 0;
        traversal(root, curSum);
        return root;
    }
};
```



```c++
class Solution {
    int traversal (TreeNode* root, int curSum) {
        if (!root) return curSum;
        int rightSum = traversal(root->right, curSum);
        root->val += rightSum;
        int leftSum = traversal(root->left, root->val);
        return leftSum;
    }
public:
    TreeNode* convertBST(TreeNode* root) {
        traversal(root, 0);
        return root;
    }
};
```



```
   1                10 
 /   \            /   \
..    2     ==>  ..    6
    /   \            /   \
   3     4          9     4

我们考虑node 2 中的变化, 即执行 tr(2, 0) 时:
rightSum = tr(4, 0) = 4
把 node 2 的节点更新成为 2 + 4 = 6
于是, 当前的累加和为 6
把它传入 tr(3, 6) 左子树
leftSum = tr(3, 6) = 9;
向节点1返回9

所以, 从这个意义来看, leftSum, rightSum 应该命名为 sumFromLeft, sumFromRight

事实上, 这个写法相当于手动更新 curSum 变量, 因此不如设置全局变量来的优雅
```

本题与[1038. Binary Search Tree to Greater Sum Tree](https://leetcode.com/problems/binary-search-tree-to-greater-sum-tree/)相同

# Extra

## [129. Sum Root to Leaf Numbers](https://leetcode.com/problems/sum-root-to-leaf-numbers/)

#### Java

```java
class Solution {
    private void traversal(List<Integer> path, TreeNode root) {
        if (root == null) return;
        path.add(root.val);
        if (root.left == null && root.right == null) {
            long exp = 1;
            for (int i = path.size() - 1; i > 0; i --) {
                path.set(0, path.get(0) + path.get(i) * (int) exp);
                exp *= 10;
            }
        } else {
            traversal(path, root.left);
            traversal(path, root.right);
        }
        path.removeLast();
    }
    public int sumNumbers(TreeNode root) {
        List<Integer> path = new ArrayList<>();
        path.add(0);
        traversal(path, root);
        return path.get(0);
    }
}
```



#### C++

```c++
class Solution {
    void traversal(int& sum, vector<int>& path, TreeNode* root) {
        if (!root) return;
        path.push_back(root->val);
        if (!root->left && !root->right) {
            long exp = 1;
            for (int i = path.size() - 1; i > -1; i --) {
                sum += path[i] * exp;
                exp *= 10;
            }
        } else {
            traversal(sum, path, root->left);
            traversal(sum, path, root->right);
        }
        path.pop_back();
    }
public:
    int sumNumbers(TreeNode* root) {
        int sum = 0;
        vector<int> path;
        traversal(sum, path, root);
        return sum;
    }
};
```



#### Go

```go
func sumNumbers(root *TreeNode) int {
    path := []int{}
    ans := 0
    traversal(root, &ans, &path)
    return ans
}

func traversal(root *TreeNode, ans *int, path *[]int) {
    if root == nil {
        return
    }

    *path = append(*path, root.Val)

    if root.Left == nil && root.Right == nil {
        time := 1
        for i := len(*path) - 1; i > -1 ; i -- {
            *ans += (*path)[i] * time
            time *= 10
        }
    } else {
        traversal(root.Left, ans, path)
        traversal(root.Right, ans, path)
    }
    *path = (*path)[:len(*path) - 1]
}
```



## [1382. Balance a Binary Search Tree](https://leetcode.com/problems/balance-a-binary-search-tree/)

#### Java

```java
class Solution {
    public TreeNode balanceBST(TreeNode root) {
        List<Integer> lst = new ArrayList<>();
        readBST(lst, root);
        return buildBalanceBST(lst, 0, lst.size() - 1);
    }
    private void readBST(List<Integer> lst, TreeNode root) {
        if (root == null) return;
        readBST(lst, root.left);
        lst.add(root.val);
        readBST(lst, root.right);
    }
    private TreeNode buildBalanceBST(List<Integer> lst, int l, int r) {
        if (l > r) return null;
        int m = (l + r) / 2;
        TreeNode root = new TreeNode(lst.get(m));
        root.left = buildBalanceBST(lst, l, m - 1);
        root.right = buildBalanceBST(lst, m + 1, r);
        return root;
    }
}
```

*AVL Tree*

## [100. Same Tree](https://leetcode.com/problems/same-tree/)

#### Java

```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        else if (p != null && q == null) return false;
        else if (p == null && q != null) return false;
        else if (p.val != q.val) return false;
        else return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
}
```



#### C++

```c++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (!p && !q) return true;
        else if (!p ^ !q) return false;
        else if (p->val != q->val) return false;
        else return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```



#### Kotlin

```kotlin
class Solution {
    fun isSameTree(p: TreeNode?, q: TreeNode?): Boolean {
        if (p == null && q == null) return true
        if (p?.`val` != q?.`val`) return false
        else return isSameTree(p?.left, q?.left) && isSameTree(p?.right, q?.right) // 要充分利用kotlin的?.特性
    }
}
```



## [1038. Binary Search Tree to Greater Sum Tree](https://leetcode.com/problems/binary-search-tree-to-greater-sum-tree/)

#### C++

```c++
class Solution {
    int foo(TreeNode* root, int sum) {
        if (!root) return sum;
        sum = foo(root->right, sum);
        root->val += sum;
        sum = root->val;
        sum = foo(root->left, sum);
        return sum;
    }
public:
    TreeNode* bstToGst(TreeNode* root) {
        foo(root, 0);
        return root;
    }
};
```

