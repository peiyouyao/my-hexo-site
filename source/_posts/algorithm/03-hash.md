---
title: Hash
date: 2024-04-19 00:00:00
tags: key-algorithm
categories: algorithm
---

---

# Fundamental

## Java

### Set Interface

```java
boolean add(E e);
boolean	addAll(Collection<? extends E> c);

void clear();

boolean	contains(Object o);
boolean	containsAll(Collection<?> c);

boolean	remove(Object o);
boolean	removeAll(Collection<?> c);
```

### Map Interface

```java
boolean	containsKey(Object key);
boolean	containsValue(Object value);

Set<Map.Entry<K,V>>	entrySet();
Set<K> keySet();
Collection<V> values();

V get(Object key);
V remove(Object key);
```

## C++

### unordered_map

```c++
std::unordered_map<int, std::string> map;

/** 插入
* 返回值是一个 std::pair，其中：
* first 是一个指向插入或已有元素的迭代器。
* second 是一个 bool 值，表示插入是否成功。
*/
auto result = map.insert({1, "one"});
if (result.second) std::cout << "Insert successful!" << std::endl;
else std::cout << "Insert failed, key already exists." << std::endl;

/** 插入 (Operator[])
*/
map[2] = "two"; // 通过 [] 插入键值对 (2, "two")

/** 读取
*/
std::cout << map[1] << std::endl;    // 输出: one
std::cout << map.at(2) << std::endl; // 输出: two
// 读不存在的 key
cout << map[3] << endl; // "" 返回默认值的引用 (可修改), 如果value 是 int, 就是 0
cout << map[3] << endl; // 抛异常

/** 修改
*/
map[1] = "ONE"; // 将键 1 的值更新为 "ONE"
map.at(2) = "TWO";

/** 擦除
* 返回值是 size_t 类型，表示成功删除的元素数量（0 或 1）
*/
size_t count = map.erase(1); // 删除键为 1 的元素
if (count > 0) std::cout << "Erase successful!" << std::endl;
else std::cout << "Key not found." << std::endl;

/** 查找
*/
if (map.find(1) != map.end()) std::cout << "Key 1 found!" << std::endl;
```

### unordered_set

```c++
std::unordered_set<int> set;

/** 插入
* 返回值是一个 std::pair，其中：
* first 是指向插入或已有元素的迭代器。
* second 是一个 bool 值，表示插入是否成功。
*/
set.insert(10); // 插入元素 10
set.insert(20); // 插入元素 20
auto result = set.insert(10);
if (result.second) std::cout << "Insert successful!" << std::endl;
else std::cout << "Insert failed, element already exists." << std::endl;

/** 查找
*/
if (set.find(10) != set.end()) std::cout << "10 exists!" << std::endl;

/** 擦除
* 返回值是 size_t 类型，表示成功删除的元素数量（0 或 1）
*/
size_t count = set.erase(10);
if (count > 0) std::cout << "Erase successful!" << std::endl;
else std::cout << "Element not found." << std::endl;

std::cout << "Size: " << set.size() << std::endl;
```

## Rust

# Examples

## [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)

#### Java

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        int[] rec = new int[26];
        for (char ch : s.toCharArray()) {
            int idx = (int)(ch - 'a');
            rec[idx] ++;
        }
        for (char ch : t.toCharArray()) {
            int idx = (int)(ch - 'a');
            rec[idx] --;
            if (rec[idx] < 0) return false;
        }
        for (int r : rec) if (r != 0) return false;
        return true;
    }
}
```

```java
class Solution {
    public boolean isAnagram(String s, String t) {

        Map<Character, Integer> record = new HashMap<>();
        for (char ch : s.toCharArray()) record.put(ch, record.getOrDefault(ch, 0) + 1);
        for (char ch : t.toCharArray()) {
            if (!record.containsKey(ch)) return false;
            record.put(ch, record.get(ch) - 1);
            if (record.get(ch) == 0) record.remove(ch);
        }
        return record.isEmpty();
    }
}
```

#### C++

```c++
class Solution {
public:
    bool isAnagram(string s, string t) {
        int rec[26] = {0};
        for (int i = 0; i < s.size(); i ++) rec[s[i] - 'a'] ++;
        for (char c : t) rec[c - 'a'] --;
        for (int r : rec) if (r != 0) return false;
        return true;
    }
};
```

#### Rust

```rust
impl Solution {
    pub fn is_anagram(s: String, t: String) -> bool {
        let mut rec: [i32; 26] = [0; 26];
        for &byte in s.as_bytes() {
            rec[byte as usize - b'a' as usize] += 1;
        }
        for byte in t.bytes() {
            rec[byte as usize - b'a' as usize] -= 1;
        }
        for r in rec {
            if r != 0 {
                return false;
            }
        }
        true
    }
}
```

## [349. Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/)

#### Java

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums1) set.add(num);
        List<Integer> ans = new ArrayList<>();
        for (int num : nums2) if (set.remove(num)) ans.add(num); // 避免两次操作 set
        return ans.stream().mapToInt(Integer::intValue).toArray(); // 或 mapToInt(x -> x)
    }
}
```

