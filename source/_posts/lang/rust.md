---
title: My Rust
date: 2024-11-01 00:00:00
tags: rust
categories: lang
---

---

# MyRust

## Rust 优秀的签名设计

> 我注意到一个写代码中的问题: 
>
> 例如 `int[] plusOne(int[] arr)` 这个函数签名无法确保程序员是在 arr 上原地修改的, 还是把arr clone 了一份
>
> 而rust 则可以通过对函数签名的要求明确表示出这一点
>
> 例如
>
> ```rust
> fn plus_one(arr: Vec<i32>) -> Vec<i32> // arr 走出函数便不再可用` 
> fn plus_one(arr: &Vec<i32>) -> Vec<i32>` 
> fn plus_one(arr: &mut Vec<i32>)`
> ```

**Rust 函数签名设计的一个核心优势**：**通过函数签名清晰表达了数据的所有权、借用和修改关系**。

在很多语言（如 C++、Java）中，**无法仅通过函数签名来判断**一个数组（或列表）是否是**原地修改的**，**还是克隆了一份新数组**。但在 Rust 中，**函数签名本身就明确了这一点**，因为 Rust 强制要求开发者**显式声明所有权和可变性**。

Rust 三权分立: 所有权, 读权, 写权

```rust
#[derive(PartialEq, Eq, Clone, Debug)]
pub struct ListNode {
    pub val: i32,
    pub next: Option<Box<ListNode>>,
}

impl ListNode {
    pub fn new(val: i32) -> Self {
        ListNode { val, next: None }
    }
}

pub fn add_one_node(head: &mut Option<Box<ListNode>>, value: i32) {
    let mut p: &mut Option<Box<ListNode>> = head; // p 是 head 的可变引用
    while let Some(node) = p {
        p = &mut node.next; // 不断向链表尾部移动
    }
    *p = Some(Box::new(ListNode::new(value))); // 在尾部插入新节点
}

pub fn show_list(head: &Option<Box<ListNode>>) {
    let mut p: &Option<Box<ListNode>> = head; // p 是 &Option<Box<ListNode>>
    while let Some(node) = p {
        print!("{} -> ", node.val); // 打印当前节点的值
        p = &node.next; // 向下一个节点移动，p 的类型是 &Option<Box<ListNode>>
    }
    println!("None");
}

fn main() {
    let mut head: Option<Box<ListNode>> = Some(Box::new(ListNode::new(1)));
    add_one_node(&mut head, 2);
    add_one_node(&mut head, 3);
    add_one_node(&mut head, 4); // 1 -> 2 -> 3 -> 4
    show_list(&head);
}
```

## Tip: `for x in y`

| `y` 类型      | 调用的迭代器   | `x` 的类型 | 说明                         |
| ------------- | -------------- | ---------- | ---------------------------- |
| `Vec<T>`      | `.into_iter()` | `T`        | 消耗集合，转移所有权         |
| `&Vec<T>`     | `.iter()`      | `&T`       | 不可变引用迭代器，不消耗集合 |
| `&mut Vec<T>` | `.iter_mut()`  | `&mut T`   | 可变引用迭代器，允许修改集合 |
| `[T; N]`      | `.iter()`      | `&T`       | 固定数组转为切片的不可变引用 |
| `&[T]`        | `.iter()`      | `&T`       | 切片的不可变引用             |
| `&mut [T]`    | `.iter_mut()`  | `&mut T`   | 切片的可变引用               |

通过这些规则，可以根据 `y` 的类型快速判断 `x` 的类型，以及是否发生所有权转移！

## Tip: `let Some(x) = y`

| Y Type           | X Type   | equals                    | DEsc                         |
| ---------------- | -------- | ------------------------- | ---------------------------- |
| `Option<T>`      | `T`      | `x = y.unwrap()`          | x 会取得数据的所有权         |
| `&Option<T>`     | `&T`     | `x = y.as_ref().unwrap()` | x 只是读借用, 不会取得所有权 |
| `&mut Option<T>` | `&mut T` | `x = y.as_mut().unwrap()` | x 是写借用, 不会取得所有权   |

特别地

```
当 y: Option<T>
while let Some(x) = &y => x: &T
if let Some(x) = &mut y => x: &mut T
```

