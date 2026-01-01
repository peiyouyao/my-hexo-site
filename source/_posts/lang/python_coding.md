---
title: Python Coding
date: 2025-08-28 00:00:00
tags: python
categories: lang
---

---

# Python Type Hints (Analogous to Java Generics)

Python supports **type hints** for static type checking (e.g., with `mypy`), which is similar to Java generics but optional and enforced only by tools, not at runtime. Python is dynamically typed, so type hints are for clarity and tooling, not compilation.

```python
from typing import List, Dict, Set, TypeVar, Generic

T = TypeVar('T')  # Generic type variable, like Java's T

class Resp(Generic[T]):  # Generic class
    def __init__(self, data: T, msg: str, code: int):
        self.data: T = data
        self.msg: str = msg
        self.code: int = code
```

- **Type Variables**: Use `TypeVar` for generic types (like `T`, `K`, `V` in Java).
- **No Runtime Type Erasure**: Unlike Java’s generics, Python’s type hints are not erased but are ignored at runtime unless explicitly checked.
- **Common Types**: `List[T]`, `Dict[K, V]`, `Set[T]`, etc., from the `typing` module.

## Generic Functions

```python
from typing import TypeVar, Callable

T = TypeVar('T')

def process_name(name: T) -> T:
    return name

# Example usage
result: str = process_name("Mozart")  # Type hint ensures str
result_int: int = process_name(42)    # Type hint ensures int
```

# Python Built-in Types (Analogous to Java Wrapper Classes)

Python’s basic types (`int`, `float`, `str`, `bool`) are immutable and don’t require explicit wrapper classes like Java’s `Integer` or `Double`. However, Python provides methods for type conversion.

```python
# Type conversions
num_str: str = "123"
num_int: int = int(num_str)  # String to int
num_float: float = float(num_str)  # String to float
num_str_back: str = str(num_int)  # Int to string

# Constants
max_int: int = 2**31 - 1  # Similar to Integer.MAX_VALUE (for 32-bit int)
min_int: int = -2**31     # Similar to Integer.MIN_VALUE
```

- **No Auto-Boxing/Unboxing**: Python doesn’t distinguish between primitive and wrapper types; `int` is an object with methods like `.__str__()`.
- **Caching**: Small integers (`-5` to `256`) and some strings are cached (similar to Java’s `Integer` cache for `-128` to `127`).

```python
a: int = 256
b: int = 256
print(a is b)  # True (cached)

c: int = 257
d: int = 257
print(c is d)  # False (not cached)
```

# Python Lists (Analogous to Java Arrays and ArrayList)

Python’s `list` is a dynamic array, similar to Java’s `ArrayList`. It’s mutable, resizable, and supports random access.

```python
# Initialize list
notes: List[str] = ["C", "C#", "D", "D#", "E", "F", "F#", "G", "G#", "A", "A#", "B"]

# Basic operations
notes.append("C")          # Add element (O(1) amortized)
notes.insert(1, "Db")      # Insert at index (O(n))
notes.pop()                # Remove last (O(1))
notes.pop(0)               # Remove at index (O(n))
notes[2] = "D2"            # Update element (O(1))
print(notes[0])            # Access element (O(1))

# Slicing (similar to Java's subList)
subset: List[str] = notes[1:4]  # Returns ["Db", "D2", "D#"]

# List comprehension (similar to Java Stream)
evens: List[int] = [x for x in range(10) if x % 2 == 0]  # [0, 2, 4, 6, 8]
```

- **Performance**: 
  - Access/Modify: O(1)
  - Insert/Delete (non-end): O(n)
  - Append/Pop (end): O(1) amortized
- **Thread Safety**: Python lists are not thread-safe (use `threading.Lock` if needed).
- **Immutable Alternative**: Use `tuple` for immutable sequences.

```python
# Tuple (immutable)
notes_tuple: tuple = ("C", "D", "E")
# notes_tuple[0] = "F"  # Error: tuples are immutable
```

# Python Sets (Analogous to Java Set)

Python’s `set` is similar to Java’s `HashSet`, providing O(1) average-case operations for add, remove, and lookup. Python lacks direct equivalents to `LinkedHashSet` and `TreeSet`, but alternatives exist.

## `set` (Analogous to HashSet)

