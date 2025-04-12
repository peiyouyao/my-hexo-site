---
title: Bugs
date: 2023-07-20 00:00:00
tags: java
categories: lang
---

---

>这是一篇 Java 刷题, 开发过程中, 常常容易产生的 bug 汇总.
>
>This is a summary of bugs that are often prone to occur during the development process in Java.

## 传值与传引用
```java
class Solution {
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] arr = new int[]{1, 2, 3}; // arr 是栈上变量, 其值是一个指向堆上 int[] 的指针 (地址)
        s.clear(arr); // 函数参数传递指针, 通过指针找到堆上数组, 把数组全部置为 0 了
        System.out.println(Arrays.toString(arr)); // [0, 0, 0]
        
        arr = new int[]{1, 2, 3};
        s.clear1(arr); // 也是函数参数传递指针, 但函数内是修改了形参的值 (修改了指针), 但因为没有返回, 外部变量不受影响
        System.out.println(Arrays.toString(arr));  // [1, 2, 3]
    }
    public void clear(int[] arr) { Arrays.fill(arr, 0); }
    public void clear1(int[] arr) { arr = new int[arr.length]; }
}
```

由此可以看出, Java 中的函数参数都是值传递, 只不过八大基本变量的值是数值, 对象变量的值是指针

## 数组引用

```java
class Solution {
    public void test0() {
        int[][] arr = {{0, 1}, {1, 2}, {2, 3}, {3, 4}, {4, 5}, {5, 6}};
        int[] a0 = arr[0];
        int[] a1 = arr[1];

        a0 = a1;
        System.out.println(Arrays.deepToString(arr));
        // [[0, 1], [1, 2], [2, 3], [3, 4], [4, 5], [5, 6]]
    }

    public void test1() {
        int[][] arr = {{0, 1}, {1, 2}, {2, 3}, {3, 4}, {4, 5}, {5, 6}};
        int[] a0 = arr[0];
        int[] a1 = arr[1];

        a0[0] = a1[0];
        a0[1] = a1[1];
        System.out.println(Arrays.deepToString(arr));
        // [[1, 2], [1, 2], [2, 3], [3, 4], [4, 5], [5, 6]]
    }
}
```

**在 `test0` 方法中**：

1. `a0` 和 `a1` 分别引用了 `arr[0]` 和 `arr[1]`。
2. 执行 `a0 = a1;` 后，`a0` 被赋予了 `a1` 的引用，指向与 `a1` 相同的数组对象，即 `arr[1]`。
3. 由于 `a0` 原本指向的 `arr[0]` 没有被修改，`arr` 数组的内容保持不变。
4. 最终输出为 `[[0, 1], [1, 2], [2, 3], [3, 4], [4, 5], [5, 6]]`。

这里的关键是 `a0 = a1;` 只是改变了 `a0` 的引用指向，但没有改变 `arr[0]` 的内容。

**在 `test1` 方法中**：

1. `a0` 和 `a1` 同样分别引用了 `arr[0]` 和 `arr[1]`。
2. 然而，这里执行了 `a0[0] = a1[0];` 和 `a0[1] = a1[1];`。这些操作实际上修改了 `a0` 指向的数组内容，即修改了 `arr[0]` 的值。
3. 因为 `a0` 指向 `arr[0]`，修改 `a0` 的内容等同于直接修改 `arr[0]`。
4. 最终输出为 `[[1, 2], [1, 2], [2, 3], [3, 4], [4, 5], [5, 6]]`，因为 `arr[0]` 的内容已被更新为与 `arr[1]` 相同。

**注**: `aObj = bObj` 相当于把 aObj 的引用指向了 bObj. 如果它们是 **"不可变对象"** (例如, String 和 一系列装箱类 Integer), 那么, 就起到了等价于 把 bObj 的值 赋给 aObj 的效果 (因为当修改 aObj 时, 只能重新 new 对象, 这样, bObj 相当于没有受影响). 



## `toArray()` 方法

错误代码

```java
LinkedList<int[]> lst = new LinkedList<>();
lst.add(new int[] {0, 1});
lst.add(new int[] {2, 3});
lst.toArray();  // error
```

`lst.toArray();` 这一句出现报错是因为 `toArray()` 方法返回的是一个 `Object[]` 类型的数组，而不是 `int[][]` 类型。

为了返回 `int[][]` 类型的数组，你需要使用 `toArray(T[] a)` 的重载方法 (这里, 相当于泛型 T 为 `int[]`)，将结果转换成 `int[][]`。具体可以如下修改：

```
lst.toArray(new int[lst.size()][]);
```

这样会确保 `lst` 转换成 `int[][]` 类型的数组。

- `lst.toArray(new int[lst.size()][])` 的作用是创建一个与 `lst` 相同大小的 `int[][]` 数组，并将 `res` 中的元素复制到这个数组中。
- 这种用法是标准的 `ArrayList` 和 `LinkedList` 转换为数组的方式，当需要指定返回数组的类型时非常常用。

**注**: `int[]` 是类型 (对象), `int[n]` 是实例, 故上面的泛型 T 为 `int[]`

## `Integer.MAX_VALUE` & `Integer.MIN_VALUE`

```java
System.out.println(Integer.MAX_VALUE);  // 2147483647  (2^31 - 1)
System.out.println(Integer.MIN_VALUE);  // -2147483648  (-2^31)
System.out.println(-Integer.MAX_VALUE);  // -2147483647  (-2^31 + 1)
System.out.println(-Integer.MIN_VALUE);  // 溢出 -2147483648  (2^31)
```

