---
title: Collection
date: 2023-09-13 00:00:00
tags: java
categories: lang
---

---

# 包装类

八个基本数据类型 -> 包装类

```
boolean -> Boolean
char -> Character
---
byte -> Byte
short -> Short
int -> Integer
long -> Long
float -> Float
double -> Double
```

六个数字基本类型的包装类继承了 Number 抽象类, 它们实现了抽象方法: `intValue(), longValue(), floatValue(), doubleValue(), shortValue(), byteValue()`

以上是实现类型转化的, 例如 `Double d = new Double(3.14)`; `d` 中有 `intValue()` 方法, 可以转为 `int`

```java
Integer int1 = new Integer(1);
Integer int2 = Integer.valueOf(2);  // 推荐

int n1 = int1.intValue();

// String 转 Integer 对象
Integer int3 = new Integer("333");
Integer int4 = Integer.parseInt("999");  // 推荐
Integer int44 = Integer.valueOf("999");

// Integer 转 String
String str1 = int1.toString();

Integer int5 = Integer.MAX_VALUE;
Integer int6 = Integer.MIN_VALUE;
```

## 自动装箱拆箱

装箱 `Integer a = Integer.valueOf(3)`

拆箱 `int b = a.intValue()`

## 包装类的缓存问题

```java
Integer n1 = 4000;
Integer n2 = 4000;

n1 == n2;  // false
n1.equals(n2);  // true

// 包装类的缓存问题  -128 <= n <= 127  直接取缓存数组中的元素 缓存了 Integer(-128) ... Integer(127), 相当于是一个对象池 (static) 只要使用 Integer, 就会制造一个static的缓存对象池
Integer n3 = 123;
Integer n4 = 123;
n3 == n4;  // true
n3.equals(n4);  // true
```

## 包装类数组的装箱拆箱

### `int[]` 转换为 `Integer[]`

可以使用 `Arrays.stream()` 方法, 将 `int[]` 转换为流, 并使用 `boxed()` 方法将基本类型 `int` 转换为包装类型 `Integer`, 然后将其转换为数组:

```java
int[] intArray = {1, 2, 3, 4, 5};
Integer[] integerArray = Arrays.stream(intArray).boxed().toArray(Integer[]::new);
```

### `Integer[]` 转换为 `int[]`

使用 `Arrays.stream()` 方法将 `Integer[]` 转换为流, 然后使用 `mapToInt()` 方法将流中的 `Integer` 元素转换为基本类型 `int`:

```java
Integer[] integerArray = {1, 2, 3, 4, 5};
int[] intArray = Arrays.stream(integerArray).mapToInt(Integer::intValue)/* 或 mapToInt(x -> x) */.toArray();
```

# 泛型 Generics

数据类型参数化

不使用泛型时, 要使用 Object + instanceof + 强制类型转换, 很麻烦

类型擦除: 泛型仅存在于编译中, 编译之后都会被搞成 Object

## 定义泛型

通常使用 E T K V N ? 来代表泛型

```
E - Element 在容器中使用
T - Type 普通的 Java 类
K - Key
V - Value
? - 不确定的类型
```

## 泛型类

```java
public class Resp<T> {
    private T data;
    private String msg;
    private int code;
    
    public void setData(T data) {
        this.data = data;
    }
}
```

## 泛型接口

```java
public interface UserService<T> {
    T login(UsernamePasswordDTO);
}
```

## 泛型方法

```java
public <T> void setName(T name) {  // 在方法前定义要使用的泛型
    ...
}

public <T> T getName(T name) {
    ...
}
```

静态方法无法使用类上定义的泛型, 静态方法只能定义泛型方法

```java
public static <T> T f(T para) {}
```

# 容器 Collection

## 各容器关系图

