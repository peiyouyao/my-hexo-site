---
title: LeetCode 301-350
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

---



## [367. Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/) [Array]



## [376. Wiggle Subsequence](https://leetcode.com/problems/wiggle-subsequence/) [Greedy]



## [377. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/) [DP-Knapsack]



## [383. Ransom Note](https://leetcode.com/problems/ransom-note/) [Hash]



## [392. Is Subsequence](https://leetcode.com/problems/is-subsequence/) [DP-Sub]



## [404. Sum of Left Leaves](https://leetcode.com/problems/sum-of-left-leaves/) [Tree]



## [406. Queue Reconstruction by Height](https://leetcode.com/problems/queue-reconstruction-by-height/) [Greedy]



## [416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)

###### Java

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if ((sum & 1) == 1) return false;
        int cap = sum / 2;
        int[] dp = new int[cap + 1];
        for (int num : nums) {
            for (int c = cap; c >= num; c--) {
                dp[c] = Math.max(dp[c], dp[c - num] + num);
            }
        }
        return dp[cap] == cap;
    }
}
```



```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if ((sum & 1) == 1) return false;
        int cap = sum / 2;
        int n = nums.length;
        int[][] dp = new int[n][cap + 1];
        for (int c = nums[0]; c <= cap; c ++) dp[0][c] = nums[0];
		
        // 范围不能与 initilize 时重合
        for (int i = 1; i < n; i ++ ) {
            for (int c = 1; c <= cap; c ++) {
                dp[i][c] = Math.max( dp[i-1][c] , (c - nums[i] < 0) ? 0 : dp[i-1][c - nums[i]] + nums[i] );
            }
        }
        return dp[n - 1][cap] == cap;
    }
}
```



###### Rust

```rust
impl Solution {
    pub fn can_partition(nums: Vec<i32>) -> bool {
        let sum: i32 = nums.iter().sum();
        if sum & 1 == 1 {
            return false;
        }
        let cap: usize = sum as usize / 2;
        let n: usize = nums.len();
        let mut dp: Vec<Vec<i32>> = vec![vec![0; cap + 1]; n];
        for c in nums[0] as usize..=cap {
            dp[0][c] = nums[0];
        }
        for i in 1..n {
            for c in 0..=cap {
                dp[i][c] = dp[i - 1][c];
                if c >= nums[i] as usize {
                    dp[i][c] = dp[i][c].max(dp[i - 1][c - nums[i] as usize] + nums[i]);
                }
            }
        }
        dp[n - 1][cap] == cap as i32
    }
}
```



## [429. N-ary Tree Level Order Traversal](https://leetcode.com/problems/n-ary-tree-level-order-traversal/) [Tree]



## [430. Flatten a Multilevel Doubly Linked List](https://leetcode.cn/problems/flatten-a-multilevel-doubly-linked-list/)

#### C++

*Recursion*

```c++
class Solution {
public:
    Node* flatten(Node* head) {
        if (!head) return head;
        if (head->child) {
            Node* next = head->next;
            Node* child = head->child;
            head->child = nullptr;
            head->next = child;
            child->prev = head;
            while (child->next) child = child->next;
            child->next = next;
            if (next) next->prev = child;
        }
        Node* next = head->next;
        next = flatten(next);
        head->next = next;
        if (next) next->prev = head;
        return head;
    }
};
```



*Iteration*

```c++
class Solution {
public:
    Node* flatten(Node* head) {
        if (!head) return nullptr;
        Node* p = head;
        while (p) {
            if (p->child) {
                Node* next = p->next;
                Node* child = p->child;
                p->child = nullptr;
                p->next = child;
                child->prev = p;
                while(child->next) child = child->next;
                child->next = next;
                if (next) next->prev = child;
            }
            p = p->next;
        }
        return head;
    }
};
```



## [435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/) [Greedy]



## [445. Add Two Numbers II](https://leetcode.cn/problems/add-two-numbers-ii/)

#### C++

使用后序遍历避免翻转链表

```c++
class Solution {
    int foo(ListNode* head) { // 相当于后序遍历
        if (!head) return 0;
        int carry = foo(head->next);
        head->val += carry;
        carry = head->val / 10;
        head->val %= 10;
        return carry;
    }
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* p1 = l1;
        ListNode* p2 = l2;
        while (p1 && p2) {
            p1 = p1->next;
            p2 = p2->next;
        }
        if (!p1) { // 确保 l1 更长
            ListNode* t = l1; l1 = l2; l2 = t;
            t = p1; p1 = p2; p2 = t;
        }
        p2 = l1;
        while (p1) {
            p1 = p1->next;
            p2 = p2->next;
        }
        while (p2) {
            p2->val += l2->val;
            p2 = p2->next;
            l2 = l2->next;
        }

