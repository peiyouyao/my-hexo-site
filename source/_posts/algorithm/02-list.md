---
title: List Node
date: 2024-04-12 00:00:00
tags: key-algorithm
categories: algorithm
---

---

# Definition of List Node

## Java

```java
// Definition for singly-linked list.
public class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
}
```

## C++

```c++
// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};
```

## Rust

```rust
// Definition for singly-linked list.
#[derive(PartialEq, Eq, Clone, Debug)]
pub struct ListNode {
  pub val: i32,
  pub next: Option<Box<ListNode>>
}

impl ListNode {
  #[inline]
  fn new(val: i32) -> Self {
    ListNode {
      next: None,
      val
    }
  }
}
```

### 基本 CRUD

```rust
impl Solution {
    pub fn vector_to_list(nums: Vec<i32>) -> Option<Box<ListNode>> {
        let mut dummy: Box<ListNode> = Box::new(ListNode::new(0));
        let mut p: &mut Box<ListNode> = &mut dummy;
        for val in nums {
            p.next = Some(Box::new(ListNode::new(val)));
            p = p.next.as_mut().unwrap();
        }
        dummy.next
    }


    pub fn list_to_vector(
        mut head: Option<Box<ListNode>>
    ) -> Vec<i32> {
        let mut ans: Vec<i32> = Vec::new();
        while let Some(node) = head {
            // node: Box<ListNode>
            ans.push(node.val);
            head = node.next; // 根据签名, 允许把 head 消耗掉
        }
        ans
    }


    pub fn list_read(
        mut head: &Option<Box<ListNode>>
    ) -> Vec<i32> {
        let mut ans: Vec<i32> = Vec::new();
        while let Some(node) = head {
            // node: &Box<ListNode>
            ans.push(node.val);
            head = &node.next;
        }
        ans
    }


    pub fn list_add_at(
        head: Option<Box<ListNode>>, idx: i32, val: i32
    ) -> Option<Box<ListNode>> {
        let mut dummy: Option<Box<ListNode>> = Some(
            Box::new(ListNode { val: 0, next: head }));
        let mut p: &mut Option<Box<ListNode>> = &mut dummy;
        for _ in 0..idx {
            if let Some(node) = p {
                // node: &mut Box<ListNode>
                p = &mut node.next;
            } else {
                return dummy.unwrap().next;
            }
        }
        let rest: Option<Box<ListNode>> = p.as_mut().unwrap().next.take();
        p.as_mut().unwrap().next = Some(Box::new(ListNode { val, next: rest }));
        dummy.unwrap().next
    }


    pub fn list_remove_at(
        head: Option<Box<ListNode>>, idx: i32
    ) -> Option<Box<ListNode>> {
        let mut dummy: Box<ListNode> = Box::new(ListNode { val: 0, next: head });
        let mut p: &mut ListNode = dummy.as_mut();
        for _ in 0..idx {
            if let Some(nxt) = &mut p.next {
                // nxt: &mut Box<ListNode>
                p = nxt;
            } else {
                return dummy.next;
            }
        }
        let rest: Option<Box<ListNode>> = p.next.as_mut().unwrap().next.take();
        p.next = rest;
        dummy.next
    }


    pub fn list_remove_at1(
        head: Option<Box<ListNode>>, idx: i32
    ) -> Option<Box<ListNode>> {
        let mut dummy: Box<ListNode> = Box::new(ListNode { val: 0, next: head });
        let mut p: &mut Box<ListNode> = &mut dummy;
        for _ in 0..idx {
            if let Some(nxt) = &mut p.next {
                // nxt: &mut Box<ListNode>
                p = nxt;
            } else {
                return dummy.next;
            }
        }
        let rest: Option<Box<ListNode>> = p.next.as_mut().unwrap().next.take();
        p.next = rest;
        dummy.next
    }
}
```

### 测试代码

```rust
mod solution;
use solution::Solution;
fn main() {
    println!("Hello, world!");
    // Test cases
    let test_cases = vec![
        Solution::vector_to_list(vec![]),              // 空链表
        Solution::vector_to_list(vec![1]),             // 单节点链表
        Solution::vector_to_list(vec![1, 2, 3]),       // 三节点链表
        Solution::vector_to_list(vec![1, 2, 3, 4, 5]), // 五节点链表
    ];


    // Test list_to_vector
    println!("Testing list_to_vector:");
    for (i, head) in test_cases.clone().into_iter().enumerate() {
        let result = Solution::list_to_vector(head);
        println!("Case {}: {:?}", i, result);
    }


    // Test list_read
    println!("\nTesting list_read:");
    for (i, head) in test_cases.iter().enumerate() {
        let result = Solution::list_read(head);
        println!("Case {}: {:?}", i, result);
    }


    // Test list_add_at
    println!("\nTesting list_add_at:");
    let add_cases = vec![
        (Solution::vector_to_list(vec![1, 2, 3]), 0, 0), // 在头部插入
        (Solution::vector_to_list(vec![1, 2, 3]), 2, 99), // 在中间插入
        (Solution::vector_to_list(vec![1, 2, 3]), 3, 100), // 在尾部插入
        (Solution::vector_to_list(vec![1, 2, 3]), 5, 101), // idx 越界
    ];
    for (i, (head, idx, val)) in add_cases.into_iter().enumerate() {
        let result = Solution::list_to_vector(Solution::list_add_at(head, idx, val));
        println!("Case {}: {:?}", i, result);
    }


    // Test list_remove_at
    println!("\nTesting list_remove_at:");
    let remove_cases = vec![
        Solution::vector_to_list(vec![1, 2, 3]), // 删除头部
        Solution::vector_to_list(vec![1, 2, 3]), // 删除中间
        Solution::vector_to_list(vec![1, 2, 3]), // 删除尾部
        Solution::vector_to_list(vec![1, 2, 3]), // idx 越界
    ];
    let remove_idx = vec![0, 1, 2, 5];
    for (i, (head, idx)) in remove_cases.into_iter().zip(remove_idx).enumerate() {
        let result = Solution::list_to_vector(Solution::list_remove_at(head, idx));
        println!("Case {}: {:?}", i, result);
    }
}
```