```python
# Initialize set
unique_notes: Set[str] = {"C", "D", "E"}
unique_notes.add("F")           # Add element (O(1) average)
unique_notes.remove("C")        # Remove element (O(1) average, raises KeyError if not found)
unique_notes.discard("D")       # Remove if present (O(1) average, no error)
print("E" in unique_notes)      # Check containment (O(1) average)

# Set operations
set1: Set[int] = {1, 2, 3}
set2: Set[int] = {2, 3, 4}
print(set1 | set2)  # Union: {1, 2, 3, 4}
print(set1 & set2)  # Intersection: {2, 3}
print(set1 - set2)  # Difference: {1}
print(set1 ^ set2)  # Symmetric difference: {1, 4}
```

- **Custom Objects**: Must implement `__hash__` and `__eq__` for use in sets (similar to Java’s `hashCode` and `equals`).

```python
class User:
    def __init__(self, id: int, username: str):
        self.id: int = id
        self.username: str = username
    
    def __hash__(self) -> int:
        return hash((self.id, self.username))
    
    def __eq__(self, other) -> bool:
        if isinstance(other, User):
            return self.id == other.id and self.username == other.username
        return False

users: Set[User] = {User(1, "Bach"), User(2, "Beethoven")}
```

## Ordered Set (Analogous to LinkedHashSet)

Python’s standard library doesn’t have a `LinkedHashSet` equivalent, but `collections.OrderedDict` (since Python 3.7, regular `dict` preserves insertion order) or third-party libraries like `ordered-set` can mimic it.

```python
from collections import OrderedDict

# Using OrderedDict as an ordered set
ordered_set = OrderedDict.fromkeys(["C", "D", "E"])  # Keys maintain insertion order
print(list(ordered_set.keys()))  # ["C", "D", "E"]
```

## Sorted Set (Analogous to TreeSet)

Python lacks a built-in `TreeSet`, but you can use `sortedcontainers.SortedSet` (third-party) or maintain a sorted list with `bisect` for sorted operations.

```python
from sortedcontainers import SortedSet

sorted_set: SortedSet = SortedSet([3, 1, 2])
print(sorted_set)  # SortedSet([1, 2, 3])
sorted_set.add(4)  # O(log n)
print(sorted_set[0])  # 1 (smallest, O(1))
print(sorted_set[-1])  # 4 (largest, O(1))
print(sorted_set.bisect_left(2))  # Index where 2 would be inserted (O(log n))
```

- **Alternative with `bisect`**:
```python
import bisect

sorted_list: List[int] = [1, 2, 3]
bisect.insort(sorted_list, 2)  # Inserts 2 in sorted order (O(n) for insert, O(log n) for search)
print(sorted_list)  # [1, 2, 2, 3]
```

# Python Dictionaries (Analogous to Java Map)

Python’s `dict` is similar to Java’s `HashMap`, with O(1) average-case operations. Since Python 3.7, `dict` preserves insertion order (like `LinkedHashMap`).

```python
# Initialize dictionary
composer_scores: Dict[str, int] = {"Bach": 95, "Beethoven": 90, "Mozart": 85}

# Basic operations
composer_scores["Schubert"] = 88  # Add/Update (O(1) average)
del composer_scores["Bach"]       # Remove (O(1) average)
print(composer_scores.get("Mozart", 0))  # Get with default (O(1) average)
print("Beethoven" in composer_scores)    # Check key (O(1) average)

# Iteration
for key in composer_scores:  # Iterate keys
    print(key)
for value in composer_scores.values():  # Iterate values
    print(value)
for key, value in composer_scores.items():  # Iterate key-value pairs
    print(f"{key}: {value}")

# Dictionary comprehension
squares: Dict[int, int] = {x: x*x for x in range(5)}  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

- **Sorted Dictionary (Analogous to TreeMap)**: Use `sortedcontainers.SortedDict` for sorted keys.

```python
from sortedcontainers import SortedDict

sorted_dict = SortedDict({"C": 1, "A": 2, "B": 3})
print(sorted_dict)  # SortedDict({'A': 2, 'B': 3, 'C': 1})
print(sorted_dict.peekitem(0))  # ('A', 2) - smallest key
```

# Python Deques (Analogous to Java Deque)

Python’s `collections.deque` is a double-ended queue, similar to Java’s `ArrayDeque`.

```python
from collections import deque