        int carry = foo(l1);
        if (carry) {
            l1 = new ListNode(carry, l1);
        }
        return l1;
    }
};
```



#### Go

```go
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
    swap := func(a, b **ListNode) {
        t := *a; *a = *b; *b = t;
    }
    var foo func(*ListNode) int
    foo = func(head *ListNode) int {
        if head == nil {
            return 0
        }
        carry := foo(head.Next)
        head.Val += carry
        carry = head.Val / 10
        head.Val %= 10
        return carry
    }

    p1, p2 := l1, l2
    for p1 != nil && p2 != nil {
        p1 = p1.Next
        p2 = p2.Next
    }
    if (p1 == nil) {
        swap(&l1, &l2)
        swap(&p1, &p2)
    }
    p2 = l1
    for p1 != nil {
        p1 = p1.Next
        p2 = p2.Next
    }
    for p2 != nil {
        p2.Val += l2.Val
        l2 = l2.Next
        p2 = p2.Next
    }
    carry := foo(l1)
    if carry > 0 {
        l1 = &ListNode{carry, l1}
    }
    return l1
}
```



## [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/) [Tree]



## [452. Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/) [Greedy]



## [454. 4Sum II](https://leetcode.com/problems/4sum-ii/) [Hash]



## [455. Assign Cookies](https://leetcode.com/problems/assign-cookies/) [Greedy]



## [457. Circular Array Loop](https://leetcode.cn/problems/circular-array-loop/)

#### Go

```go
func circularArrayLoop(nums []int) bool {
    n := len(nums)
    next := func(curr int) int {
        return ((curr + nums[curr]) % n + n) % n // 确保 next 在 [0, n) 中
    }
    for i := 0; i < n; i ++ {
        s, f := i, next(i)
        for nums[i] * nums[s] > 0 && nums[i] * nums[f] > 0 && nums[i] * nums[next(f)] > 0 { // 确定方向正确, 即 s 的方向, f 的方向, next(f) 的方向, 要与 i 的方向相同
            if s == f {
                if s != next(s) { // 排除 s -> s 的情况
                    return true
                } else {
                    break
                }
            }
            s = next(s)
            f = next(next(f))
        }
    }
    return false
}
```



#### C++

```c++
class Solution {
    vector<int> nums;
    int nxt(int cur) {
        int t = cur + nums[cur];
        return t < 0 ? (t + nums.size()) % nums.size() : t % nums.size(); // nums.size() 是 t_size (无符号整数), 会导致 -1 % 3 = 0
    }
    /*
    int n;
    int nxt(int cur) {
        return ((cur + nums[cur]) % n + n) % n; // 相当于把 nums.size() 转成 int
    }
    */
public:
    bool circularArrayLoop(vector<int>& nums) {
        this->nums = nums;
        for (int i = 0; i < nums.size(); i ++) {
            int s = i, f = nxt(i);
            while (
                nums[i] * nums[s] > 0 && 
                nums[i] * nums[f] > 0 && 
                nums[i] * nums[nxt(f)] > 0
            ) {
                if (s == f) {
                    if (s != nxt(s)) return true;
                    else break;
                }
                s = nxt(s);
                f = nxt(nxt(f));
            }
        }
        return false;
    }
};
```



## [459. Repeated Substring Pattern](https://leetcode.com/problems/repeated-substring-pattern/) [String]



## [460. LFU Cache](https://leetcode.cn/problems/lfu-cache/)

#### Java

*Balanced Tree*

使用 TreeSet 维护有序节点 (自定义比较器)

注意, 不能使用 PriorityQueue, 因为队列无法对队列内部 (不是队首) 的元素进行高效率修改!!!!!

TreeSet 可以维护所有数据的有序性, 同时高效查取; 而 PriorityQueue只能确保队首元素最小

这种方法的时间复杂度为$\log N$

```java
class LFUCache {
    private static class Node implements Comparable<Node> {
        int cnt, time, key, val;
        public Node(int c, int t, int k, int v) { cnt = c; time = t; key = k; val = v; }
        public int compareTo(Node node) { return cnt == node.cnt ? time - node.time : cnt - node.cnt; }
    }
    private int cap, time = 0;
    private Map<Integer, Node> map = new HashMap<>();
    private TreeSet<Node> set = new TreeSet<>();
    private Node getNode(int key) {
        time ++; // 合法操作, time ++
        Node node = map.get(key);
        set.remove(node); // 移出再移入, 是为了能把修改过 cnt, time 的 node 放在 set 正确的位置上
        node.cnt ++;
        node.time = time;
        set.add(node);
        return node;
    }

    public LFUCache(int capacity) { cap = capacity; }
    
