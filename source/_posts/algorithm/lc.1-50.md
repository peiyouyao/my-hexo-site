---
title: LeetCode 1-50
date: 1989-06-04 00:00:00
tags: leetcode
categories: algorithm
---

---

## [1. Two Sum](https://leetcode.com/problems/two-sum/) [Hash]



## [2. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)

#### Java

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode ans = new ListNode();
        ListNode p = ans;
        int carry = 0;
        while (l1 != null || l2 != null) {
            p.next = new ListNode();
            p = p.next;
            int sum = (l1 == null ? 0 : l1.val) + (l2 == null ? 0 : l2.val) + carry;
            p.val = sum % 10;
            carry = sum / 10;
            if (l1 != null) l1 = l1.next; 
            if (l2 != null) l2 = l2.next;
        }
        if (carry != 0) {
            p.next = new ListNode(carry);
        }
        return ans.next;
    }
}
```



#### Go

```go
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
    var l *ListNode = new(ListNode)
    p := l
    carry := 0
    for l1 != nil || l2 != nil {
        v1, v2 := 0, 0
        if l1 != nil {
            v1 = l1.Val
            l1 = l1.Next
        }
        if l2 != nil {
            v2 = l2.Val
            l2 = l2.Next
        }
        sum := v1 + v2 + carry
        carry, sum = sum / 10, sum % 10
        p.Next = new(ListNode)
        p.Next.Val = sum
        p = p.Next
    }
    if carry != 0 {
        p.Next = new(ListNode)
        p.Next.Val = carry
    }
    return l.Next
}
```



#### Rust

```rust
impl Solution {
    pub fn add_two_numbers(
        mut l1: Option<Box<ListNode>>,
        mut l2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        let mut carry = 0;
        let mut ans = Some(Box::new(ListNode::new(0)));
        let mut p = &mut ans;
        while l1.as_ref().is_some() || l2.as_ref().is_some() {
            p.as_mut().unwrap().next = Some(Box::new(ListNode::new(0)));
            p = &mut p.as_mut().unwrap().next;
            let sum = if l1.as_ref().is_none() { 0 } else { l1.as_ref().unwrap().val } 
            + if l2.as_ref().is_none() { 0 } else { l2.as_ref().unwrap().val } + carry;
            p.as_mut().unwrap().val = sum % 10;
            carry = sum / 10;
            if l1.as_ref().is_some() { l1 = l1.unwrap().next; }
            if l2.as_ref().is_some() { l2 = l2.unwrap().next; }
        }
        if carry != 0 {
            p.as_mut().unwrap().next = Some(Box::new(ListNode::new(carry)));
        }
        ans.unwrap().next
    }
}
```



## [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

*Sliding Window*

#### Java

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Set<Character> rec = new HashSet<>();
        int ans = 0;
        int l = 0;
        for (int r = 0; r < s.length(); r ++) {
            char ch = s.charAt(r);
            while (rec.contains(ch)) {
                rec.remove(s.charAt(l));
                l ++;
            }
            ans = Math.max(ans, r-l+1);
            rec.add(ch);
        }
        return ans;
    }
}
```



#### Go

```go
func lengthOfLongestSubstring(s string) int {
    rec := map[byte]bool{}
    ans := 0
    for i, j := 0, 0; i < len(s); i++ {
        for rec[s[i]] {
            delete(rec, s[j])
            j++
        }
        rec[s[i]] = true
        if ans < i-j+1 {
            ans = i - j + 1
        }
    }
    return ans
}
```



#### Rust

以下写法性能高 (4 ms)

```rust
use std::collections::HashMap;
impl Solution {
    pub fn length_of_longest_substring(s: String) -> i32 {
        let mut map: HashMap<char, usize> = HashMap::new(); // 记录出现位置
        let mut l: usize = 0;
        let mut ans: usize = 0;
        for (r, ch) in s.chars().enumerate() {
            if let Some(&prev) = map.get(&ch) { l = l.max(prev + 1); } // 如果字符已经出现过，更新左指针位置
            // 更新字符的最新位置
            map.insert(ch, r);
            ans = ans.max(r - l + 1);
        }
        ans as i32
    }
}
```

以下是低效率写法 (154 ms)

```rust
use std::collections::{HashMap, HashSet};
impl Solution {
    pub fn length_of_longest_substring(s: String) -> i32 {
        let mut rec = HashSet::new();
        let mut l: usize = 0;
        let mut ans = 0;
        for (r, ch) in s.chars().enumerate() {
            while l < s.len() && rec.contains(&ch) {
                rec.remove(&s.chars().nth(l).unwrap()); // s.chars().nth(l) 是线性搜索, 性能极低
                l += 1;
            }
            rec.insert(ch);
            ans = ans.max(r - l + 1);
        }
        ans as i32
    }
}
```



## 4



## [5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/) [DP-Sub]



## [6. Zigzag Conversion](https://leetcode.com/problems/zigzag-conversion/)

#### Java

本质上是一个数学问题: 按层序分组