## Go

```go
// Definition for singly-linked list.
type ListNode struct {
    Val int
    Next *ListNode
}
```

## Kotlin

```kotlin
/**
 * Example:
 * var li = ListNode(5)
 * var v = li.`val`
 * Definition for singly-linked list.
 */
class ListNode(var `val`: Int) {
    var next: ListNode? = null
}
```

## Python

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

# Examples

## [203. Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/)

#### Java

*Iteration*

```java
// 虚拟头节点
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(0, head);
        ListNode p = dummy;
        while (p != null && p.next != null) {
            if (p.next.val == val) p.next = p.next.next;
            else p = p.next;
        }
        return dummy.next;
    }
}
```

*Recursion*

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if (head == null) return head;
        ListNode next = head.next;
        if (head.val == val) return removeElements(next, val);
        else {
            head.next = removeElements(next, val);
            return head;
        }
    }
}
```

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if (head == null) return head;
        while (head != null && head.val == val) head = head.next;
        if (head == null) return head;
        head.next = removeElements(head.next, val);
        return head;
    }
}
```

#### C++

```c++
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if (head == nullptr) return head;
        if (head->val == val) {
            ListNode* nxt = head->next;
            delete head;
            return removeElements(nxt, val);
        } else {
            head->next = removeElements(head->next, val);
            return head;
        }
    }
};
```

#### Rust

*Recursion*

```rust
impl Solution {
    pub fn remove_elements(mut head: Option<Box<ListNode>>, val: i32) -> Option<Box<ListNode>> {
        if let Some(mut head) = head {
            if head.val == val {
                return Self::remove_elements(head.next, val);
            } else {
                let next = head.next;
                head.next = Self::remove_elements(next, val);
                return Some(head);
            }
        } else {
            None
        }
    }
}
```

*Iteration*

```
一个ListNode 结构体存储了两个数据, 一个是 val 值, 另一个是一个 "链接" (Option<Box<ListNode>>)
在取得链接时, 因为这个链接不是Copy的, 所以直接使用 Some(nxt) = cur.next, 就会造成 cur 的部分所有权丢失, 以至于之后的代码就不能使用 cur.next 了, 因此要使用 .take() 把 cur 的链接置空, 再把其内部数据转移到 nxt 上
```

```rust
impl Solution {
    pub fn remove_elements(head: Option<Box<ListNode>>, val: i32) -> Option<Box<ListNode>> {
        let mut dummy: Box<ListNode> = Box::new(ListNode::new(0));
        dummy.next = head;
        let mut cur: &mut ListNode = dummy.as_mut();
        while let Some(nxt) = cur.next.take() { // nxt: Box<ListNode>
            if nxt.val == val {
                cur.next = nxt.next;
            } else {
                cur.next = Some(nxt);
                cur = cur.next.as_mut().unwrap();
            }
        }
        dummy.next
    }
}
```

```rust
impl Solution {
    pub fn remove_elements(head: Option<Box<ListNode>>, val: i32) -> Option<Box<ListNode>> {
        let mut dummy: Box<ListNode> = Box::new(ListNode { val: 0, next: head });
        let mut cur: &mut Box<ListNode> = &mut dummy;
        while let Some(nxt) = cur.next.take() { // cur.next 被置空了, 里面数据的所有权转移给了 nxt: Box<ListNode>
            if nxt.val == val {
                cur.next = nxt.next;
            } else {
                cur.next = Some(nxt); // 把被nxt夺走的数据拿回来
                cur = cur.next.as_mut().unwrap();
            }
        }
        dummy.next
    }
}
```

#### Python

```python
class Solution:
    def removeElements(self, head: Optional[ListNode], val: int) -> Optional[ListNode]:
        dummy_head = ListNode(next = head)
        cur = dummy_head

        while cur and cur.next:
            if cur.next.val == val:
                cur.next = cur.next.next
            else:
                cur = cur.next
        return dummy_head.next
```

## [707. Design Linked List](https://leetcode.com/problems/design-linked-list/)

涉及删除, 要搞 dummy node

#### Java