    public int get(int key) {
        if (!map.containsKey(key)) return -1;
        return getNode(key).val;
    }
    
    public void put(int key, int value) {
        if (!map.containsKey(key)) {
            if (map.size() == cap) {
                Node rmv = set.first();
                set.remove(rmv);
                map.remove(rmv.key);
            }
            time ++;
            Node node = new Node(1, time, key, value);
            map.put(key, node);
            set.add(node);
        } else getNode(key).val = value;
    }
}
```



*链表组*

```java
class LFUCache {
    private static class Node {
        int freq, key, val;
        Node prev, next;
        public Node(int k, int v) { freq = 1; key = k; val = v; }
    }

    private int cap, minfreq = 1;
    private final Map<Integer, Node> keyNodeMap = new HashMap<>(), freqHeadMap = new HashMap<>();

    private Node newHead() {
        Node head = new Node(-1, -1);
        head.next = head;
        head.prev = head;
        return head;
    }

    private void insertNode(Node head, Node node) { // 永远是头插
        Node next = head.next;
        head.next = node;
        next.prev = node;
        node.next = next;
        node.prev = head;
    }

    private void removeNode(Node head, Node node) {
        Node prev = node.prev;
        Node next = node.next;
        prev.next = next;
        next.prev = prev;
        if (head == head.next) {
            freqHeadMap.remove(node.freq);
            if (node.freq == minfreq) minfreq ++; // 只有在当前链表为空时, 才可以移除该链表
        }
    }

    private Node getNode(int key) {
        Node node = keyNodeMap.get(key);
        Node head = freqHeadMap.get(node.freq);
        removeNode(head, node);
        node.freq ++;
        if (!freqHeadMap.containsKey(node.freq)) freqHeadMap.put(node.freq, newHead());
        head = freqHeadMap.get(node.freq);
        insertNode(head, node);
        return node;
    }
    
    public LFUCache(int capacity) {
        cap = capacity;
        minfreq = 1;
    }
    
    public int get(int key) {
        if (!keyNodeMap.containsKey(key)) return -1;
        return getNode(key).val;
    }
    
    public void put(int key, int value) {
        if (!keyNodeMap.containsKey(key)) { // add
            if (keyNodeMap.size() == cap) { // 删除频率最低 使用最久远的key
                Node head = freqHeadMap.get(minfreq);
                Node node = head.prev;
                removeNode(head, node);
                keyNodeMap.remove(node.key);
            }
            minfreq = 1;
            Node node = new Node(key, value);
            keyNodeMap.put(key, node);
            if (!freqHeadMap.containsKey(node.freq)) freqHeadMap.put(node.freq, newHead());
            Node head = freqHeadMap.get(node.freq);
            insertNode(head, node);
        } else getNode(key).val = value; // update
    }
}
```



#### C++

```c++
class LFUCache {
    struct Node {
        int freq, key, val;
        Node *next, *prev;
        Node(int k, int v) : freq(1), key(k), val(v) {}
    };
    int cap, minfreq = 1;
    unordered_map<int, Node*> keyNodeMap, freqHeadMap;

    Node* newHead() {
        Node* head = new Node(-1, -1);
        head->next = head; head->prev = head;
        return head;
    }

    void insertNode(Node* head, Node* node) {
        Node* next = head->next;
        head->next = node;
        next->prev = node;
        node->next = next;
        node->prev = head;
    }

    void removeNode(Node* head, Node* node) {
        Node* next = node->next;
        Node* prev = node->prev;
        prev->next = next; next->prev = prev;

        if (head->next == head) { // 当前频率对应的列表为空
            freqHeadMap.erase(node->freq); // 这里是否移除取决于空间是否吃紧, 以及增加过多无用freq后map的性能下降
            delete head; // 删除 head 确保内存安全
            if (minfreq == node->freq) minfreq ++;
        }
    }

    Node* getNode(int key) {
        Node* node = keyNodeMap[key];
        Node* head = freqHeadMap[node->freq];
        removeNode(head, node);

        node->freq ++;
        if (freqHeadMap.find(node->freq) == freqHeadMap.end()) freqHeadMap[node->freq] = newHead();
        head = freqHeadMap[node->freq];
        insertNode(head, node);
        return node;
    }

public:
    LFUCache(int capacity) { cap = capacity; }
    ~LFUCache() { // 只有 freqHeadMap 需要额外删除, 如果删除了 keyNodeMap 中的 node 会导致内存重复释放
        for (auto it : freqHeadMap) {
            Node* head = it.second;
            Node* p = head->next;
            while (p != head) {
                Node* next = p->next;
                delete p;
                p = next;
            }
            delete head;
        }
    }
    