dq: deque = deque(["C", "D", "E"])
dq.append("F")        # Add to right (O(1))
dq.appendleft("B")    # Add to left (O(1))
dq.pop()              # Remove from right (O(1))
dq.popleft()          # Remove from left (O(1))
print(dq[0])          # Access first (O(1))
print(dq[-1])         # Access last (O(1))
```

- **Use Case**: Efficient for stacks/queues in problems (e.g., sliding window, BFS).

# Python Heaps (Analogous to Java PriorityQueue)

Python’s `heapq` module provides a min-heap, similar to Java’s `PriorityQueue`.

```python
import heapq

heap: List[int] = [3, 1, 2]
heapq.heapify(heap)  # O(n)
heapq.heappush(heap, 4)  # O(log n)
print(heapq.heappop(heap))  # 1 (O(log n))
print(heap[0])  # Peek smallest (O(1))

# Max-heap (negate values)
max_heap: List[int] = [-x for x in [3, 1, 2]]
heapq.heapify(max_heap)
print(-heapq.heappop(max_heap))  # 3
```

- **Custom Objects**: Use tuples with priority or define `__lt__` for custom types.

# List to Set/Dict and Vice Versa

## List to Set/Dict

```python
notes: List[str] = ["C", "D", "C", "E"]
unique_notes: Set[str] = set(notes)  # {"C", "D", "E"}

# List to dict
pairs: List[tuple[str, int]] = [("Bach", 95), ("Beethoven", 90)]
composer_scores: Dict[str, int] = dict(pairs)  # {"Bach": 95, "Beethoven": 90}
```

## Set/Dict to List

```python
unique_notes: Set[str] = {"C", "D", "E"}
notes_list: List[str] = list(unique_notes)  # ["C", "D", "E"]

composer_scores: Dict[str, int] = {"Bach": 95, "Beethoven": 90}
keys: List[str] = list(composer_scores.keys())  # ["Bach", "Beethoven"]
values: List[int] = list(composer_scores.values())  # [95, 90]
items: List[tuple[str, int]] = list(composer_scores.items())  # [("Bach", 95), ("Beethoven", 90)]
```

# Sorting in Python

Python’s `sorted()` and `list.sort()` are analogous to Java’s `Collections.sort` and `Arrays.sort`.

```python
# Default sorting
notes: List[str] = ["Beethoven", "Bach", "Mozart"]
sorted_notes: List[str] = sorted(notes)  # New sorted list
notes.sort()  # In-place sort
print(sorted_notes)  # ["Bach", "Beethoven", "Mozart"]

# Custom sorting with key function
sorted_by_length: List[str] = sorted(notes, key=len)  # Sort by length
print(sorted_by_length)  # ["Bach", "Mozart", "Beethoven"]

# Reverse sorting
sorted_reverse: List[str] = sorted(notes, reverse=True)  # ["Mozart", "Beethoven", "Bach"]

# Custom key function with multiple criteria
users: List[tuple[int, str]] = [(1, "Bach"), (2, "Beethoven"), (1, "Mozart")]
sorted_users: List[tuple[int, str]] = sorted(users, key=lambda x: (x[0], x[1]))
print(sorted_users)  # [(1, "Bach"), (1, "Mozart"), (2, "Beethoven")]
```

- **Note**: `sorted()` works on any iterable and returns a list; `list.sort()` is in-place and only for lists. For primitive-like lists (e.g., `int`), no boxing is needed, unlike Java.

# Stream-like Operations in Python

Python doesn’t have a direct `Stream` API, but list comprehensions, generators, and `itertools` provide similar functionality for functional-style processing.

## Creating Streams (Iterables)

```python
from typing import List
from itertools import chain

# From list
lst: List[str] = ["Bach", "Beethoven", "Mozart"]
for composer in lst:
    print(composer)

# From dictionary
composer_scores: Dict[str, int] = {"Bach": 95, "Beethoven": 90}
for key, value in composer_scores.items():
    print(f"{key}: {value}")

# From array-like data
nums: List[int] = [1, 2, 3]
for num in nums:
    print(num)

# From arbitrary data
for x in [3, 3, 4, 5]:
    print(x)
```

## Intermediate Operations

### Filter

```python
lst: List[str] = ["EEFG", "GFED", "CCDE", "EDD-"]
filtered: List[str] = [s for s in lst if not s.endswith("-")]  # ["EEFG", "GFED", "CCDE"]
# Or using filter()
filtered_alt: List[str] = list(filter(lambda s: not s.endswith("-"), lst))
```

### Limit/Skip/Distinct

```python
nums: List[int] = [3, 3, 4, 5, 5, 4, 3]
limited: List[int] = nums[:3]  # First 3: [3, 3, 4]
skipped: List[int] = nums[3:]  # Skip first 3: [5, 5, 4, 3]
distinct: List[int] = list(set(nums))  # [3, 4, 5] (order not preserved)