#### C++

```c++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> set(nums1.begin(), nums1.end());
        vector<int> ans;
        for (int num : nums2) if (set.erase(num)) ans.push_back(num);
        return ans;
    }
};
```

#### Rust

```rust
use std::collections::HashSet;
impl Solution {
    pub fn intersection(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
        let mut ans = Vec::new();
        let mut set: HashSet<i32> = nums1.into_iter().collect();
        for num in nums2 {
            if set.remove(&num) {
                ans.push(num);
            }
        }
        ans
    }
}
```

## [202. Happy Number](https://leetcode.com/problems/happy-number/)

#### Java

```java
class Solution {
    public boolean isHappy(int n) {
        Set<Integer> rec = new HashSet<>();
        while (true) {
            if (n == 1) return true;
            if (rec.contains(n)) return false;
            rec.add(n);
            n = beHappy(n);
        }
    }
    private int beHappy(int n) {
        int happy = 0;
        while (n > 0) {
            happy += (n % 10) * (n % 10);
            n /= 10;
        }
        return happy;
    }
}
```

#### C++

```c++
class Solution {
    int beHappy(int n) {
        int happy = 0;
        while (n) {
            happy += (n % 10) * (n % 10);
            n /= 10;
        }
        return happy;
    }
public:
    bool isHappy(int n) {
        unordered_set<int> set;
        while (1) {
            if (n == 1) return true;
            if (set.find(n) != set.end()) return false;
            set.insert(n);
            n = beHappy(n);
        }
    }
};
```

#### Rust

```rust
use std::collections::HashSet;
impl Solution {
    pub fn is_happy(mut n: i32) -> bool {
        fn be_happy(mut n: i32) -> i32 {
            let mut happy = 0;
            while n > 0 {
                happy += (n % 10) * (n % 10);
                n /= 10;
            }
            happy
        }
        let mut set = HashSet::new();
        loop {
            if n == 1 {
                return true;
            }
            if set.contains(&n) {
                return false;
            }
            set.insert(n);
            n = be_happy(n);
        }
    }
}
```

## [1. Two Sum](https://leetcode.com/problems/two-sum/)

#### Java

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] ans = {-1, -1};
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i ++) {
            if (map.containsKey(target - nums[i])) {
                ans[0] = map.get(target - nums[i]);
                ans[1] = i;
                return ans;
            }
            map.put(nums[i], i);
        }
        return ans;
    }
}
```

#### C++

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        std::unordered_map<int, int> map;
        for (int i = 0; i < nums.size(); i ++) {
            auto iter = map.find(target - nums[i]);
            if (iter != map.end()) return {iter->second, i};
            map.insert(pair<int, int>(nums[i], i));
        }
        return {-1, -1};
    }
};
```

#### Rust

```rust
use::std::collections::HashMap;
impl Solution {
    pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
        let mut map = HashMap::new();
        for (i, &num) in nums.iter().enumerate() {
            let key = target - num;
            if let Some(idx) = map.get(&key) {
                return vec![*idx as i32, i as i32];
            } else {
                map.insert(num, i);
            }
        }
        panic!("not found");
    }
}
```

## [454. 4Sum II](https://leetcode.com/problems/4sum-ii/)

#### Java

```java
class Solution {
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        int len = nums1.length;
        Map<Integer, Integer> record12 = new HashMap<>();
        for (int i = 0; i < len; i ++) {
            for (int j = 0; j < len; j ++) {
                int curSum = nums1[i] + nums2[j];
                record12.put(curSum, record12.getOrDefault(curSum, 0) + 1);  // 相当于记录了前两个数相加的各种情况, 以便查询, 以空间换时间
            }
        }
        int cnt = 0;
        for (int k = 0; k < len; k ++) {
            for (int l = 0; l < len; l ++) {
                int curSum = nums3[k] + nums4[l];
                cnt += record12.getOrDefault(-curSum, 0);
            }
        }
        return cnt;

    }
}
```