```
- Collection (I) (单例集合)
  |
  |- List (I)
  |  |
  |  |- ArrayList (C)
  |  |
  |  |- LinkedList (C)
  |  |
  |  `- Vector (C)
  |    |
  |    `- Stack (C)
  |
  |- Set (I)
  |  |
  |  |- HashSet (C)
  |  |
  |  `- TreeSet (C)
  |
  `- Queue (I)
    |
    |- Deque (I)
    |  |
    |  |- LinkedList (C)
    |  `- ArrayDeque (C)
    |
    |- LinkedList (C)
    |
    `- PriorityQueue (C)
    
    
- Map (I) (双例集合)
  |
  |- HashMap (C)
  |
  `- TreeMap (C)
```

## List Interface

>  继承 Collection 接口, 添加了其它方法

可重, 有序

```java
int size();
boolean isEmpty();
boolean contains(Object o);
Iterator<E> iterator();
boolean add(E e);
void add(int idx, E e);
boolean addAll(Collection<? extends E> c);
boolean addAll(int index, Collection<? extends E> c);
E get(int idx);
E set(int idx, E e);
E remove(int idx);
boolean remove(Object o);
boolean removeAll(Collection<?> c);
boolean retainAll(Collection<?> c);
void clear();
int indexOf(Object o);
int lastIndexOf(Object o);
List<E> subList(int fromIndex, int toIndex);
Object[] toArray();
<T> T[] toArray(T[] a);
```

### ArrayList Class

>  实现 List 接口

底层数组实现, 查询效率高, 增删效率低, 线程不安全

### LinkedList Class

>  实现 List 接口

双链表实现, 查询效率低, 增删效率高, 线程不安全

### Vector Class

> 实现 List 接口

底层数组, 线程安全, 效率低 (所有方法都套上了 synchronized 关键字)

#### Stack Class

> 继承 Vector 类

是 Vector 的子类 Last In First Out

```java
E peek();
void push(E e);
E pop();
```

## Queue Interface

> 继承 Collection 接口

```java
boolean add(E e); // 入队, 满则抛异常
boolean offer(E e); // 入队, 满则 return flase

E remove(); // 出队, 空则抛异常
E poll(); // 出队, 空则 return null

E element(); // 获取队头, 空则抛异常
E peek(); // 获取队头, 空则 return null
```

### LinkedList Class

> 实现 Queue 接口

```java
void addFirst(E e); void addLast(E e);
E removeFirst(); E removeLast();
E getFirst(); E getLast();
boolean offerFirst(E e); boolean offerLast(E e);
E pollFirst(); E pollLast();
```

### PriorityQueue Class

> 实现 Queue 接口

## Set Interface

> 继承 Collection 接口, 未添加其它方法

不可重复, 无序

```java
boolean add(E e);
boolean	addAll(Collection<? extends E> c);

void clear();

boolean	contains(Object o);
boolean	containsAll(Collection<?> c);

boolean	remove(Object o);
boolean	removeAll(Collection<?> c);
```

### HashSet Class

> 实现 Set 接口, 底层 HashMap

Hash 算法 (散列算法) mod n 运算

HashSet 底层是 HashMap, HashMap 底层使用 数组 和 链表 实现存储

当两个元素 Hash 值得到位置相同时, 会 调用元素 `equals()` 方法, 如果相同则跳过, 不同则加到链表

数组默认长度是 16, 数组中存储的是单链表: `NodeList<Object>[] hashArr = new NodeList<>[16]; `

使用 HashSet 存储自定义对象

```java
public class User {
    public int id;
    public String username;
    
    // 要重写 hashCode() 和 equals() 以便使用集合存储
    public int hashCode() {
        int code = username == null ? 0 : username.hashCode();
        code = 31 * code + id;
        return code;
    }
    
    public boolean equals(Object o) {
        if (o instanceof User) {
            User user = (User) o;
            return (user.id == this.id) && (user.username == this.username);
        }
        return false;
    }
}
```

### TreeSet Class

> 实现 Set 接口, 底层 TreeMap

允许对元素排序 要给定排序规则

通过元素自身实现比较规则 (Integer, String) compareTo() 方法

```java
public class User implements Comparable<User> {
    public Integer id;
    public String username;
    