```java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows == 1) return s;
        int len = s.length();
        int n = numRows * 2 - 2;
        StringBuilder res = new StringBuilder();
        for (int lvl = 0; lvl < numRows; lvl ++) {  // 考查第 lvl 层
            if (lvl == 0 || lvl == numRows - 1) {
                int d = lvl;  // 偏移量
                for (int i = d; i < len; i += n) res.append(s.charAt(i));
            } else {
                int d1 = lvl, d2 = n - lvl;
                int i = d1, j = d2;
                while (i < len) {
                    res.append(s.charAt(i));
                    if (j < len) res.append(s.charAt(j));
                    i += n; j += n;
                }
            }
        }
        return res.toString();
    }
}
```



```java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows <= 1) return s;
        StringBuilder[] lvls = new StringBuilder[numRows];
        for (int i = 0; i < numRows; i ++) lvls[i] = new StringBuilder();
        int idx = 0, d = -1;
        for (char c : s.toCharArray()) {
            if (idx == 0 || idx == numRows - 1) d *= -1;
            lvls[idx].append(c);
            idx += d;
        }
        return Arrays.stream(lvls).map(lvl -> new String(lvl)).collect(Collectors.joining(""));
    }
}
```



#### Go

使用偏移量, 把字符加入到正确的层中

```go
func convert(s string, numRows int) string {
    if numRows == 1 {
        return s
    }
    lvls := make([]string, numRows)
    idx, d := 0, -1
    for _, c := range s {
        lvls[idx] += string(c)
        if idx == 0 || idx == numRows - 1 {
            d *= -1
        }
        idx += d
    }
    return strings.Join(lvls, "")
}
```



## [7. Reverse Integer](https://leetcode.com/problems/reverse-integer/)

#### Java

```java
class Solution {
    public int reverse(int x) {
        boolean isNegative = x < 0;
        if (isNegative) x *= -1;
        String xStr = String.valueOf(x);
        xStr = new StringBuilder(xStr).reverse().toString();  // 翻转字符串的写法
        try {
            x = Integer.valueOf(xStr);  // x = 1,534,236,469 其转化值越界了
        } catch (Exception e) {
            return 0;
        }
        return (isNegative) ? -x : x;
    }
}
```

```java
class Solution {
    public int reverse(int x) {
        long ans = 0;
        while (x != 0) {
            ans *= 10;
            ans = ans + x % 10;
            x /= 10;
            if (ans > Integer.MAX_VALUE || ans < Integer.MIN_VALUE) return 0;
        }
        return (int)ans;
    }
}
```



#### Go

```go
func reverse(x int) (rev int) {
    for x != 0 {
        if rev < math.MinInt32 / 10 || rev > math.MaxInt32 / 10 {
            return 0
        }
        digit := x % 10
        x /= 10
        rev = rev * 10 + digit
    }
    return
}
```



## [8. String to Integer (atoi)](https://leetcode.com/problems/string-to-integer-atoi/)

这道题并不难, 主要是要搞清楚逻辑, 规划好代码顺序

#### Java

```java
class Solution {
    public int myAtoi(String s) {
        int i = 0;
        while (i < s.length() && s.charAt(i) == ' ') i ++;
        boolean negative = false;
        if (i < s.length() && s.charAt(i) == '+') i ++;
        else if (i < s.length() && s.charAt(i) == '-') {
            i ++;
            negative = true;
        }
        long ans = 0;
        while (i < s.length() && s.charAt(i) >= '0' && s.charAt(i) <= '9') {
            ans *= 10;
            int val = s.charAt(i) - '0';
            ans = ans + (negative ? -val : val);
            i ++;
            if (ans >= Integer.MAX_VALUE) {
                ans = Integer.MAX_VALUE;
                break;
            } else if (ans <= Integer.MIN_VALUE) {
                ans = Integer.MIN_VALUE;
                break;
            }
        }
        return (int)ans;
    }
}
```



#### Go

```go
func myAtoi(s string) int {
    idx := 0
    for ; idx < len(s); idx ++ {
        if s[idx] != ' ' {
            break
        }
    }
    if idx == len(s) {
        return 0
    }

    sig := 1 // int64
    if s[idx] == '+' {
        idx ++
    } else if s[idx] == '-' {
        sig = -1
        idx ++
    }

    ans := 0
    for ; idx < len(s); idx ++ {
        if s[idx] < '0' || s[idx] > '9' {
            return ans
        }
        ans = ans * 10 + sig * int(s[idx] - '0')
        if ans < math.MinInt32 {
            return math.MinInt32
        }

        if ans > math.MaxInt32 {
            return math.MaxInt32
        }
    }
    return ans
}
```



## [9. Palindrome Number](https://leetcode.com/problems/palindrome-number/)

#### Java

```java
class Solution {
    public boolean isPalindrome(int x) {
        String xStr = String.valueOf(x);
        return (new StringBuilder(xStr).reverse().toString().equals(xStr));
    }
}
```



#### Go

```go
func isPalindrome(x int) bool {
    rev := 0;
    for num := x; num > 0; num /= 10 {
        rev = rev * 10 + num % 10
    }
    return rev == x
}
```



#### Rust

```rust
impl Solution {
    pub fn is_palindrome(x: i32) -> bool {
        x.to_string() == x.to_string().chars().rev().collect::<String>()
    }
}
```



## 10



## [11. Container With Most Water](https://leetcode.com/problems/container-with-most-water/)