```java
// 单链表实现
class ListNode {
    int val;
    ListNode next;
    ListNode(){}
    ListNode(int val) {
        this.val=val;
    }
}

class MyLinkedList {
    private int size;
    private ListNode dummy;

    public MyLinkedList() {
        this.size = 0;
        this.dummy = new ListNode(0);
    }

    public int get(int index) {
        if (index >= this.size) return -1;
        ListNode p = this.dummy;
        for (int i = 0; i < index; i ++) p = p.next;
        return p.next.val;
    }

    public void addAtHead(int val) {
        ListNode tmp = this.dummy.next;
        ListNode addNode = new ListNode(val);
        addNode.next = tmp;
        this.dummy.next = addNode;
        size ++;
    }

    public void addAtTail(int val) {
        ListNode p = this.dummy;
        while (p.next != null) p = p.next;
        ListNode addNode = new ListNode(val);
        p.next = addNode;
        size ++;
    }

    public void addAtIndex(int index, int val) {
        if (index > size) return;
        ListNode p = this.dummy;
        for (int i = 0; i < index; i ++) {
            p = p.next;
        }
        ListNode tmp = p.next;
        ListNode addNode = new ListNode(val);
        addNode.next = tmp;
        p.next = addNode;
        size ++;
    }

    public void deleteAtIndex(int index) {
        if (index >= this.size) return;
        ListNode p = this.dummy;
        for (int i = 0; i < index; i ++) p = p.next;
        p.next = p.next.next;
        this.size --;
    }
}
```

```java
// 双链表实现
class ListNode {
    public int val;
    public ListNode prev;
    public ListNode next;

    public ListNode(int $val) {
        val = $val;
    }
}

class MyLinkedList {
    int size;
    ListNode head;  // dummy head
    ListNode tail;  // dummy tail

    public MyLinkedList() {
        head = new ListNode(0);
        tail = new ListNode(0);
        head.next = tail;
        tail.prev = head;
        size = 0;
    }

    public int get(int index) {
        if ( index >= size ) return -1;
        if (index >= size / 2) { // start from tail
            ListNode p = this.tail;
            for (int i = 0; i < size - index; i ++) p = p.prev;
            return p.val;
        } else {
            ListNode p = this.head;
            for (int i = 0; i < index; i ++) p = p.next;
            return p.next.val;
        }
    }

    public void addAtHead(int val) {
        this.addAtIndex(0, val);
    }

    public void addAtTail(int val) {
        this.addAtIndex(this.size, val);
    }

    public void addAtIndex(int index, int val) {
        if (index > size) return;
        ListNode p = this.head;
        for (int i = 0; i < index; i ++) p = p.next;
        ListNode tmp = p.next;
        ListNode addNode = new ListNode(val);
        addNode.next = tmp;
        tmp.prev = addNode;
        p.next = addNode;
        addNode.prev = p;
        size ++;
    }

    public void deleteAtIndex(int index) {
        if (index >= this.size) return;
        ListNode p = this.head;
        for (int i = 0; i < index; i ++) p = p.next;
        ListNode tmp = p.next.next;
        p.next = tmp;
        tmp.prev = p;
        size --;
    }
}
```

#### C++

```c++
class MyLinkedList {
private:
    struct Node {
        int val;
        Node* next;
        Node* prev;

        Node(int val) : val(val), next(nullptr), prev(nullptr) {};
    };

    Node* head;
    Node* tail;
    int size = 0;

public:
    MyLinkedList() {
        head = new Node(0); // 为什么这里一定要在堆上分配, 为什么不可以 &Node(0), `Node(0)` 是临时对象，生命周期结束后会被销毁, head 在 构造函数结束后就又成立nullptr
        tail = new Node(0);

        head->next = tail;
        tail->next = head;

        size = 0;
    }

    ~MyLinkedList() {
        Node* curr = head->next;  // 从第一个数据节点开始
        while (curr != tail) {   // 直到遍历到尾节点
            Node* next = curr->next;
            delete curr;  // 删除当前节点
            curr = next;
        }
        delete head;  // 删除头节点
        delete tail;  // 删除尾节点
    }

    /* 错误的析构函数
    ~MyLinkedList() {
        Node* curr = head;
        while (curr != nullptr) {
            Node* next = curr->next;
            delete curr;
            curr = next;
        }
    }
    */

    int get(int index) {
        if (index < 0 || index > size - 1) return -1;
        if (index <= size / 2) {
            Node* p = head;
            for (int i = 0; i < index; i ++) p = p->next;
            return p->next->val;
        } else {
            int t = size - 1 - index;
            Node* p = tail;
            for (int i = 0; i < t; i ++) p = p->prev;
            return p->prev->val;
        }
    }

    void addAtHead(int val) {
        addAtIndex(0, val);
    }

    void addAtTail(int val) {
        addAtIndex(size, val);
    }

    void addAtIndex(int index, int val) {
        if (index < 0 || index > size) return;

        if (index <= size / 2) {
            Node* p = head;
            for (int i = 0; i < index; i ++) p = p->next;
            Node* reserve = p->next;
            Node* newNode = new Node(val);
            p->next = newNode;
            reserve->prev = newNode;
            newNode->next = reserve;
            newNode->prev = p;
        } else {
            Node* p = tail;
            for (int i = 0; i < size-index; i ++) p = p->prev;
            Node* reserve = p->prev;
            Node* newNode = new Node(val);
            p->prev = newNode;
            reserve->next = newNode;
            newNode->next = p;
            newNode->prev = reserve;
        }
        size ++;   
    }

    void deleteAtIndex(int index) {
        if (index < 0 || index > size - 1) return;
        Node *p = head;
        for (int i = 0; i < index; i ++) p = p->next;
        Node* reserve = p->next->next;
        Node* del = p->next;
        p->next = reserve;
        reserve->prev = p;
        delete del;
        size --;
    }
};
```

#### Python

