---
title: My Kotlin
date: 2024-12-01 00:00:00
tags: kotlin
categories: lang
---

---

## 自定义规则排序

### 1. 使用 `sortedWith` 和 `Comparator`

`sortedWith` 方法需要传入一个 `Comparator` 对象，定义你的排序规则。

```kotlin
fun main() {
    val array = arrayOf(5, 2, 8, 1, 3)

    // 自定义排序规则：按数字的奇偶性排序，奇数排在前，偶数排在后，奇数和奇数按升序，偶数和偶数按升序
    val sortedArray = array.sortedWith(compareBy(
        { it % 2 == 0 }, // 奇偶性，奇数 (false) 在前，偶数 (true) 在后
        { it }           // 同为奇数或偶数时按升序排序
    ))

    println(sortedArray) // 输出: [1, 3, 5, 2, 8]
}
```

### 2. 使用 `sortWith` 方法对原数组排序

如果需要对原数组进行排序而不是生成一个新数组，可以使用 `sortWith` 方法。

```kotlin
fun main() {
    val array = arrayOf(5, 2, 8, 1, 3)

    // 自定义排序规则：按降序排序
    array.sortWith(Comparator { a, b -> b - a })

    println(array.joinToString(", ")) // 输出: 8, 5, 3, 2, 1
}
```

### 3. 使用 `lambda` 表达式定义简单的比较规则

Kotlin 支持直接使用 lambda 表达式来定义比较规则。

```kotlin
fun main() {
    val array = arrayOf(5, 2, 8, 1, 3)

    // 按数字的绝对值升序排序
    val sortedArray = array.sortedWith { a, b -> 
        a.absoluteValue - b.absoluteValue
    }

    println(sortedArray) // 输出: [1, 2, 3, 5, 8]
}
```

### 4. 综合使用 `compareBy` 和多条件排序

`compareBy` 可以链式调用多个条件进行排序，优雅地定义复杂规则。

```kotlin
fun main() {
    val array = arrayOf(Pair("Apple", 2), Pair("Banana", 1), Pair("Apple", 1))

    // 优先按第一个元素排序，若第一个元素相同，则按第二个元素排序
    val sortedArray = array.sortedWith(compareBy({ it.first }, { it.second }))

    println(sortedArray) // 输出: [(Apple, 1), (Apple, 2), (Banana, 1)]
}
```

### 注意事项

1. 如果对大数据量排序，请确保排序规则不会引发性能瓶颈。
2. `sortedWith` 会返回一个新的数组，不会修改原数组。
3. 若需要逆序，可以结合 `reversed` 方法或使用 `compareByDescending`。