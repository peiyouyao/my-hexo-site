---
title: Arrays & Collections & Stream API
date: 2023-08-19 00:00:00
tags: java
categories: lang
---

---

# Arrays 工具类

工具类中所有的方法都是静态方法

```java
static <T> List<T> asList(T... a)
static <T> T[] copyOf(T[] original, int newLength)
static<T> T[] copyRangeOf(T[] original, int from, int to)
```

# Collections 工具类

工具类中所有的方法都是静态方法

```java
shuffle()
reverse()
fill()
binarySearch()
copy()
```

# Array <=> Collection

Java 提供了几种方法将数组转换为集合, 或将集合转换为数组. 以下是常见的 API 和示例代码: 

## 数组转换为集合

使用 `Arrays.asList()` 方法可以将数组转换为集合. 这个方法返回一个 `List`, 不过返回的集合是固定大小的, 无法添加或删除元素. 

```java
public class ArrayToCollection {
    public static void main(String[] args) {
        String[] array = {"Apple", "Banana", "Cherry"};
        
        // 数组转换为集合（List）
        List<String> list = Arrays.asList(array);
        
        System.out.println(list);
    }
}
```

**注意**：使用 `Arrays.asList()` 返回的集合是一个固定大小的 `List`, 不能进行 `add()` 和 `remove()` 操作. 如果需要可变的集合, 可以进一步转换为 `ArrayList`：

```java
List<String> list = new ArrayList<>(Arrays.asList(array));
```

## 集合转换为数组

使用 `toArray()` 方法可以将集合转换为数组：

### 使用 `toArray()` 无参方法

返回一个 `Object[]` 数组：

```java
public class CollectionToArray {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("Apple");
        list.add("Banana");
        list.add("Cherry");
        
        // 集合转换为数组
        Object[] array = list.toArray();
        
        System.out.println(Arrays.toString(array));
    }
}
```

### 使用 `toArray(T[] a)` 方法

使用指定类型的数组, 可以避免类型转换问题：

```java
String[] array = list.toArray(new String[0]);
```

这里使用一个大小为 0 的数组（`new String[0]`）作为参数, Java 将根据集合的大小自动调整数组大小. 也可以传入指定大小的数组, 如果数组大小足够, 集合元素将放入该数组中. 

**注意**: 对二维List

```java
Integer[][] arr = lst.toArray(new Integer[lst.size()][]);
```

使用这种写法, 返回指定类型 (`Integer[]`) 的数组 (`Integer[][]`)

### 使用 Java 8 的 Stream

Java 8 的 `Stream` API 也可以用来实现数组和集合之间的转换：

#### 数组转换为集合

通过 `Arrays.stream()` 将数组转换为流, 再通过 `collect()` 转换为集合：

```java
List<String> list = Arrays.stream(array).collect(Collectors.toList());
```

#### 集合转换为数组

通过 `Stream` 的 `toArray` 方法转换为数组：

```java
String[] array = list.stream().toArray(String[]::new);
```

### 总结

- 数组转集合：`Arrays.asList()` 或 `Arrays.stream()`
- 集合转数组：`toArray()` 方法或流的 `toArray()` 方法

# `.sort()` 方法

在 Java 中, `Arrays.sort()` , `Collections.sort()`方法允许通过自定义排序来对数组进行排序. 二者几乎无区别.

可以使用 `Comparator` 接口, 也可以用 lambda 表达式来简化代码. 以下是几种常见的用法：

## 使用 `Comparator` 接口的自定义排序

我们可以创建一个实现 `Comparator` 接口的类来定义排序规则. 

```java
public class CustomSortExample {
    public static void main(String[] args) {
        String[] names = {"Alice", "Bob", "Charlie"};
        
        // 自定义 Comparator
        Comparator<String> lengthComparator = new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                return Integer.compare(s1.length(), s2.length());
            }
        };
        
        // 使用自定义 Comparator 进行排序
        Arrays.sort(names, lengthComparator);
        
        System.out.println(Arrays.toString(names));
    }
}
```

## 使用匿名内部类的方式实现 `Comparator`

在不需要单独实现类的情况下, 可以直接使用匿名内部类来定义排序规则：

```java
Arrays.sort(names, new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return Integer.compare(s1.length(), s2.length());
    }
});
```

```java
class User implements Comparable {
	public int comparaTo(Object o) {  // 要实现的方法 将当前对象和传入的对象比较 如果 this < user, this 在前, 反之 user 在前
        User user = (User) o;
        if (this.score < user.score) return -1;
        if (this.score > user.score) return 1;
        return 0;
    }
}
```