```python
class MyLinkedList:

    def __init__(self):
        self.dummy = ListNode()
        self.size = 0

    def get(self, index: int) -> int:
        if index >= self.size or index < 0:
            return -1
        cur = self.dummy.next
        for i in range(index):
            cur = cur.next

        return cur.val


    def addAtHead(self, val: int) -> None:
        self.dummy.next = ListNode(val, next = self.dummy.next)
        self.size += 1


    def addAtTail(self, val: int) -> None:
        cur = self.dummy
        while cur.next:
            cur = cur.next
        cur.next = ListNode(val)
        self.size += 1


    def addAtIndex(self, index: int, val: int) -> None:
        if index > self.size or index < 0:
            return

        cur = self.dummy
        for i in range(index):
            cur = cur.next
        tmp = cur.next
        cur.next = ListNode(val, tmp)
        self.size += 1


    def deleteAtIndex(self, index: int) -> None:
        if index >= self.size or index <0:
            return

        cur = self.dummy
        for _ in range(index):
            cur = cur.next
        cur.next = cur.next.next
        self.size -= 1
```

## [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)

#### Java

*Iteration*

```java
// 创建一个新链表来接住结果
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode res = null;
        ListNode p = head;
        while (p != null) {
            ListNode tmp = p.next;
            p.next = res;
            res = p;
            p = tmp;
        }
        return res;
    }
}
```

*Recursion*

```java
// 递归
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode reversedNode = reverseList(head.next);  // 返回head.next翻转完成的结果, 把head放到它的末尾即可
        ListNode p = reversedNode;
        while (p != null && p.next != null) p = p.next;
        p.next = head;
        head.next = null;
        return reversedNode;
    }
}
```

```java
// 递归简写
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode rest = this.reverseList(head.next);  
        head.next.next = head;  // head <=> rest[size - 2] <- rest[size - 3] <- ... <- rest[0]
        head.next = null;  // null <- head <- rest[size - 2] <- rest[size - 3] <- ... <- rest[0]
        return rest;
    }
}
// 出现了两个指针指向同一个结点的情况
```

```
f(1 -> 2 -> 3 -> null)
|- f(2 -> 3 -> null)
|  |- f(3 -> null)
|  |  `- return 3 -> null
|  |
|  |- rest = 3 -> null
|  |- 2 <=> 3  // 让 3 不要 -> null 而是 -> 2
|  |- null <- 2 <- 3  // 让 2 不要 -> 3 而是 -> null
|  `- return 3 -> 2 -> null
|
|- rest = 3 -> 2 -> null
|- 1 <=> 2 <- 3  // 让 2 不是 -> null 而是 -> 1
|- null <- 1 <- 2 <- 3  // 让 1 不要 -> null 而是 -> null
`- return 3 -> 2 -> 1 -> null
```

#### C++

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* rest = head->next;
        rest = reverseList(rest);
        head->next->next = head;
        head->next = nullptr;
        return rest;
    }
};
```

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* rest = head->next;
        head->next = nullptr; // 这里其实没必要
        rest = reverseList(rest);
        ListNode* p = rest;
        while (p != nullptr && p->next != nullptr) p = p->next;
        p->next = head;
        p = nullptr;
        return rest;
    }
};
```

#### Rust

*Iteration*

```rust
// 迭代 (better)
impl Solution {
    pub fn reverse_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut cur: Option<Box<ListNode>> = head;
        let mut pre: Option<Box<ListNode>> = None;
        while let Some(mut node) = cur.take() {
            cur = node.next;
            node.next = pre;
            pre = Some(node);
        }
        pre
    }
}
```

```rust
// 迭代
impl Solution {
    pub fn reverse_list(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        if head.is_none() || head.as_ref().unwrap().next.is_none() {
            head
        } else {
            let mut ans: Option<Box<ListNode>> = None;
            while head.is_some() {
                let nxt: Option<Box<ListNode>> = head.as_mut().unwrap().next.take();
                head.as_mut().unwrap().next = ans;
                ans = head;
                head = nxt;
            }
            ans
        }
    }
}
```

*Recursion*

```rust
// 递归; better; 0 ms
impl Solution {
    pub fn reverse_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        fn reverse(
            mut cur: Option<Box<ListNode>>,
            mut pre: Option<Box<ListNode>>,
        ) -> Option<Box<ListNode>> {
            if let Some(mut node) = cur {
                let nxt = node.next;
                node.next = pre;
                pre = Some(node);
                reverse(nxt, pre)
            } else {
                pre
            }
        }
        reverse(head, None)
    }
}
```

```rust
// 递归; worse; 67 ms
impl Solution {
    pub fn reverse_list(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        if head.is_none() || head.as_ref().unwrap().next.is_none() {
            head
        } else {
            let mut rest: Option<Box<ListNode>> =
                Self::reverse_list(head.as_mut().unwrap().next.take()); // mut 体现在 take() 上, head.next 现在已经为 None
            let mut p: &mut Box<ListNode> = rest.as_mut().unwrap();
            while let Some(np) = p.next.take() { // np: Box<List>
                p.next = Some(np); // 这里十分耗费性能, 每次都要把数据take出来
                p = p.next.as_mut().unwrap();
            }
            p.next = head;
            rest
        }
    }
}
```

*Wrong Code*

```rust
// Wrong Code
impl Solution {
    pub fn reverse_list(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        if head.is_none() || head.as_ref().unwrap().next.is_none() {
            head
        } else {
            let mut rest = Self::reverse_list(head.as_mut().unwrap().next.take());
            let mut p: &mut Option<Box<ListNode>> = &mut rest;
            while let Some(np) = p {
                if np.next.is_none() { // 在添加这一段逻辑后, 出现以下报错
                    break;
                }
                p = &mut np.next;
            }
            p.as_mut().unwrap().next = head; // cannot borrow `*p` as mutable more than once at a time second mutable borrow occurs here
            rest
        }
    }
}
```

#### Python

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        pre = None
        cur = head

        while cur:
            tmp = cur.next
            cur.next = pre
            pre = cur
            cur = tmp
        return pre
```