# Using itertools for distinct with order preservation
from itertools import groupby
sorted_nums = sorted(nums)
distinct_ordered: List[int] = [k for k, _ in groupby(sorted_nums)]  # [3, 4, 5]
```

### Concat

```python
from itertools import chain

lst1: List[str] = ["EEFG", "GFED"]
lst2: List[str] = ["GCDEF", "GCC"]
concatenated: List[str] = list(chain(lst1, lst2))  # ["EEFG", "GFED", "GCDEF", "GCC"]
```

### Map

```python
lst: List[str] = ["C-1", "D-2", "E-3"]
values: List[int] = [int(s.split("-")[1]) for s in lst]  # [1, 2, 3]
# Or using map()
values_alt: List[int] = list(map(lambda s: int(s.split("-")[1]), lst))
```

## Terminal Operations

### ForEach

```python
lst: List[str] = ["Bach", "Beethoven", "Mozart"]
for s in lst:
    print(s)
# Or
list(map(print, lst))  # Less common, but functional style
```

### Count

```python
lst: List[str] = ["EEFG", "GFED", "CCDE", "EDD-"]
count: int = sum(1 for s in lst if s.startswith("G") or s.startswith("C"))  # 3
# Or using len
count_alt: int = len([s for s in lst if s.startswith("G") or s.startswith("C")])
```

### Collect to List/Set/Dict

```python
lst: List[str] = ["Bach-17", "Beethoven-19", "Mozart-18"]

# To list
b_list: List[str] = [s for s in lst if s.startswith("B")]  # ["Bach-17", "Beethoven-19"]

# To set
b_set: Set[str] = set(s for s in lst if s.startswith("B"))  # {"Bach-17", "Beethoven-19"}

# To dict
b_map: Dict[str, int] = {s.split("-")[0]: int(s.split("-")[1]) for s in lst if s.startswith("B")}
print(b_map)  # {"Bach": 17, "Beethoven": 19}
```

### Join to String

```python
words: List[str] = ["Hello", "world", "this", "is", "Python"]
result: str = " ".join(words)  # "Hello world this is Python"
```

## Generators for Lazy Evaluation

For memory-efficient stream-like processing, use generators:

```python
def large_range():
    for i in range(1000000):
        yield i

evens = (x for x in large_range() if x % 2 == 0)  # Lazy evaluation
print(next(evens))  # 0 (processes one element at a time)
```

# Strings and Related

- **String**: Immutable (`str`).
- **Mutable Alternatives**: Use `list` of characters or `io.StringIO` for efficient string building.
- **Thread Safety**: Strings and lists are not thread-safe; use `threading.Lock` if needed.

```python
s: str = "Hello"
# s[0] = "h"  # Error: str is immutable
chars: List[str] = list(s)  # ["H", "e", "l", "l", "o"]
chars[0] = "h"
new_s: str = "".join(chars)  # "hello"
```

# 2D Lists (Analogous to Java 2D Arrays)

```python
# Initialize jagged 2D list
arr: List[List[int]] = [[] for _ in range(3)]
arr[0] = [1, 2]
arr[1] = [1, 2, 3]
arr[2] = [1, 2, 3, 4]
print(arr)  # [[1, 2], [1, 2, 3], [1, 2, 3, 4]]

# Fixed-size 2D list
rows, cols = 3, 4
matrix: List[List[int]] = [[0] * cols for _ in range(rows)]
```

- **Note**: Avoid `[[0] * cols] * rows` as it creates shared references to the same inner list.

# Summary for Problem-Solving

- **List**: Use for dynamic arrays, stacks, or queues (O(1) append/pop).
- **Set**: Use for O(1) lookups and uniqueness (e.g., deduplication in problems).
- **Dict**: Use for key-value mappings, frequency counting, or graph adjacency lists.
- **Deque**: Use for sliding windows, BFS, or double-ended operations.
- **Heapq**: Use for priority queues (e.g., top-k problems, Dijkstra’s algorithm).
- **SortedSet/SortedDict**: Use for sorted data (install `sortedcontainers`).
- **Stream-like**: Use list comprehensions or generators for concise, functional-style coding.
- **Sorting**: Use `sorted()` or `list.sort()` with `key` for custom sorting.