## 使用 Lambda 表达式

在 Java 8 及以上版本中, 可以使用 Lambda 表达式简化代码. Lambda 表达式适合用于简洁地定义 `Comparator`, 如按字符串长度排序：

```java
Arrays.sort(names, (s1, s2) -> Integer.compare(s1.length(), s2.length()));
```

Lambda 表达式还有一个好处就是可以捕获外部的变量 (函数内变量), 类似 Rust, Golang 中的闭包, 例如: 

```java
Map<Integer, Integer> freq = new HashMap<>();
...
Arrays.sort(nums, (n1, n2) -> freq.get(n1) - freq.get(n2)); // 捕获 freq, 按照出现频率排序
```

## 使用 `Comparator.comparing` 方法

Java 8 引入了 `Comparator.comparing()` 方法, 可以更加简洁地定义比较逻辑. 例如, 按字符串长度排序：

```java
Arrays.sort(names, Comparator.comparing(String::length));
```

## 使用 `reversed()` 方法进行反序

如果需要逆序排序, 可以使用 `reversed()` 方法. 例如, 将前面基于字符串长度的排序反转：

```java
Arrays.sort(names, Comparator.comparing(String::length).reversed());
```

## 复杂的自定义排序

可以组合多个排序条件, 例如先按长度排序, 如果长度相同, 再按字母顺序排序：

```java
Arrays.sort(names, Comparator.comparing(String::length).thenComparing(String::compareTo));
```

## 注意:

对于 `Collection`和 `Object[]` 我们可以使用 `.sort()` 方法轻松自定义排序, 但是对于基本类型数组, 如 `int[]`, 我们必须将它先装箱变成 `Integer[]` 后才能自定义排序方法, 不然就只能按照默认从小到大排序. 

# Stream API

## Stream 使用流程

获取 Stream 对象 -> 中间方法 -> 终结方法

### 获取 Stream 对象

**单例集合: `Stream<E> stream()` Collection 接口的方法**

```java
class Draft {
    public void draft() {
        List<String> lst = new ArrayList<String>();
        Collections.addAll(lst, "Bach", "Beethoven", "Mozart", "Handel", "Schubert");
        Stream<String> stream = lst.stream();  // 获取 stream 对象
        stream.forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        });  // stream 一旦被遍历就不能再次遍历

        // stream.forEach(s -> System.out.println(s));

        // stream.forEach(System.out::println);
    }
}
```

**双例集合: 使用 keySet(), entrySet() 转成单例集合**

```java
class Draft {
    public void draft() {
        HashMap<String, Integer> map = new HashMap<>();
        map.put("C", 1);
        map.put("D", 2);
        map.put("E", 3);
        map.put("F", 4);
        map.put("G", 5);
        map.put("A", 6);
        map.put("B", 7);

        map.keySet().stream().forEach(k -> System.out.println(k + " " + map.get(k)));  // 使用 keySet() 获取键

        map.entrySet().stream().forEach(e -> System.out.println(e.getKey() + " " + e.getValue()));  // 使用 entrySet() 获取每一个键值对
    }
}
```

**数组: `Stream<E> stream(T[])`Arrays 工具类中的静态方法**

```java
class Draft {
    public void draft() {
        int[] arr = {3,3,4,5,5,4,3,2,1,1,2,3,3,2,2};
        Arrays.stream(arr).forEach(x -> System.out.println(x));
    }
}
```

**一堆零散数据**

```java
class Draft {
    public void draft() {
        Stream<Integer> stream = Stream.of(3,3,4,5,5,4,3,2,1,1,2,3,3,2,2);
        stream.forEach(x -> System.out.println(x));
    }
}
```

### 中间方法

这些方法改变的是流中的数据, 对集合本身并无影响

**filter()**

```java
class Draft {
    public void draft() {
        ArrayList<String> lst = new ArrayList<>();
        Collections.addAll(lst, "EEFG", "GFED", "CCDE", "EDD-");
        lst.stream().filter(new Predicate<String>() {
            @Override
            public boolean test(String s) {  // true 留下 - false 过滤
                return !s.endsWith("-");
            }
        }).forEach(s -> System.out.println(s));

        lst.stream().filter(s -> !s.endsWith("-")).forEach(s -> System.out.println(s));
        lst.stream().filter(s -> { return !s.endsWith("-"); }).forEach(s -> System.out.println(s));
    }
}
```

**limit() & skip() & distinct()**