## [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)

#### Java

*Iteration*

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode cur = head;
        while (cur != null && cur.next != null) {
            ListNode rest = cur.next.next;  // 跳两步
            int tmpVal = cur.val; // 不要修改next指向, 而是直接修改值即可
            cur.val = cur.next.val;
            cur.next.val = tmpVal;
            cur = rest;
        }
        return head;
    }
}
```

*Recursion*

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode rest = head.next.next;
        ListNode nxt = head.next; // 直接修改链表结构, 不是简单交换值
        nxt.next = head;
        head.next = null;
        head = nxt;
        head.next.next = swapPairs(rest); // 注意两个 .next
        return head;
    }
}
```

#### C++

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* rest = head->next->next;
        ListNode* nxt = head->next;
        nxt->next = head;
        head->next = swapPairs(rest);
        return nxt; // 把nxt提前
    }
};
```

#### Rust

*Recursion*

```rust
impl Solution {
    pub fn swap_pairs(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        if head.is_none() || head.as_ref().unwrap().next.is_none() {
            head
        } else {
            let mut nxt: Option<Box<ListNode>> = head.as_mut().unwrap().next.take();
            let mut rest: Option<Box<ListNode>> = nxt.as_mut().unwrap().next.take();
            /*                                        ^^^^^^^^
            这里要用 as_mut():
            1) 直接 head / nxt.unwrap() 转移 head / nxt 所有权, 后续的代码就不能再使用 head 了
            2) 有 .take() 操作, 故不能使用 as_ref()
            */
            head.as_mut().unwrap().next = Self::swap_pairs(rest);
            nxt.as_mut().unwrap().next = head;
            nxt
        }
    }
}
```

*Iteration*

```rust
impl Solution {
    pub fn swap_pairs(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        if head.is_none() || head.as_ref().unwrap().next.is_none() {
            head
        } else {
            let mut cur: &mut Option<Box<ListNode>> = &mut head;
            while cur.is_some() && cur.as_ref().unwrap().next.is_some() {
                let mut nxt: Option<Box<ListNode>> = cur.as_mut().unwrap().next.take();
                let tmp: i32 = cur.as_ref().unwrap().val;
                cur.as_mut().unwrap().val = nxt.as_ref().unwrap().val;
                nxt.as_mut().unwrap().val = tmp;
                cur.as_mut().unwrap().next = nxt; // 要把 take() 出来的数据放回去
                cur = &mut cur.as_mut().unwrap().next.as_mut().unwrap().next;
                /*
                cur: &mut Option<Box<ListNode>>
                cur.as_mut(): Option<&mut Box<ListNode>>
                cur.as_mut().unwrap(): &mut Box<ListNode>
                cur.as_mut().unwrap().next: Option<Box<ListNode>>
                cur.as_mut().unwrap().next.as_mut().unwrap().next: Option<Box<ListNode>>
                */
            }
            head
        }
    }
}
/*
Rust 中, 必须把 nxt take() 出来
如果不把nxt take() 出来, 
那么对于整个链表数据, 
有一个 cur 的可变引用, 
同时为了修改它下一个结点的值, 
必须增加一个可变引用, 
这样就违反了借用规则
*/
```

```
rust中, 对于较为复杂的数据, 它的所有权是怎么划分的?
例如以上的链表, 在初始状态下, 是不是所有结点都归 head 所有?
在使用 take() 之后, 是不是短暂的把所有权分割给了 head 和 nxt?
类似 vec 中的 split_as_mut()

ChatGPT 说：
ChatGPT
是的，你的理解非常接近 Rust 的所有权和借用模型！让我们一步步详细分析 Rust 中复杂数据结构（如链表）的所有权划分机制，以及像 take() 这种操作如何影响所有权的分配。

1. 链表的所有权
在链表的初始状态下（以你的代码为例）：

rust
复制代码
let head: Option<Box<ListNode>>;
head 是整个链表的所有者。因为 head 是 Option<Box<ListNode>> 类型，Box<ListNode> 是堆上分配的链表节点。
每个节点（Box<ListNode>）内部包含 val 和一个指向下一个节点的指针 next。
所有权规则：
head 拥有第一个节点。
每个节点的 next 字段（如果存在）拥有下一个节点，依此类推，形成链表。
整个链表的所有权是递归定义的：head 通过链表的结构间接拥有所有节点。
2. take() 操作如何影响所有权
take() 是 Option 类型的方法，用来取出其中的值，同时将 Option 置为 None。在你的代码中：

rust
复制代码
let mut nxt: Option<Box<ListNode>> = cur.as_mut().unwrap().next.take();
这会产生以下变化：

cur.as_mut().unwrap().next 的值被移动（move）到 nxt 中。
cur.as_mut().unwrap().next 被置为 None，表示当前节点不再拥有下一个节点的所有权。
此时，链表的所有权被“短暂分割”：
cur 拥有当前节点。
nxt 拥有原本属于 next 的下一个节点。
所以，在调用 take() 后，链表的结构是暂时“断开”的。此时 head 和 nxt 分别拥有链表的不同部分。