    int get(int key) {
        if (keyNodeMap.find(key) == keyNodeMap.end()) return -1;
        return getNode(key)->val;
    }
    
    void put(int key, int value) {
        if (keyNodeMap.find(key) == keyNodeMap.end()) { // add
            if (keyNodeMap.size() == cap) { // delete
                Node* head = freqHeadMap[minfreq];
                Node* node = head->prev; // find the node will be deleted
                removeNode(head, node);
                keyNodeMap.erase(node->key);
                delete node;
            }
            minfreq = 1;
            Node* node = new Node(key, value);
            if (freqHeadMap.find(node->freq) == freqHeadMap.end()) freqHeadMap[node->freq] = newHead();
            Node* head = freqHeadMap[node->freq];
            insertNode(head, node);
            keyNodeMap[key] = node;
        } else getNode(key)->val = value; // update
    }
};
```



内存管理错误版本:

```c++
class LFUCache {
    struct Node {
        int freq, key, val;
        Node *next, *prev;
        Node(int k, int v) : freq(1), key(k), val(v) {}
    };
    int cap, minfreq = 1;
    unordered_map<int, Node*> keyNodeMap;
    unordered_map<int, Node> freqHeadMap; // 这样写风险很大

    Node newHead() {
        Node head = Node(-1, -1);
        head.next = &head; head.prev = &head;
        /*我认为这里是报错的原因, head.next和prev指向了栈上, 而这段代码实际上是希望head的这两个指针, 在head被存入map后(head在堆上), 指向堆上, 因此这里造成了问题, 你觉得我的分析有道理吗? 
        - 这段代码中的问题确实出在 newHead() 函数中，你尝试返回一个栈上的对象 head（局部变量），然后把它拷贝到 unordered_map<int, Node> 中（作为值类型存储），但在设置 head.next = &head 时，你让一个值类型的对象自指向了栈上临时变量的地址 —— 这就留下了一个悬垂指针（dangling pointer）。
        */
        return head; 
    }

    void insertNode(Node* head, Node* node) {
        Node* next = head->next;
        head->next = node;
        next->prev = node;
        node->next = next;
        node->prev = head;
    }

    void removeNode(Node* head, Node* node) {
        Node* next = node->next;
        Node* prev = node->prev;
        prev->next = next; next->prev = prev;

        if (head->next == head) {
            freqHeadMap.erase(node->freq);
            if (minfreq == node->freq) minfreq ++;
        }
    }

    Node* getNode(int key) {
        Node* node = keyNodeMap[key];
        Node* head = &freqHeadMap[node->freq]; // 报错出现在这里 
        removeNode(head, node);

        node->freq ++;
        if (freqHeadMap.find(node->freq) == freqHeadMap.end()) freqHeadMap[node->freq] = newHead();
        head = &freqHeadMap[node->freq];
        insertNode(head, node);
        return node;
    }

public:
    LFUCache(int capacity) { cap = capacity; }
    ~LFUCache() {
        for (auto it : freqHeadMap) {
            Node* head = &it.second;
            Node* p = head->next;
            while (p != head) {
                Node* next = p->next;
                delete p;
                p = next;
            }
        }
    }
    
    int get(int key) {
        if (keyNodeMap.find(key) == keyNodeMap.end()) return -1;
        return getNode(key)->val;
    }
    
    void put(int key, int value) {
        if (keyNodeMap.find(key) == keyNodeMap.end()) {
            if (keyNodeMap.size() == cap) {
                Node* head = &freqHeadMap[minfreq];
                Node* node = head->prev;
                removeNode(head, node);
                keyNodeMap.erase(node->key);
                delete node;
            }
            minfreq = 1;
            Node* node = new Node(key, value);
            if (freqHeadMap.find(node->freq) == freqHeadMap.end()) freqHeadMap[node->freq] = newHead();
            Node* head = &freqHeadMap[node->freq];
            insertNode(head, node);
            keyNodeMap[key] = node;
        } else getNode(key)->val = value;
    }
};
```



## [461. Hamming Distance](https://leetcode.com/problems/hamming-distance/)

```java
class Solution {
    public int hammingDistance(int x, int y) {
        int xor  = x ^ y;
        int cnt = 0;
        for (int i = 0; i < 32; i ++) {
            cnt += ((xor >>> i) & 1);
        }
        return cnt;
    }
}
```



## [463. Island Perimeter](https://leetcode.com/problems/island-perimeter/) [Simulation]



## [474. Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/) [DP-Knapsack]



## [491. Non-decreasing Subsequences](https://leetcode.com/problems/non-decreasing-subsequences/) [Backtrack]



## [494. Target Sum](https://leetcode.com/problems/target-sum/) [DP-Knapsack]



## [496. Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/) [Mono Stack]