```java
class Draft {
    public void draft() {
        int[] arr = {3,3,4,5,5,4,3,2,1,1,2,3,3,2,2};
        Arrays.stream(arr).limit(3).forEach(x -> System.out.println(x));  // 保留前 n 个
        System.out.println("---");
        Arrays.stream(arr).skip(3).forEach(x -> System.out.println(x));  // 跳过前 n 个
        System.out.println("---");
        Arrays.stream(arr).distinct().forEach(x -> System.out.println(x));  // 去重 依赖对象的 hashCode() 和 equals() 方法 底层是 HashSet() 去重
    }
}
```

**concat() - Stream 中的静态方法**

```java
class Draft {
    public void draft() {
        ArrayList<String> lst1 = new ArrayList<>();
        Collections.addAll(lst1, "EEFG", "GFED", "CCDE", "EDD-");
        LinkedList<String> lst2 = new LinkedList<>();
        Collections.addAll(lst2, "GCDEF", "GCC", "AFGAB", "CCC", "FGFED", "EFEDC", "DEDCBC");

        Stream.concat(lst1.stream(), lst2.stream()).forEach(s -> System.out.println(s));
    }
}
```

**map()**

```java
class Draft {
    public void draft() {
        LinkedList<String> lst = new LinkedList<>();
        Collections.addAll(lst, "C-1", "D-2", "E-3", "F-4", "G-5", "A-6", "B-7");
        lst.stream().map(new Function<String, Integer>(/* 第一个泛型表示流中原本的类型, 第二个类型表示要转的类型 */) {
            @Override
            public Integer apply(String s) {
                String[] split = s.split("-");
                return Integer.valueOf(split[1]);
            }
        }).forEach(s -> System.out.println(s));  // 这里的 s 就是 Integer 了

        lst.stream().map(s -> {
            String[] split = s.split("-");
            return Integer.valueOf(split[1]);
        }).forEach(s -> System.out.println(s));

        lst.stream().map(s -> Integer.valueOf(s.split("-")[1])).forEach(s -> System.out.println(s));
    }
}
```

### 终结方法

**遍历 ``void forEach(Consumer action)``**

```java
class Draft {
    public void draft() {
        List<String> lst = new ArrayList<String>();
        Collections.addAll(lst, "Bach", "Beethoven", "Mozart", "Handel", "Schubert");
        lst.forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        });  // stream 一旦被遍历就不能再次遍历

         lst.forEach(s -> System.out.println(s));

         lst.forEach(System.out::println);
    }
}
```

**统计 ``long count()``**

```java
class Draft {
    public void draft() {
        ArrayList<String> lst1 = new ArrayList<>();
        Collections.addAll(lst1, "EEFG", "GFED", "CCDE", "EDD-");
        LinkedList<String> lst2 = new LinkedList<>();
        Collections.addAll(lst2, "GCDEF", "GCC", "AFGAB", "CCC", "FGFED", "EFEDC", "DEDCBC");

        long cnt = Stream.concat(lst1.stream(), lst2.stream()).filter(s -> s.startsWith("G") || s.startsWith("C")).count();  // 注意返回是 long
        System.out.println(cnt);
    }
}
```

**收集到数组 ``toArray()``**

```java
class Draft {
    public void draft() {
        List<String> lst = new ArrayList<String>();
        Collections.addAll(lst, "Bach", "Beethoven", "Mozart", "Handel", "Schubert");

        Object[] array = lst.stream().toArray();  // 这种情况只能返回 Object[]
        System.out.println(Arrays.toString(array));

        // toArray() 参数: 创建一个指定类型的数组
        String[] array1 = lst.stream().toArray(new IntFunction<String[]>(/* 泛型表示你想要的数组类型 */) {
            @Override
            public String[] apply(int value) {  // 参数 value 是数组的长度
                return new String[value];
            }
        });
        System.out.println(Arrays.toString(array1));

        String[] array2 = lst.stream().toArray(value -> new String[value]);
        System.out.println(Arrays.toString(array2));
    }
}
```