3. 类似 Vec 的所有权分割
Vec 中的 split_at_mut() 或 split_first_mut() 方法也是所有权分割的好例子：

Vec 的行为
rust
复制代码
let mut vec = vec![1, 2, 3, 4];
let (first, rest) = vec.split_first_mut().unwrap();
vec.split_first_mut() 返回了两个可变引用：
first 是对第一个元素的可变引用。
rest 是对剩余切片的可变引用。
Rust 的借用规则允许这种“分割借用”，因为它能够确保：
first 和 rest 是不重叠的。
你不能同时对 first 和 rest 操作同一部分数据。
与链表的对比
在链表中，分割所有权比 Vec 更复杂：

链表没有像 Vec 那样简单的索引操作。
为了分割链表，必须通过 take() 明确地“移动”所有权，将 Option<Box<ListNode>> 的一部分拆分出来。
4. 链表和所有权分割的关键点
链表的初始所有权：

head 是整个链表的所有者。
每个节点通过 Box 的嵌套依次拥有它的后续节点。
分割所有权的方式：

使用 take()，可以将某个节点的所有权移动到另一个变量中。
通过 Box 或 Option 的所有权操作，可以安全地修改链表结构。
分割后的短暂状态：

分割所有权后，你暂时拥有多个不相连的链表片段。
最终需要通过将 nxt 或其他节点重新“链接”回去（如你的代码中的 cur.as_mut().unwrap().next = nxt），重新建立完整的链表。
5. 总结
Rust 的所有权划分基于以下核心原则：

所有权唯一：每个值只能有一个所有者，链表中的每个节点都由其前一个节点的 next 字段拥有。
借用规则：在链表操作中，分割所有权需要使用 take()，以安全地转移节点所有权，避免同时存在多个可变引用。
类似 Vec 的分割：虽然链表和 Vec 的所有权模型不同，但它们都依赖 Rust 的借用检查器来确保分割的安全性。
通过 take() 的分割，链表操作可以做到类似于 Vec 的 split_at_mut()，即短暂分割数据所有权，再通过重新链接来恢复原始结构。
```

```rust
// better code
impl Solution {
    pub fn swap_pairs(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut cur: &mut Option<Box<ListNode>> = &mut head;
        while let Some(node) = cur {
            if let Some(mut next) = node.next.take() {
                // Swap values between current node and next node
                std::mem::swap(&mut node.val, &mut next.val);
                // Reattach the next node back to the list
                node.next = Some(next);
                // Move to the next pair
                cur = &mut node.next.as_mut().unwrap().next;
            } else {
                break; // End of list
            }
        }
        head
    }
}
```

#### Python

```python
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy_head = ListNode(next = head)
        cur = dummy_head

        while cur.next and cur.next.next:
            tmp_1 = cur.next
            tmp_2 = cur.next.next.next

            cur.next = cur.next.next
            cur.next.next = tmp_1
            cur.next.next.next = tmp_2
            cur = cur.next.next
        return dummy_head.next
```

## [19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

#### Java

```java
// 先行 n 步
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0, head);
        ListNode front = dummy.next, back = dummy;
        for (int i = 0; i < n; i ++) {
            if ( front == null ) return dummy.next;
            front = front.next;
        }
        while (front != null) {
            front = front.next;
            back = back.next;
        }
        back.next = back.next.next;
        return dummy.next;
    }
}
```

#### C++

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummy = new ListNode(0, head); // 创建 dummy 节点
        ListNode *p = dummy->next, *q = dummy;
        for (int i = 0; i < n; i++) {
            if (p == nullptr) { // 如果链表长度不足 n
                delete dummy; // 释放 dummy 节点
                return head;  // 返回原链表
            }
            p = p->next;
        }
        while (p != nullptr) {
            p = p->next;
            q = q->next;
        }
        ListNode* del = q->next;
        if (del != nullptr) { // 确保 del 不为 nullptr
            q->next = del->next; // 删除目标节点
            delete del;          // 释放目标节点内存
        }

        ListNode* ans = dummy->next; // 保存头节点
        delete dummy; // 释放 dummy 节点
        return ans;   // 返回结果链表
    }
};
```

#### Rust

难点在于有两个对 head 所拥有数据的借用, 而且其中一个借用还需要变成 &mut, 一个方法是使用 clone()

```rust
// Wrong Code
#[allow(mutable_transmutes)]
use std::mem;

impl Solution {
    pub fn remove_nth_from_end(head: Option<Box<ListNode>>, n: i32) -> Option<Box<ListNode>> {
        use std::cell::*;

        let mut dummy = Some(Box::new(
            ListNode {
                val: 0,
                next: head,
            }
        ));

        let mut slow = &dummy;
        let mut fast = &dummy;

        for _ in 0..n {
            if let Some(nxt) = &fast.as_ref() {
                fast = &nxt.next;
            } else {
                return dummy.unwrap().next;
            }
        }

        while fast.as_ref().unwrap().next.is_some() {
            fast = &fast.as_ref().unwrap().next;
            slow = &slow.as_ref().unwrap().next;
        }

        fast = &None;
        let slow: &mut Option<Box<ListNode>> = unsafe {
            mem::transmute(slow)
        };

        slow.as_mut().unwrap().next = slow.as_mut().unwrap().next.as_mut().unwrap().next.take();
        dummy.unwrap().next
    }
}
```