#### C++

```c++
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        std::unordered_map<int, int> map;
        for (int n1 : nums1) for (int n2 : nums2) map[n1 + n2] ++;
        int ans = 0;
        for (int n3 : nums3) for (int n4 : nums4) ans += map[- n3 - n4];
        return ans;
    }
};
```

#### Rust

```rust
use::std::collections::HashMap;
impl Solution {
    pub fn four_sum_count(nums1: Vec<i32>, nums2: Vec<i32>, nums3: Vec<i32>, nums4: Vec<i32>) -> i32 {
        let mut ans = 0;
        let mut map = HashMap::new();
        for n1 in nums1.iter() {
            for n2 in nums2.iter() {
                *map.entry(n1 + n2).or_insert(0) += 1; // n1, n2: &i32, 运算时被自动解引用了
            }
        }
        for n3 in nums3.iter() {
            for n4 in nums4.iter() {
                if let Some(cnt) = map.get(&(- n3 - n4)) {
                    ans += cnt;
                }
            }
        }
        ans
    }
}
```

## [383. Ransom Note](https://leetcode.com/problems/ransom-note/)

#### Java

```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        Map<Character, Integer> recordM = new HashMap<>();
        for (char ch : magazine.toCharArray()) {
            recordM.put(ch, recordM.getOrDefault(ch, 0) + 1);
        }
        for (char ch : ransomNote.toCharArray()) {
            if ( !recordM.containsKey(ch) ) return false;
            else {
                recordM.put(ch, recordM.get(ch) - 1);
                if (recordM.get(ch) == 0) recordM.remove(ch);  // ch 用完了
            }
        }
        return true;
    }
}
```

#### C++

```c++
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        if (ransomNote.size() > magazine.size()) return false;
        int rec[26] = {0};
        for (auto& c : magazine) rec[c - 'a'] ++;
        for (auto& c : ransomNote) {
            rec[c - 'a'] --;
            if (rec[c - 'a'] < 0) return false;
        }
        return true;
    }
};
```

#### Rust

```rust
impl Solution {
    pub fn can_construct(ransom_note: String, magazine: String) -> bool {
        if ransom_note.len() > magazine.len() {
            return false;
        }
        let mut rec = [0; 26];
        for c in magazine.bytes() {
            rec[(c - b'a') as usize] += 1;
        }
        for c in ransom_note.bytes() {
            rec[(c - b'a') as usize] -= 1;
            if rec[(c - b'a') as usize] < 0 {
                return false;
            }
        }
        true
    }
}
```

## [15. 3Sum](https://leetcode.com/problems/3sum/)

#### Java

以下代码为了对 num2 去重, 使用了 map, 效率其实很低 (360+ ms)

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        Arrays.sort(nums);
        if (nums[0] > 0 || nums[nums.length - 1] < 0) return res;
        Map<Integer, Boolean> record2 = new HashMap<>();  // record whether the second num has been put the res, which can kill repeating result
        for (int i = 0; i < nums.length - 2; i ++) {  // 对 num1 去重
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            record2.clear();
            for (int k = i + 2; k < nums.length; k ++) {
                int num2 = nums[k - 1];  // 先添加第二个数字
                if (!record2.containsKey(num2)) record2.put(num2, false);
                if (record2.containsKey(-nums[i]-nums[k]) && record2.get(-nums[i]-nums[k]) == false) {
                    record2.put(-nums[i]-nums[k], true);  // 对 num2 去重
                    List<Integer> tmp = new ArrayList<>();
                    Collections.addAll(tmp, nums[i], -nums[i]-nums[k], nums[k]);
                    res.add(tmp);
                }

            }
        }
        return res;
    }
}
```

#### C++

使用集合, 同样也能完成去重, 但效率也不高

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        // 找出a + b + c = 0
        // a = nums[i], b = nums[j], c = -(a + b)
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] > 0) break; // 排序之后如果第一个元素已经大于零，那么不可能凑成三元组
            if (i > 0 && nums[i] == nums[i - 1]) continue; //三元组元素a去重
            unordered_set<int> set; // 这里如果在循环外部搞一个 set 每次在这里 clear() 效率低下, 因为c++中 hash.clear() 时间复杂度是 O(n)
            for (int j = i + 1; j < nums.size(); j++) {
                if (j > i + 2
                    && nums[j] == nums[j-1]
                    && nums[j-1] == nums[j-2]) continue; // 三元组元素b去重
                int c = 0 - (nums[i] + nums[j]);
                if (set.find(c) != set.end()) {
                    result.push_back({nums[i], nums[j], c});
                    set.erase(c);// 三元组元素c去重
                } else set.insert(nums[j]);
                /*
                if (set.erase(c)) result.push_back({nums[i], nums[j], c}); // erase返回是擦除个数
                else set.insert(nums[j]);
                */
            }
        }
        return result;
    }
};
```