*Greedy*

收缩边界时, 总是移动高度较小的一侧, 这样可以使得下一步中的 $\min(h[l], h[r])$ 尽可能大

与 [84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/) 相对比, 两者思路不同, 84 要使用单调栈

#### Java

```java
class Solution {
    public int maxArea(int[] height) {
        int l = 0, r = height.length - 1;
        int res = 0;
        while (l < r) {
            int width = r - l;
            int h = Math.min(height[l], height[r]);
            res = Math.max(res, h * width);
            if (height[l] <= height[r]) l ++;
            else r --;
        }
        return res;
    }
}
```



#### Go

```go
func maxArea(height []int) int {
    min := func(a, b int) int {
        if a < b {
            return a
        }
        return b
    }

    max := func(a, b int) int {
        if a > b {
            return a
        }
        return b
    }
    ans := 0
    i, j := 0, len(height) - 1
    for i < j {
        ans = max(ans, min(height[i], height[j]) * (j - i))
        if height[i] > height[j] {
            j --
        } else {
            i ++
        }
    }
    return ans
}
```



## [12. Integer to Roman](https://leetcode.com/problems/integer-to-roman/)

#### Java

```java
class Solution {
    final static int[] val =    {1000,900, 500,400, 100,90,  50, 40,  10, 9,   5,  4,   1  };
    final static String[] rom = {"M", "CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};

    public String intToRoman(int num) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < val.length; i ++) {
            while (num >= val[i]) {  // 这里注意是 >= 
                sb.append(rom[i]);
                num -= val[i];
            }
        }
        return sb.toString();
    }
}
```



#### Go

```go
func intToRoman(num int) string {
    tab := [][]string {
        {"", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"},
        {"", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"},
        {"", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"},
        {"", "M", "MM", "MMM", "",   "",  "",   "",    "",     ""  },
    }

    roman := make([]string, 3)
    for i := 0; num > 0; i ++ {
        digit := num % 10
        num /= 10
        roman = append(roman, tab[i][digit])
    }
    i, j := 0, len(roman) - 1
    for i < j {
        t := roman[i]
        roman[i] = roman[j]
        roman[j] = t
        i ++
        j --
    }
    return strings.Join(roman, "")
}
```



## [13. Roman to Integer](https://leetcode.com/problems/roman-to-integer/)

#### Java

```java
class Solution {
    public int romanToInt(String s) {
        Map<Character, Integer> map = new HashMap<>();
        map.put('I', 1);
        map.put('V', 5);
        map.put('X', 10);
        map.put('L', 50);
        map.put('C', 100);
        map.put('D', 500);
        map.put('M', 1000);

        int res = map.get(s.charAt(s.length() - 1));
        for (int i = s.length() - 2; i > -1; i --) {
            char cur = s.charAt(i);
            char pre = s.charAt(i + 1);
            int curVal = map.get(cur);
            int preVal = map.get(pre);
            if (curVal < preVal) {
                res -= curVal;
            } else {
                res += curVal;
            }
        }
        return res;
    }
}
```



#### Go

```go
func romanToInt(s string) int {
    tab := map[byte]int {
        'I': 1, // 0
        'V': 5, 
        'X': 10, // 1
        'L': 50, 
        'C': 100, // 2
        'D': 500, 
        'M': 1000, 
    }
    nega := [3]bool{}; // 记录遇到 I X C 是加还是减
    ans := 0
    for i := len(s) - 1; i > -1; i -- {
        c := s[i]
        if c == 'V' || c == 'X' {
            nega[0] = true
        }

        if c == 'L' || c == 'C' {
            nega[1] = true
        }

        if c == 'D' || c == 'M' {
            nega[2] = true
        }

        if c == 'I' && nega[0] || c == 'X' && nega[1] || c == 'C' && nega[2] {
            ans -= tab[c]
        } else {
            ans += tab[c]
        }
    }
    return ans
}
```



#### Rust

```rust
impl Solution {
    pub fn roman_to_int(s: String) -> i32 {
        let mut val: i32 = 0;
        let mut appear: [bool; 7] = [false; 7];

        for ch in s.chars().rev() {
            match ch {
                'I' => {
                    if appear[1] || appear[2] {
                        val -= 1;
                    } else {
                        val += 1;
                    }
                    appear[0] = true;
                }
                'V' => {
                    val += 5;
                    appear[1] = true;
                }
                'X' => {
                    if appear[3] || appear[4] {
                        val -= 10;
                    } else {
                        val += 10;
                    }
                    appear[2] = true;
                }
                'L' => {
                    val += 50;
                    appear[3] = true;
                }
                'C' => {
                    if appear[5] || appear[6] {
                        val -= 100;
                    } else {
                        val += 100;
                    }
                    appear[4] = true;
                }
                'D' => {
                    val += 500;
                    appear[5] = true;
                }
                'M' => {
                    val += 1000;
                    appear[6] = true;
                }
                _ => {}
            }
        }
        val
    }
}
```



## [14. Longest Common Prefix](https://leetcode.com/problems/longest-common-prefix/)

