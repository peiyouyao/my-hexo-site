---
title: Tips
date: 2023-01-26 00:00:00
tags: java
categories: lang
---

---

> 在 Java 开发, 刷题中常用的 API 和小技巧
>
> Commonly used APIs and tips in Java development and problem-solving

## 数组 Array

- 求长度 `nums.length`

- 升序重排 `Arrays.sort(nums)`

- 填充数组 `Arrays.fill(nums, val)`

- 转化为字符串 `Arrays.toString(nums)`

- 将多维数组转化为字符串 `Arrays.deepToString(nums)`

- Array -> List `new LinkedList(Arrays.asList(nums))`

- List -> Array `Integer[] array = list.toArray(new Integer[0]);`

- 字符串数组转化为字符串 `new String(chars)`

- 判断数组元素相同 `Arrays.equals(nums1, nums2)`

- 深拷贝数组

  ```java
  int[] original = {1, 2, 3};
  int[] copy = original.clone();
  ```

- 二维数组深拷贝

  ```java
  int[][] original = {{1, 2}, {3, 4}};
  int[][] copy = Arrays.stream(original)
                       .map(int[]::clone)
                       .toArray(int[][]::new);
  ```



## 字符串 String

- 求长度 `str.length()`
- 以`regex`分割字符串为数组 `str.split(regex)`
- 查找目标字符（串）（找不到返回-1） `str.indexOf(c)` `str.lastIndexOf(c)` `str.indexOf(subStr)` `str.lastIndexOf(subStr)`
- 判断字符串相同 `strA.equals(strB)`
- 比较字符串的字典序 `strA.compareTo(strB)`
- 判断是否以目标前缀开头 `str.startsWith(prefix)`
- 判断是否以目标后缀结尾 `str.endsWith(suffix)`
- 去除首尾空格 `str.trim()`
- 全小写 `str.toLowerCase()`
- 全大写 `str.toUpperCase()`
- 替换字符 `str.replace(oldChar, newChar)`
- 替换字符串 `str.replace(oldRegex, newRegex)`
- 获取从`[start, end - 1]`的子串 `str.substring(start, end)`
- 获取第i个字符 `str.charAt(i)`
- 转化为字符数组 `str.toCharArray()`
- 转化为StringBuilder `new StringBuilder(str)`
- **转化为整数** `Integer.parseInt(str)` `Integer.valueOf(str)`
- **数字转化为字符串** `String.valueOf(num)`
- 翻转字符串 `String reversed = new StringBuilder("hello").reverse().toString();`



## 可变字符串 StringBuilder

- 在末尾添加新字符（串） `sb.append(c)` `sb.append(str)`
- 删除第i个字符 `sb.deleteCharAt(i)`
- 删除从`[start, end - 1]`的子串 `sb.delete(start, end)`
- 替换第i个字符 `sb.setCharAt(i, newChar)`
- 替换从`[start, end - 1]`的子串 `sb.replace(start, end, str)`
- 在第i个字符之前插入字符串 `sb.insert(i, str)`
- 反转字符串 `sb.reverse()`
- 转化为String `sb.toString()`



## 链表 List

- 末尾添加元素 `list.add(element)`

- 插入元素 `lst.add(index, value)`

- 获取第i个位置的元素 `list.get(i)`

- 替换第i个位置的元素 `list.set(i, newElement)`

- 删除第i个位置的元素 `list.remove(i)`

- 删除最后一个元素 `linkedList.removeLast()`

- 求长度 `list.size()`

- 判断是否为空 `list.isEmpty()`

- 清空 `list.clear()`

- 判断是否包含某元素 `list.contains(object)`

- 查找元素（找不到返回-1） `list.indexOf(object)` `list.lastIndexOf(object)`

- 转化为集合 `new HashSet<>(list)`

- 转化为整数数组 `list.stream().mapToInt(x -> x).toArray()` `list.stream.mapToInt(Integer::intValue).toArray()`