## [18. 4Sum](https://leetcode.com/problems/4sum/)

#### Java

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Arrays.sort(nums);
        List<List<Integer>> ans = new ArrayList<>();
        Set<Integer> set = new HashSet<>();
        for (int i = 0; i < nums.length - 3; i ++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            for (int j = i + 1; j < nums.length - 2; j ++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                set.clear();
                for (int k = j + 1; k < nums.length; k ++) {
                    if (k > j + 2
                        && nums[k] == nums[k - 1]
                        && nums[k - 1] == nums[k - 2]
                    ) continue;
                    int n3 = target - nums[i] - nums[j] - nums[k];
                    if (set.remove(n3)) ans.add(List.of(nums[i], nums[j], n3, nums[k])); // List.of Java 9+ 新增的静态方法; 巧用 remove() 的返回值是 boolean 减少hash次数
                    else set.add(nums[k]);
                }
            }
        }
        return ans;
    }
}
```

# Extra

## [205. Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/)

使用 Map 记录映射关系

#### Java

```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        if (s.length() != t.length()) return false;
        Map<Character, Character> map = new HashMap<>();
        for (int i = 0; i < s.length(); i ++) {
            char sch = s.charAt(i), tch = t.charAt(i);
            if (map.containsKey(sch) && map.get(sch) != tch) return false;
            else map.put(sch, tch); 
        }
        map.clear();
        for (int i = 0; i < t.length(); i ++) {
            char sch = s.charAt(i), tch = t.charAt(i);
            if (map.containsKey(tch) && map.get(tch) != sch) return false;
            else map.put(tch, sch);
        }
        return true;
    }
}
```

#### C++

```c++
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        if (s.size() != t.size()) return false;
        int len = s.size();
        std::unordered_map<char, char> map;
        for (int i = 0; i < len; i ++) {
            char cs = s[i], ct = t[i];
            if (map.find(cs) != map.end() && map[cs] != ct) return false;
            map[cs] = ct;
        }
        map.clear();
        for (int i = 0; i < len; i ++) {
            char ct = t[i], cs = s[i];
            if (map.find(ct) != map.end() && map[ct] != cs) return false;
            map[ct] = cs;
        }
        return true;
    }
};
```

## [1002. Find Common Characters](https://leetcode.com/problems/find-common-characters/)

#### Java

```java
class Solution {
    public List<String> commonChars(String[] words) {
        List<String> ans = new ArrayList<>();
        int[] rec = new int[26];
        Arrays.fill(rec, Integer.MAX_VALUE);
        int[] wordRec = new int[26];
        for (String word : words) {
            Arrays.fill(wordRec, 0);
            for (char c : word.toCharArray()) wordRec[char2int(c)] ++;
            for (int i = 0; i < 26; i ++) rec[i] = Math.min(rec[i], wordRec[i]);
        }
        for (int i = 0; i < 26; i ++) {
            while (rec[i] > 0) {
                ans.add(String.valueOf((char)(i + 'a')));
                rec[i] --;
            }
        }
        return ans;
    }
    private int char2int(char c) { return (int)(c - 'a'); }
}
```

#### C++

```c++
class Solution {
public:
    vector<string> commonChars(vector<string>& words) {
        vector<string> ans;
        vector<int> rec(26, INT_MAX);
        vector<int> wordRec(26, 0);
        for (string& word : words) {
            fill(wordRec.begin(), wordRec.end(), 0);
            for (char& c : word) wordRec[c - 'a']++;
            for (int i = 0; i < 26; i++) rec[i] = min(rec[i], wordRec[i]);
        }
        for (int i = 0; i < 26; i++) {
            while (rec[i] > 0) {
                ans.push_back(string(1, i + 'a'));
                rec[i]--;
            }
        }
        return ans;
    }
};
```