#### Java

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        int idx = 0;
        while (true) {
            Character ch = null;
            for (String s: strs) {
                if (idx == s.length()) return strs[0].substring(0, idx);
                if (ch == null) ch = s.charAt(idx);
                char cur = s.charAt(idx);
                if (cur != ch) return strs[0].substring(0, idx);
            }
            idx ++;
        }
    }
}
```



#### Go

```go
func longestCommonPrefix(strs []string) string {
    commonLen := 0
    for {
        var c byte = '*'
        ok := true
        for _, s := range strs {
            if commonLen == len(s) {
                ok = false
                break
            }
            if c == '*' {
                c = s[commonLen]
            }
            if  s[commonLen] != c {
                ok = false
                break
            }
        }
        if ok {
            commonLen ++
        } else {
            break
        }
    }
    return strs[0][:commonLen]
}
```



#### Rust

```rust
impl Solution {
    pub fn longest_common_prefix(strs: Vec<String>) -> String {
        let mut idx = 0;
        loop {
            let mut ch: Option<char> = None;
            for s in &strs {
                if idx == s.len() {
                    return strs[0][0..idx].to_string();
                }
                let cur: Option<char> = s.chars().nth(idx);
                if ch.is_none() {
                    ch = cur;
                }
                if cur != ch {
                    return strs[0][0..idx].to_string();
                }
            }
            idx += 1;
        }
    }
}
```



## [15. 3Sum](https://leetcode.com/problems/3sum/) [Hash & Double Pointers]



## [16. 3Sum Closest](https://leetcode.com/problems/3sum-closest/)

#### Java

双指针, 时间复杂度: $O(n^2)$

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int diff = Integer.MAX_VALUE;
        int res = 0;
        for (int i = 0; i <= nums.length - 3; i ++) {
            int l = i + 1, r = nums.length - 1;
            while (l < r) {  // 使 l, r 在中间位置相遇
                int curSum = nums[i] + nums[l] + nums[r];
                int curDiff = Math.abs(curSum - target);
                if (curDiff < diff) {  // 每一次都要判读当前是否更优
                    diff = curDiff;
                    res = curSum;
                }
                if (curSum > target) r --;
                else if (curSum < target) l ++;
                else return target;
            }
        }
        return res;
    }
}
```



#### Go

```go
func threeSumClosest(nums []int, target int) (ans int) {
    sort.Ints(nums)
    var (
        l = len(nums)
        minDist = math.MaxInt32
    )
    abs := func(n int) int {
        if n < 0 {
            return -n
        }
        return n
    }
    for i := 0; i <= l - 3; i ++ {
        j := i + 1
        k := l - 1
        for j < k {
            sum := nums[i] + nums[j] + nums[k]
            dist := abs(target - sum)
            if dist < minDist {
                minDist = dist
                ans = sum
            }

            if sum < target {
                j ++
            } else if sum > target {
                k --
            } else {
                return
            }
        }
    }
    return
}
```



## [17. Letter Combinations of a Phone Number](https://LeetCode.com/problems/letter-combinations-of-a-phone-number/) [Backtrack]



## [18. 4Sum](https://leetcode.com/problems/4sum/) [Hash & Double Pointers]



## [19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) [List]



## [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/) [Stack-Queue]



## [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

#### Java

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode res = new ListNode();
        ListNode p = res;
        while (list1 != null && list2 != null) {
            p.next = new ListNode(); // 如果没有要求保留 head1 head2, 这里可以不需要 new ListNode
            p = p.next;
            if (list1.val < list2.val) {
                p.val = list1.val;
                list1 = list1.next;
            } else {
                p.val = list2.val;
                list2 = list2.next;
            }
        }
        if (list1 != null) p.next = list1;
        else p.next = list2;
        return res.next;
    }
}
```



#### Rust

```rust
// Definition for singly-linked list.
#[derive(PartialEq, Eq, Clone, Debug)]
pub struct ListNode {
    pub val: i32,
    pub next: Option<Box<ListNode>>,
}

impl ListNode {
    #[inline]
    fn new(val: i32) -> Self {
        ListNode { next: None, val }
    }
}

impl Solution {
    pub fn merge_two_lists(
        mut list1: Option<Box<ListNode>>,
        mut list2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        let mut dummy: Option<Box<ListNode>> = Some(Box::new(ListNode::new(0)));
        let mut p: &mut Option<Box<ListNode>> = &mut dummy;

        while list1.is_some() && list2.is_some() {
            let val1: i32 = list1.as_ref().unwrap().val;
            let val2: i32 = list2.as_ref().unwrap().val;
            p.as_mut().unwrap().next = Some(Box::new(ListNode::new(0)));
            p = &mut p.as_mut().unwrap().next;
            if val1 <= val2 {
                p.as_mut().unwrap().val = val1;
                list1 = list1.unwrap().next;
            } else {
                p.as_mut().unwrap().val = val2;
                list2 = list2.unwrap().next;
            }
        }
        if list1.is_some() {
            p.as_mut().unwrap().next = list1;
        } else {
            p.as_mut().unwrap().next = list2;
        }
        dummy.unwrap().next
    }
}
```



## [22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)

#### Java

这种方法需要 Set 去重

```java
class Solution {
    private Set<String> res = new HashSet<>();