- 转化为字符串数组 `(String[]) list.toArray(new String [0])`

- 深拷贝

  ```java
  List<Integer> path = new ArrayList<>(Arrays.asList(1, 2, 3));
  List<Integer> copy = new ArrayList<>(path);
  ```

- 与 Set 的转换

  ```java
  Set<Integer> set = new HashSet<>(list);
  List<Integer> listFromSet = new ArrayList<>(set);
  ```

- 排序 `Collections.sort(list);`


## 栈 Stack

- 压入栈顶 `stk.push(element)`
- 从栈顶弹出 `stk.pop()`
- 查看栈顶元素 `stk.peek()`
- 求长度 `stk.size()`
- 判断是否为空 `stk.isEmpty()` `stk.empty()`

## 单端队列 Queue

- 从队尾添加元素 失败会抛异常`queue.add(element)` 失败会返回false`queue.offer(element)`
- 从队头移除元素 空指针会抛异常`queue.remove()` 空指针会返回null`queue.poll()`
- 查看队头元素 空指针会抛异常`queue.element()` 空指针会返回null`queue.peek()`
- 求长度 `queue.size()`
- 判断是否为空 `queue.isEmpty()`
- 清空 `queue.clear()`
- 判断是否包含某元素 `queue.contains(object)`

## 双端队列 Deque

- 从队尾添加元素 失败会抛异常`deque.addLast(element)` 失败会返回false`deque.offerLast(element)`
- 从队头添加元素 失败会抛异常`deque.addFirst(element)` `deque.push()` 失败会返回false`deque.offerFirst(element)`
- 从队尾删除元素 空指针会抛异常`deque.removeLast()` 空指针会返回null`deque.pollLast()`
- 从队头删除元素 空指针会抛异常`deque.removeFirst()` `deque.pop()` 空指针会返回null`deque.pollFirst()`
- 查看队尾元素 空指针会抛异常`deque.getLast()` 空指针会返回null`deque.peekLast()`
- 查看队头元素 空指针会抛异常`deque.getFirst()` 空指针会返回null`deque.peekFirst()`
- 求长度 `deque.size()`
- 判断是否为空 `deque.isEmpty()`
- 清空 `deque.clear()`

## 集合 Set

- 添加元素 `set.add(element)`
- 删除元素 `set.remove(element)`
- 判断是否包含某元素 `set.contains(object)`
- 求长度 `set.size()`
- 清空 `set.clear()`
- 获取不小于key的最小元素（空指针返回null） `treeSet.ceiling(key)`
- 获取不大于key的最大元素（空指针返回null） `treeSet.floor(key)`
- 获取第一个元素 `treeSet.first()`
- 获取最后一个元素 `treeSet.last()`
- 转化为整数数组 `set.stream().mapToInt(x -> x).toArray()`

## 映射 Map

- 添加或修改键值对 `map.put(key, val)`
- 删除键值对 `map.remove(key)`
- 获取指定键对应的值 `map.get(key)`
- 获取指定键对应的值（如果不存在，则设定默认值） `map.getOrDefault(key, defaultVal)`
- 判断是否包含某个键 `map.containsKey(key)`
- 判断是否包含某个值 `map.containsValue(val)`
- 判断是否为空 `map.isEmpty()`
- 求长度 `map.size()`
- 清空 `map.clear()`
- 生成对应的键值对集合 `map.entrySet()`
- 获取不小于key的最小元素（空指针返回null） `treeMap.ceilingEntry(key)`
- 获取不大于key的最大元素（空指针返回null） `treeMap.floorEntry(key)`
- 获取第一个键 `treeMap.firstKey()`
- 获取最后一个键 `treeMap.lastKey()`

## 键值对 Map.Entry

- 获取键值对的键 `entry.getKey()`
- 获取键值对的值 `entry.getValue()`
- 修改键值对的值 `entry.setValue(val)`