非常巧妙的方法, 成功避免了两个借用的尴尬

```rust
impl Solution {
    pub fn remove_nth_from_end(head: Option<Box<ListNode>>, n: i32) -> Option<Box<ListNode>> {
        let mut dummy = Some(Box::new(ListNode {
            val: 0,
            next: head,
        }));
        let mut sz = 0;
        {   
            /*
            let mut p = dummy.as_ref();
            while let Some(node) = p {
                sz += 1;
                p = node.next.as_ref();
            }
            */
            let mut p = &dummy;
            while let Some(node) = p.as_ref() {
                sz += 1;
                p = &node.next;
            }
        } // 当这段代码块结束, p 已经被销毁
        let mut p = &mut dummy;
        for _ in 0..(sz - 1 - n) {
            p = &mut p.as_mut().unwrap().next;
        }
        let nxt = p.as_mut().unwrap().next.as_mut().unwrap().next.take();
        p.as_mut().unwrap().next = nxt;
        dummy.unwrap().next
    }
}
```

#### Python

```python
class Solution:
    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        dummy_head = ListNode(next=head)
        fast = slow = dummy_head
        for _ in range(n + 1):
            fast = fast.next
        while fast:
            slow = slow.next
            fast = fast.next
        slow.next = slow.next.next
        return dummy_head.next
```

## [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

#### Java

*Hash*

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        Set<ListNode> rec = new HashSet<>();
        while (headA != null) {
            rec.add(headA);
            headA = headA.next;
        }

        while (headB != null) {
            if (rec.contains(headB)) return headB;
            headB = headB.next;
        }
        return null;
    }
}
```

```java
public class Solution {
    private int size(ListNode head) {
        int size = 0;
        while (head != null) {
            size ++;
            head = head.next;
        }
        return size;
    }

    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        int sizeA = size(headA), sizeB = size(headB);
        if (sizeA >= sizeB) for(int i = 0; i < sizeA - sizeB; i ++) headA = headA.next;
        else for (int i = 0; i < sizeB - sizeA; i ++) headB = headB.next;
        while (headA != null && headA != headB) {
            headA = headA.next;
            headB = headB.next;
        }
        return headA;
    }
}
```

#### C++

该题要求保持链表原始结构, 不要 delete

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        int szA = size(headA);
        int szB = size(headB);
        if (szA > szB) {
            for (int i = 0; i < szA - szB; i ++) {
                if (headA == nullptr) return nullptr;
                ListNode* nxt = headA->next;
                headA = nxt;
            }
        } else {
            for (int i = 0; i < szB - szA; i ++) {
                if (headB == nullptr) return nullptr;
                ListNode* nxt = headB->next;
                headB = nxt;
            }
        }
        while (headA != nullptr && headB != nullptr && headA != headB) {
            ListNode* nxtA = headA->next;
            ListNode* nxtB = headB->next;
            headA = nxtA;
            headB = nxtB;
        }
        return headA;
    }
private:
    int size(ListNode* head) {
        int sz = 0;
        while (head != nullptr) {
            sz ++;
            head = head->next;
        }
        return sz;
    }
};
```

#### Rust

本题用 Rust 搞不了, 要把链表定义换成 Rc 指针

#### Python

```python
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        dummy_headA = ListNode(next=headA)
        dummy_headB = ListNode(next=headB)
        curA, curB = dummy_headA, dummy_headB

        lenA = lenB = 0
        while curA.next:
            curA = curA.next
            lenA += 1
        while curB.next:
            curB = curB.next
            lenB += 1

        curA, curB = dummy_headA, dummy_headB

        if lenA < lenB:
            curA, curB = curB, curA # 让headA更长
            lenA, lenB = lenB, lenA
        for _ in range(lenA - lenB):
            curA = curA.next

        while curA:
            if curA == curB:
                return curA
            else:
                curA = curA.next
                curB = curB.next
        return None
```

## [142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)

1. take care of the while condition
   `while (slow != null && fast != null && fast.next != null)`
2. when `fast` and `slow` meet, let `start` remove from `head`, then the node where `start` and `slow` meet would be the answer

#### Java

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head, fast = head;
        while (slow != null && fast != null && fast.next != null) {
            slow = slow.next; fast = fast.next.next;
            if ( slow == fast ) {
                ListNode start = head;
                while ( start != slow ) {
                    start = start.next; slow = slow.next;
                }
                return start;
            }
        }
        return null;
    }
}
```

#### C++

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *fast = head, *slow = head;
        while (fast != NULL && fast->next != NULL) {
            fast = fast->next->next;
            slow = slow->next;
            if (fast == slow) {
                ListNode* p = head;
                while (p != slow) {
                    p = p->next;
                    slow = slow->next;
                }
                return p;
            }
        }
        return NULL;
    }
};
```

#### Rust

搞不了, 要把链表定义成 Rc, 甚至要引入弱引用 ^_^

#### Go

```go
func detectCycle(head *ListNode) *ListNode {
    var slow, fast *ListNode = head, head
    for slow != nil && fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            var p *ListNode = head
            for p != slow {
                p = p.Next
                slow = slow.Next
            }
            return p
        }
    }
    return nil
}
```

#### Python

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        fast = slow = head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
            if fast == slow: break
        else: return None

        ind_in_line = head
        ind_in_circle = fast
        while ind_in_line != ind_in_circle:
            ind_in_line = ind_in_line.next
            ind_in_circle = ind_in_circle.next
        return ind_in_line