    private void backtrack(char[] path, int th, int n) { // th 代表第 th 个括号 (th = 0, 1, ... n - 1)
        if (th == n) {  // 当 th == n 说明 n 个括号已经被打完, 收集结果
            res.add(new String(path));
            return;
        }

        for (int i = th; i < 2 * th + 1; i ++) {  // 关键: th 个左括号只能被安放在 th, th + 1, ..., 2 * th 位置处
            if (path[i] == '(') continue;
            path[i] = '(';
            backtrack(path, th + 1, n);
            path[i] = ')';
        }
    }

    public List<String> generateParenthesis(int n) {
        char[] path = new char[n * 2];
        Arrays.fill(path, ')');
        backtrack(path, 0, n);
        return new LinkedList<>(res);
    }
}
```

```
())))) - [th = 0: 第 0 个"("只能放在 0* 处]
	|
	(()))) - [th = 1: 第 1 个"("可以放在 1* or 2]
	|	|
	|	((())) - [th = 2: 第 2 个"("可以放在 2* or 3 or 4] add
	|	|
	|	(()()) - [th = 2: 第 2 个"("可以放在 2 or 3* or 4] add
	|	|
	|	(())() - [th = 2: 第 2 个"("可以放在 2 or 3 or 4*] add
	|
	()())) - [th = 1: 第 1 个"("可以放在 1 or 2*]
		|
		()())) - [th = 2: 第 2 个"("可以放在 2* or 3 or 4] continue
		|
		()(()) - [th = 2: 第 2 个"("可以放在 2 or 3* or 4] add
		|
		()()() - [th = 2: 第 2 个"("可以放在 2 or 3 or 4*] add
		

```



更加巧妙, 不需要最后去重

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new LinkedList<>();
        backtrack(res, 0, 0, "", n);
        return res;
    }

    private void backtrack(List<String> res, int l, int r, String s, int n) {  // l: (个数, r: )个数
        if (s.length() == n * 2) {
            res.add(s);
            return;
        }
        if (l < n) backtrack(res, l + 1, r, s + "(", n);  // 当 "( 个数" 不满 n 时 l + 1
        if (r < l) backtrack(res, l, r + 1, s + ")", n);  // 当 ") 个数" 小于 ( 个数时 r + 1
    }
}
```

```
n = 3 [l, r, s]

[0, 0, null ]
	|
	[1, 0, ( ]
		|
		[2, 0, (( ]
		|	|
		|	[3, 0, ((( ] -> ((()))
		|	|
		|	[2, 1, (() ]
		|		|
		|		[2, 2, (()) ] -> (())()
		|		|
		|		[3, 1, (()( ] -> (()())
		|
		[1, 1, () ]
			|
			[2, 1, ()( ]
				|
				[3, 1, ()(( ] -> ()(())
				|
				[2, 2, ()() ] -> ()()()
```

另一种写法

```java
class Solution {
    private StringBuilder path = new StringBuilder();
    private List<String> ans = new ArrayList<>();

    private void gen(int n, int leftCnt, int rightCnt) {
        if (rightCnt == n) {  // ')' 填满了 就收集结果
            ans.add(path.toString());
            return;
        } else if (leftCnt == n) {  // '(' 填满了, 之后只能填 ')'
            path.append(')');
            gen(n, leftCnt, rightCnt + 1);
            path.deleteCharAt(path.length()-1);
        } else if (leftCnt == rightCnt) {  // '(' 与 ')' 数量相同, 之后只能填 '('
            path.append('(');
            gen(n, leftCnt + 1, rightCnt);
            path.deleteCharAt(path.length()-1);
        } else if (leftCnt > rightCnt) {  // '(' 数量大于 ')' 数量, 则之后可以填 '(' 也可以填 ')'
            path.append('(');
            gen(n, leftCnt + 1, rightCnt);
            path.deleteCharAt(path.length()-1);

            path.append(')');
            gen(n, leftCnt, rightCnt + 1);
            path.deleteCharAt(path.length()-1);
        }
    }

    public List<String> generateParenthesis(int n) {
        gen(n, 0 , 0);
        return ans;
    }
}
```



#### Go

```go
func generateParenthesis(n int) []string {
    paths := []string{}
    path := ""
    var backtrack func(l, r int)
    backtrack = func(l, r int) {
        if l == n && r == n {
            paths = append(paths, path)
            return
        }

        if l < n {
            path += "("
            backtrack(l + 1, r)
            path = path[:len(path) - 1]
        }
        
        if l > r {
            path += ")"
            backtrack(l, r + 1)
            path = path[:len(path) - 1]
        }
    }
    backtrack(0, 0)
    return paths
}
```



## [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

#### Java

*暴力法*

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode ans = new ListNode(0);
        ListNode p = ans;
        while (true) {
            int minIdx = -1;
            int minVal = Integer.MAX_VALUE;
            for (int i = 0; i < lists.length; i ++) {
                ListNode list = lists[i];
                if (list == null) continue;
                if (minIdx == -1 || list.val < minVal) {
                    minVal = list.val;
                    minIdx = i;
                }
            }
            if (minIdx == -1) return ans.next;
            ListNode list = lists[minIdx];
            ListNode nxt = list.next;
            list.next = null;
            p.next = list;
            lists[minIdx] = nxt;
            p = p.next;
        }
    }
}
```



*Heap Sort*

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        Queue<ListNode> heap = new PriorityQueue<>((h1, h2) -> h1.val - h2.val);
        for (ListNode list : lists) if (list != null) heap.offer(list);
        ListNode ans = new ListNode();
        ListNode p = ans;
        while (!heap.isEmpty()) {
            ListNode cur = heap.poll();
            p.next = cur;
            p = p.next;
            if (cur.next != null) heap.offer(cur.next);
        }
        return ans.next;
    }
}
```