    @Override
    public int compareTo(User user) {  // 只有实现了 compareTo() 方法的类, 才可以 add 进 TreeSet
        if (this.id < user.id) return -1;
        if (this.id > user.id) return 1;
        return this.username.compareTo(user.username);
    }
    
    // 要有 hashCode 和 equals() 方法
}
```

通过比较器指定比较规则

```java
public class UserComparator implements Comparator<User> {
    @Override
    public int compare(User u1, User u2) {
        if (u1.id <= u2) return -1; // u1 先于 u2
        return 1;
    }
}

public class Main {
    public static void main(String[] args) {
        Set<User> set = new TreeMap<>(new UserComparator());
    }
}
```

## Map Interface

>  是双例集合的一个母接口, 不是 Collection 的子接口

Map 接口要求双例集合 - Collection 接口要求单例集合

```java
boolean	containsKey(Object key);
boolean	containsValue(Object value);

Set<Map.Entry<K,V>>	entrySet();
Set<K> keySet();
Collection<V> values();

V get(Object key);
V remove(Object key);
```

在 Java 中，可以使用多种方式遍历 `Map` 的键、值和键值对。以下是几种常见的方法：

### 遍历 Map 的键 (`keySet()`)

可以使用 `keySet()` 方法来获取 `Map` 中的所有键，并使用 `for-each` 循环来遍历这些键：

```java
Map<Integer, String> map = new HashMap<>();
map.put(1, "A");
map.put(2, "B");
map.put(3, "C");

for (Integer key : map.keySet()) {
    System.out.println("Key: " + key);
}
```

### 遍历 Map 的值 (`values()`)

可以使用 `values()` 方法来获取 `Map` 中的所有值，并使用 `for-each` 循环来遍历这些值：

```java
for (String value : map.values()) {
    System.out.println("Value: " + value);
}
```

### 遍历 Map 的键值对 (`entrySet()`)

可以使用 `entrySet()` 方法来获取 `Map` 中的所有键值对，使用 `for-each` 循环来遍历 `Map.Entry` 对象：

```java
for (Map.Entry<Integer, String> entry : map.entrySet()) {
    System.out.println("Key: " + entry.getKey() + ", Value: " + entry.getValue());
}
```

### 使用 `forEach()` 遍历键值对

从 Java 8 开始，`Map` 接口支持 `forEach` 方法，可以通过 Lambda 表达式遍历键值对：

```java
map.forEach((key, value) -> System.out.println("Key: " + key + ", Value: " + value));
```

### 使用 Iterator 遍历键值对

使用 `Iterator` 结合 `entrySet()` 来遍历 `Map` 的键值对，适合在遍历过程中进行修改操作：

```java
Iterator<Map.Entry<Integer, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
    Map.Entry<Integer, String> entry = iterator.next();
    System.out.println("Key: " + entry.getKey() + ", Value: " + entry.getValue());
}
```

### 总结

- 遍历键：使用 `keySet()`。
- 遍历值：使用 `values()`。
- 遍历键值对：使用 `entrySet()`，或通过 `forEach()` 方法和 Lambda 表达式来简化。
- 使用 `Iterator` 可以在遍历过程中安全地进行删除操作。

### HashMap Class

> 实现 Map 接口

### TreeMap Class

> 实现 Map 接口

可以对键排序, 要给定Key对象的排序规则

## Iterator 迭代器接口

```java

boolean hasNext();
Object next();
void remove();
```

使用 iterator 迭代 List

```java
List<String> lst = new LinkedList<>();
lst.add("asd");
lst.add("qwe");
lst.add("zxc");

Iterator<String> it = lst.iterator();  // 获取迭代器对象
while (it.hasNext()) it.next();
```

迭代 Set 是完全一样的

## 不可变的集合 (不仅仅是对象地址)

内容, 长度均不可变

使用 JDK 9 + 中的 .of() 静态方法

```java
List<String> =  List.of("C", "D", "E", "F", "G", "A", "B");
```