```

# Extra

## [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)

#### Java

本题有要求不能简单交换 node.val

```java
class Solution {
    private ListNode reverse(ListNode head) {
        ListNode ans = null;
        while (head != null) {
            ListNode tmp = head.next;
            head.next = ans;
            ans = head;
            head = tmp;
        }
        return ans;
    }

    public ListNode reverseKGroup(ListNode head, int k) {
        if (k == 1 || head == null || head.next == null) return head;
        ListNode p = head;
        for (int i = 0; i < k - 1; i ++) {
            p = p.next;
            if (p == null) return head;  // 不足k项, 直接return head
        }

        ListNode rest = p.next;
        p.next = null;
        head = reverse(head);  // 翻转前k项
        p = head;
        for (int i = 0; i < k - 1; i ++) p = p.next;
        p.next = reverseKGroup(rest, k);
        return head;
    }
}
```

#### C++

```c++
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        ListNode *p = dummy;
        for (int i = 0; i < k; i ++) {
            p = p->next;
            if (p == nullptr) return head;
        }
        ListNode* rest = p->next;
        p->next = nullptr;
        dummy->next = reverseList(dummy->next);
        p = dummy;
        for (int i = 0; i < k; i ++) {
            p = p->next;
        }
        p->next = reverseKGroup(rest, k);
        head = dummy->next;
        delete dummy;
        return head;
    }
private:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* reversed = nullptr;
        while (head != nullptr) {
            ListNode* nxt = head->next;
            head->next = reversed;
            reversed = head;
            head = nxt;
        }
        return reversed;
    }
};
```

## [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)

#### Java

快慢指针找中点

```java
class Solution {
    private ListNode reverse(ListNode head) {
        ListNode rev = null;
        while (head != null) {
            ListNode tmp = head.next;
            head.next = rev;
            rev = head;
            head = tmp;
        }
        return rev;
    }

    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) return true;
        // 使用快慢指针, 找出链表中点
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        ListNode target = (fast == null) ? reverse(slow) : reverse(slow.next);
        while (target != null) {
            if (target.val != head.val) return false;
            target = target.next;
            head = head.next;
        }
        return true;
    }
}
```

#### C++

```c++
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return true;
        ListNode *slow = head, *fast = head->next->next;
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }
        ListNode* tar = fast == nullptr ? slow->next : slow->next->next;
        tar = reverseList(tar);
        while (tar != nullptr) {
            if (tar->val != head->val) return false;
            tar = tar->next;
            head = head->next;
        }
        return true;
    }
private:
    ListNode* reverseList(ListNode* head) {
        ListNode* reversed = nullptr;
        while (head != nullptr) {
            ListNode* nxt = head->next;
            head->next = reversed;
            reversed = head;
            head = nxt;
        }
        return reversed;
    }
};
```

## [143. Reorder List](https://leetcode.com/problems/reorder-list/)

#### Java

*Recursion*

```java
class Solution {
    public void reorderList(ListNode head) {
        if (head == null || head.next == null || head.next.next == null) return;
        ListNode p = head;
        while (p.next.next != null) p = p.next;
        ListNode last = p.next;
        p.next = null;
        ListNode rest = head.next;
        reorderList(rest);
        head.next = last;
        head.next.next = rest;
    }
}
```

*Queue*

```java
class Solution {
    public void reorderList(ListNode head) {
        if (head == null || head.next == null || head.next.next == null) return;
        LinkedList<ListNode> q = new LinkedList<>();
        ListNode cur = head.next;
        while (cur != null) {
            q.addLast(cur);
            cur = cur.next;
        }
        cur = head;
        while (true) {
            cur.next = q.removeLast();
            cur = cur.next;
            if (q.isEmpty()) break;
            cur.next = q.removeFirst();
            cur = cur.next;
            if (q.isEmpty()) break;
        }
        cur.next = null;
    }
}
```

#### C++

以下递归写法效率很低

```c++
class Solution {
public:
    void reorderList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return;
        ListNode* p = head;
        while (p != nullptr && p->next != nullptr && p->next->next != nullptr) p = p->next;
        ListNode* last = p->next;
        p->next = nullptr;
        ListNode* rest = head->next;
        head->next = last;
        reorderList(rest);
        last->next = rest;
    }
};
```

*Fast / Slow Pointer*

```c++
class Solution {
public:
    void reorderList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return;
        ListNode *slow = head, *fast = head->next->next;
        while (fast != nullptr && fast->next != nullptr) {
            fast = fast->next->next;
            slow = slow->next;
        }
        ListNode* tail;
        if (fast == nullptr) { // 确保tail节点数 <= head
            tail = slow->next;
            slow->next = nullptr;
        } else {
            tail = slow->next->next;
            slow->next->next = nullptr;
        }
        tail = reverseList(tail);
        ListNode *p = head;
        while (p != nullptr && tail != nullptr) { // 把tail中的节点一个个插入进head
            ListNode* nxt = p->next;
            p->next = tail;
            tail = tail->next;
            p->next->next = nxt;
            p = nxt;
        }
    }
private:
    ListNode* reverseList(ListNode* head) {
        ListNode* rev = nullptr;
        while (head != nullptr) {
            ListNode* nxt = head->next;
            head->next = rev;
            rev = head;
            head = nxt;
        }
        return rev;
    }
};
```

## [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)

#### Java

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;
        ListNode slow = head.next, fast = head.next.next;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) return true;
        }
        return false;
    }
}
```