#### C++

```c++
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        auto cmp = [](const ListNode* a, const ListNode* b) { return a->val > b->val; };
        priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> pq;
        for (auto list : lists) if (list) pq.push(list);

        ListNode ans{}; // ans 节点分配在栈上
        ListNode* p = &ans;
        while (!pq.empty()) {
            auto cur = pq.top(); pq.pop();
            p->next = cur;
            p = p->next;
            if (cur->next) pq.push(cur->next);
        }
        return ans.next; // ans 节点是分配在栈上的, 无需释放内存. 同时需要使用.取得next
    }
};
```



#### Go

*Divide-and-Conquer*

```go
func mergeKLists(lists []*ListNode) *ListNode {
    return mergeHelper(lists, 0, len(lists) - 1)
}

func mergeHelper(lists []*ListNode, l, r int) *ListNode {
    if l == r {
        return lists[l]
    }
    if l > r {
        return nil
    }
    m := (l + r) >> 1
    return mergeTwoLists(mergeHelper(lists, l, m), mergeHelper(lists, m + 1, r))
}

func mergeTwoLists(head1 *ListNode, head2 *ListNode) *ListNode {
    head := new(ListNode)
    p := head
    for head1 != nil && head2 != nil {
        if head1.Val < head2.Val {
            p.Next = head1
            head1 = head1.Next
        } else {
            p.Next = head2
            head2 = head2.Next
        }
        p = p.Next
    }
    if head1 != nil {
        p.Next = head1
    } else {
        p.Next = head2
    }
    return head.Next
}
```



## [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/) [List]



## [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/) [List]



## [26. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/) [Array]



## [27. Remove Element](https://leetcode.com/problems/remove-element/) [Array]



## [28. Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/) [String]



## [29. Divide Two Integers](https://leetcode.com/problems/divide-two-integers/)

```
dividend = quotient * divisor + remainder
dd = q * ds + r
```



```java
class Solution {
    public int divide(int dividend, int divisor) {
        boolean negative = (dividend < 0) ^ (divisor < 0);
        long dd = Math.abs((long) dividend), ds = Math.abs((long) divisor);
        long q = 0;
        while (dd >= ds) {  // 需要优化, 不然超时
            // dd -= ds;  
            // q ++;
            long tmp = ds, multiple = 1;
            while (dd >= (tmp << 1)) {
                tmp = tmp << 1; // tmp <<= 1; 相当于 * 2
                multiple <<= 1;
            }
            dd -= tmp;
            q += multiple;
        }
        q = negative ? -q : q;
        if (q < Integer.MIN_VALUE) q = Integer.MIN_VALUE;
        if (q > Integer.MAX_VALUE) q = Integer.MAX_VALUE;
        return (int) q;
    }
}
```



## 30



## [31. Next Permutation](https://leetcode.com/problems/next-permutation/) [Simulation]



## 32



## [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

```java
class Solution {
    public int search(int[] nums, int target) {
        int l = 0, r = nums.length - 1;
        while (l < r) {
            if (nums[l] == target) return l;
            if (nums[r] == target) return r;
            int m = (l + r) / 2;
            if (nums[m]==target) return m;
            // 判断那一边是正常排序的
            if (nums[l] < nums[m]) {  // nums:[l,m] 递增
                if (nums[l] < target && target < nums[m]) r = m - 1;
                else l = m;
            } else {  // nums:[m,r] 递增
                if (nums[m] < target && target < nums[r]) l = m + 1;
                else r = m;
            }
        }
        return nums[l] == target ? l : -1;
    }
}
```

**递归能更好地体现这种分治思想**

```java
class Solution {
    private int nums[];
    private int tar;
    private int recursion(int l, int r) {
        if (nums[l]==tar) return l;
        if (nums[r]==tar) return r;
        if (l==r) return -1;
        int m = (l+r)/2;
        if (nums[m]==tar) return m;
        if (nums[l] < nums[m]) {  // nums:[l,m] 递增
            if (nums[l] < tar && tar < nums[m]) return recursion(l, m-1);
            else return recursion(m, r);
        } else {  // nums:[m,r] 递增
            if (nums[m] < tar && tar < nums[r]) return recursion(m+1, r);
            else return recursion(l, m);
        }
    }

    public int search(int[] nums, int target) {
        this.nums = nums;
        this.tar = target;
        return recursion(0, nums.length-1);
    }
}
```





## [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) [Array]



## [35. Search Insert Position](https://leetcode.com/problems/search-insert-position/) [Array]