**收集到集合 ``collect(Collector collector``**

```java
class Draft {
    public void draft() {
        List<String> lst = new ArrayList<String>();
        Collections.addAll(lst, "Bach-17", "Beethoven-19", "Mozart-18", "Handel-17", "Schubert-19", "Bach-17");

        // 收集到 List
        List<String> list = lst.stream().filter(s -> s.startsWith("B")).collect(Collectors.toList());
        System.out.println(list);
        
        // 收集到 Set
        Set<String> set = lst.stream().filter(s -> s.startsWith("B")).collect(Collectors.toSet());
        System.out.println(set);
        
        // 收集到 Map
        Map<String, Integer> map = lst.stream()
                .filter(s -> s.startsWith("B"))
                .distinct()  // 收集到Map 要确保Key不重复
                .collect(Collectors.toMap(
                        new Function<String, String>() {  // toMap 第一个参数 -> 生成 key 的规则
                            @Override
                            public String apply(String s) {
                                return s.split("-")[0];
                            }
                        },
                        new Function<String, Integer>() {  // toMap 第二个参数 -> 生成 Value 的规则
                            @Override
                            public Integer apply(String s) {
                                return Integer.valueOf(s.split("-")[1]);
                            }
                        }
                ));
        System.out.println(map);

        map = lst.stream()
                .filter(s -> s.startsWith("B"))
                .distinct()
                .collect(Collectors.toMap(
                        s -> s.split("-")[0],
                        s -> Integer.valueOf(s.split("-")[1])
                ));
        System.out.println(map);
        
        // 收集到字符串
        List<String> words = List.of("Hello", "world", "this", "is", "Java");
        // 使用 Stream API 和 Collectors.joining() 来用空格连接字符串
        String result = words.stream().collect(Collectors.joining(" "));

    }
}
```

## toArray() 方法详解

`toArray()` 方法是 Java Stream API 中常用的方法, 用于将流 (`Stream`) 中的元素收集到一个数组中. `toArray()` 方法有两个不同的版本, 分别用于**对象流 (`Stream<T>`)** 和**基本类型流 (如 `IntStream`、`LongStream`、`DoubleStream`)**. 下面详细介绍这两种用法：

### `Stream<T>` 中的 `toArray()`

在 `Stream<T>` 中, `toArray()` 有两种常用形式：

#### `toArray()` 无参方法

这是最简单的形式, 将 `Stream<T>` 中的元素收集到一个 `Object[]` 数组中：

```java
Stream<String> stream = Stream.of("A", "B", "C");
Object[] array = stream.toArray();
```

#### `toArray(IntFunction<A[]> generator)` 方法

这个重载方法接受一个 `IntFunction<A[]>`, 用于生成指定类型的数组. 一般来说, 这个方法可以让我们将流收集到一个特定类型的数组中, 而不仅仅是 `Object[]`: 

```java
Stream<String> stream = Stream.of("A", "B", "C");
String[] array = stream.toArray(String[]::new);
```

这里, `String[]::new` 是一个数组构造引用, 用于生成正确类型的数组. 

**注意**: 这种方法产生不了基本类型数组 !!!

### 基本类型流 (`IntStream`) 中的 `toArray()`

对于基本类型流 (以 `IntStream` 为例) `toArray()` 方法专门返回一个基本类型数组: 

```java
IntStream intStream = IntStream.of(1, 2, 3);
int[] intArray = intStream.toArray();
```

### 基本类型与装箱类的互转

#### `Stream<Object> ` to `int[]`

```java
String[] words = {"apple", "banana", "cherry"};

int[] lengths = Arrays.stream(words)
                      .mapToInt(String::length) // 将每个字符串映射为其长度
                      .toArray(); // 转换为 int[]
```

```java
List<Integer> lst = new ArrayList<>();
Collections.addAll(lst, 1, 2, 3, 4, 5);
int[] arr = lst.stream().mapToInt(Integer::intValue).toArray();
```

#### `IntStream` to `Integer[]`

```java
int[] arr = { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
// Arrays.stream(arr) 是一个 IntStream 对象
Integer[] arrInteger1 = Arrays.stream(arr).mapToObj(Integer::new).toArray(Integer[]::new);
Integer[] arrInteger2 = Arrays.stream(arr).boxed().toArray(Integer[]::new);
```

两种方法把基本数据类型转成装箱类

### 总结

- 对于对象流 (`Stream<T>`), `toArray()` 可以将流元素收集到 `Object[]`, 或者指定类型的数组中. 
- 对于基本类型流 (`IntStream`、`LongStream`、`DoubleStream`), `toArray()` 会返回对应类型的基本数组 (如 `int[]`、`long[]`、`double[]`).
- 使用 `toArray(IntFunction<A[]> generator)` 可以更方便地生成特定类型的数组, 并避免类型转换的麻烦.

# 杂项

## String 及其相关

String (不可变), StringBuilder (可变, 效率高, 线程不安全), StringBuffer (可变, 效率低, 线程安全)

## 二维数组

`type + [][]`

```java
int[][] arr = new int[3][];
arr[0] = new int[2];
arr[1] = {1,2,3};
arr[2] = new int[4];
```

`new int[3][]` 表示: 初始化一个int长度为3类型的数组, 该数组的元素也是 int 类型的数组