## [36. Valid Sudoku](https://leetcode.com/problems/valid-sudoku/)

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        Set<Character> record = new HashSet<>();
        for (int r = 0; r < 9; r ++) {
            record.clear();
            for (int c = 0; c < 9; c ++) {
                char num = board[r][c];
                if (num == '.') continue;
                if (record.contains(num)) return false;
                record.add(num);
            }
        }

        for (int c = 0; c < 9; c ++) {
            record.clear();
            for (int r = 0; r < 9; r ++) {
                char num = board[r][c];
                if (num == '.') continue;
                if (record.contains(num)) return false;
                record.add(num);
            }
        }

        for (int d = 0; d < 9; d ++) {
            record.clear();
            for (int r = d / 3 * 3; r < d / 3 * 3 + 3; r ++) {
                for (int c = d % 3 * 3; c < d % 3 *3 + 3; c ++) {
                    char num = board[r][c];
                    if (num == '.') continue;
                    if (record.contains(num)) return false;
                    record.add(num);
                }
            }
        }
        return true;
    }
}
```

使用增强 for 循环

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        boolean[] rec = new boolean[9];
        for (char[] row : board) {
            Arrays.fill(rec, false);
            for (char ch : row) {
                if (ch == '.') continue;
                int idx = ch - '1';
                if (rec[idx]) return false;
                rec[idx] = true;
            }
        }

        for (int col = 0; col < 9; col ++) {
            Arrays.fill(rec, false);
            for (char[] row : board) {
                if (row[col] == '.') continue;
                int idx = row[col] - '1';
                if (rec[idx]) return false;
                rec[idx] = true;
            }
        }

        for (int di = 0; di < 9; di += 3) {
            for (int dj = 0; dj < 9; dj += 3) {
                Arrays.fill(rec, false);
                for (int i = 0; i < 3; i ++) {
                    for (int j = 0; j < 3; j ++) {
                        char ch = board[di + i][dj + j];
                        if (ch == '.') continue;
                        int idx = ch - '1';
                        if (rec[idx]) return false;
                        rec[idx] = true;
                    }
                }
            }
        }

        return true;
    }
}
```



## [37. Sudoku Solver](https://LeetCode.com/problems/sudoku-solver/) [Backtrack]



## [38. Count and Say](https://leetcode.com/problems/count-and-say/)

```java
class Solution {
    public String countAndSay(int n) {
        if (n == 1) return "1";
        String prev = countAndSay(n-1);
        StringBuilder sb = new StringBuilder();
        int cnt = 1;
        char ch = prev.charAt(0);
        for (int i = 1; i < prev.length(); i ++) {
            if (prev.charAt(i) == ch) cnt ++;
            else {
                sb.append(String.valueOf(cnt));
                sb.append(ch);
                ch = prev.charAt(i);
                cnt = 1;
            }
        }
        sb.append(String.valueOf(cnt));
        sb.append(ch);
        return sb.toString();
    }
}
```



## [39. Combination Sum](https://LeetCode.com/problems/combination-sum/) [Backtrack]



## [40. Combination Sum II](https://LeetCode.com/problems/combination-sum-ii/) [Backtrack]



## 41



## [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) [Mono Stack]



## [43. Multiply Strings](https://leetcode.com/problems/multiply-strings/)

原理: n1 位数 * n2 位数 = (n1 + n2) 位数 or (n1 + n2 - 1) 位数

e.g. 1 * 10 = 10; 3 * 50 = 150

使用 int[] tmp 数组, 表示各位数相乘相加的结果 (不考虑进位)

遍历 int[] tmp 数组, 完成进位问题

**关键**: n1的第i1位 * n2的第i2位, 其结果应该加入到 tmp[i1 + i2]

```java
class Solution {
    private String killHead0(String s) {
        int start = 0;
        for (; start < s.length() - 1; start++) {  // 至少要留下一位
            if (s.charAt(start) != '0') break;
        }
        return s.substring(start);
    }

    public String multiply(String num1, String num2) {
        num1 = killHead0(num1);
        num2 = killHead0(num2);
        int len1 = num1.length(), len2 = num2.length();
        int [] tmp = new int[len1 + len2];
        for (int i1 = 0; i1 < len1; i1 ++) {
            for (int i2 = 0; i2 < len2; i2 ++) {
                char ch1 = num1.charAt(len1 - 1 - i1), ch2 = num2.charAt(len2 - 1 - i2);
                int mul = (int)(ch1 - '0') * (int)(ch2 - '0');
                tmp[i1 + i2] += mul;  // 不考虑进位
            }
        }
        for (int i = 0; i < tmp.length - 1; i ++) {  // 统一处理进位 i = 0, ..., len - 2
            tmp[i + 1] += tmp[i] /10;
            tmp[i] %= 10;
        }
        /* 也可以用如下 carry 的方法处理进位
        int carry = 0;
        for (int i = 0; i < arr.length; i ++) {
            arr[i] += carry;
            carry = arr[i] / 10;
            arr[i] %= 10;
        }
        */
        StringBuilder sb = new StringBuilder();
        for (int i = tmp.length - 1; i >= 0; i --) sb.append((char)(tmp[i] + '0'));
        return killHead0(sb.toString());
    }
}
```

```
num1 = 89, num2 = 28;
tmp = [0, 0, 0, 0];
从低位到高位
9 8
8 2

tmp = [9*8, 8*8,       0,   0]
tmp = [9*8, 8*8 + 2*9, 2*8, 0]
	= [72,  82,        16,  0]

处理进位
tmp = [2, 89, 16, 0 ]
	= [2, 9,  24, 0 ]
	= [2, 9,  4,  2 ]
结果 2492
---
num1 = 10, num2 = 1;

0 1
1

tmp = [0, 0, 0]
   -> [0, 1, 0]
010 -> 10
```

类似地, 可以写出字符串数字加法的代码:

```java
class Solution {
    private String killHead0(String s) {
        int start = 0;
        for (; start < s.length() - 1; start++) {
            if (s.charAt(start) != '0') break;
        }
        return s.substring(start);
    }

    public String add(String num1, String num2) {
        num1 = killHead0(num1);
        num2 = killHead0(num2);
        int[] tmp = new int[Math.max(num1.length(), num2.length()) + 1];
        for (int i = 0; i < tmp.length; i++) {
            int idx1 = num1.length() - 1  - i;
            int idx2 = num2.length() - 1  - i;
            int cur1 = (idx1 < 0) ? 0 : (int)(num1.charAt(idx1) - '0');
            int cur2 = (idx2 < 0) ? 0 : (int)(num2.charAt(idx2) - '0');
            int sum = cur1 + cur2;
            tmp[i] = sum;
        }
        for (int i = 0; i < tmp.length - 1; i++) {
            tmp[i + 1] += tmp[i] / 10;
            tmp[i] %= 10;
        }
        StringBuilder sb = new StringBuilder();
        for (int i = tmp.length - 1; i >= 0; i--) sb.append(tmp[i]);
        return killHead0(sb.toString());
    }
}
```

## 44



## [45. Jump Game II](https://leetcode.com/problems/jump-game-ii/) [Greedy]



## [46. Permutations](https://leetcode.com/problems/permutations/) [Backtrack]



## [47. Permutations II](https://leetcode.com/problems/permutations-ii/) [Backtrack]



## [48. Rotate Image](https://leetcode.com/problems/rotate-image/)

题目要求不能使用额外空间, 以下算法不严谨

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        int[][] tmp = new int[n][n];
        for (int r = 0; r < n; r ++) {
            for (int c = 0; c < n; c ++) {
                tmp[c][n - r - 1] = matrix[r][c];
            }
        }

        for (int r = 0; r < n; r ++) {
            for (int c = 0; c < n; c ++) {
                matrix[r][c] = tmp[r][c];
            }
        }
    }
}
```

使用两次翻转代替旋转, 满足不使用额外空间的要求

```java
class Solution {
    public void rotate(int[][] matrix) {
        // 对角线翻转
        int n = matrix.length;
        for (int i = 0; i < n; i ++) {
            for (int j = i + 1; j < n; j ++) {
                int t = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = t;
            }
        }
        // 左右翻转
        for (int[] row : matrix) {
            int i = 0, j = n - 1;
            while (i < j) {
                int t = row[i];
                row[i] = row[j];
                row[j] = t;
                i ++; j --;
            }
        }
    }
}
```

```
for (int[] row : matrix) {
    int i = 0, j = n - 1;
    while (i < j) {
        int t = row[i];
        row[i] = row[j];
        row[j] = t;
        i ++; j --;
    }
}
---
nums = new int[]{1,2,3,4,5};
for (int num : nums) {
    num = 0; // new 了新的对象, nums不会改变
}
```



## [49. Group Anagrams](https://leetcode.com/problems/group-anagrams/)

利用排序归一化

```java
class Solution {
    private String formulate(String s) {
        char[] chars = s.toCharArray();
        Arrays.sort(chars);
        return new String(chars);
    }

    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String str : strs) {
            String formulatedStr = formulate(str);
            if (!map.containsKey(formulatedStr)) {
                map.put(formulatedStr, new LinkedList<>());
            }
            map.get(formulatedStr).add(str);
        }
        return new ArrayList<>(map.values()); // 把 map 的 values 快速转换成 list
    }
}
```



## [50. Pow(x, n)](https://leetcode.com/problems/powx-n/)

### 迭代法

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) return 1;
        if (x == 0.0) return 0;
        if (x == 1.0) return 1.0;
        if (x == -1.0) return (n % 2 == 0) ? 1.0 : -1.0;

        if (n == Integer.MIN_VALUE) return 0.0;

        if (n < 0) {
            n *= -1;
            x = 1 / x;
        }
        double res = 1.0;
        for (int i = 0; i < n; i ++) res *= x;
        return res;
    }
}
```

### 递归法

时间复杂度 O(log n)

```java
class Solution {
    public double myPow(double x, int n) {
        if (x == 1.0) return 1.0;
        if (x == -1.0) return (n&1) == 0 ? 1.0 : -1.0;
        if (x != 1.0 && n == Integer.MIN_VALUE) return 0.0;
        // 以上是特殊情况特判

        if (n == 0) return 1.0;
        else if (n < 0) return 1.0 / myPow(x, -n);
        else {
            if ((n&1) == 0) {
                double tmp = myPow(x, n>>>1); // 使用 tmp 记录, 减少递归次数
                return tmp * tmp;
            } else {
                double tmp = myPow(x, (n-1)>>>1);
                return x * tmp * tmp;
            }
        }
    }
}
```

