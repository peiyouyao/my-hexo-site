---
title: String
date: 2024-04-22 00:00:00
tags: key-algorithm
categories: algorithm
---

---

# Examples

## [344. Reverse String](https://leetcode.com/problems/reverse-string/)

#### Java

```java
class Solution {
    public void reverseString(char[] s) {
        int len = s.length;
        int i = 0, j = len - 1;
        while (i <= j) {
            char tmp = s[i];
            s[i] = s[j];
            s[j] = tmp;
            i ++;
            j --;
        }
    }
}
```

#### Rust

```rust
impl Solution {
    pub fn reverse_string(s: &mut Vec<char>) {
        fn swap_chars(ch1: &mut char, ch2: &mut char) {
            let tmp: char = *ch1;
            *ch1 = *ch2;
            *ch2 = tmp;
        } // 函数用于交换两个字符

        let mut l: usize = 0;
        let mut r: usize = s.len() - 1;
        while l < r {
            let mut left = s[l];
            let mut right = s[r];
            swap_chars(&mut left, &mut right);
            s[l] = left;
            s[r] = right;
            l += 1;
            r -= 1;
        }
    }
}
```

## [541. Reverse String II](https://leetcode.com/problems/reverse-string-ii/)

#### Java

```java
class Solution {
    public String reverseStr(String s, int k) {
        char[] sLst = s.toCharArray();
        int start = 0, end = k;
        int len = s.length();

        while ( start < len ) {
            end = Math.min(len, end);
            int i = start, j = end - 1;
            while (i <= j) {
                char t = sLst[i];
                sLst[i] = sLst[j];
                sLst[j] = t;
                i ++;
                j --;
            }
            start += 2 * k;
            end += 2 * k;
        }
        return new String(sLst);  // new String(char[] charArr);
    }
}
```

#### C++

```c++
class Solution {
public:
    string reverseStr(string s, int k) {
        for (int i = 0; i < s.size(); i += (2 * k)) {
            if (i + k <= s.size()) reverse(s.begin() + i, s.begin() + i + k);
            else reverse(s.begin() + i, s.end());
        }
        return s;
    }
};
```

## [54. 替换数字（第八期模拟笔试）](https://kamacoder.com/problempage.php?pid=1064)

> 给定一个字符串 s，它包含小写字母和数字字符，请编写一个函数，将字符串中的字母字符保持不变，而将每个数字字符替换为number。 例如，对于输入字符串 "a1b2c3"，函数应该将其转换为 "anumberbnumbercnumber"。

```java
class Solution {
    public String replaceNumber(String s) {
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) sb.append("number");
            else sb.append(c);
        }
        return sb.toString();
    }
}
```

## [151. Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)

#### Java

```java
class Solution {
    public String reverseWords(String s) {
        String[] words = s.split(" ");  // java 的split 会搞出空字符串
        StringBuilder sb = new StringBuilder();
        for (int i = words.length - 1; i >= 0; i--) {
            if (!"".equals(words[i])) {
                sb.append(words[i]);
                sb.append(" ");
            }
        }
        for (int i = sb.length() - 1; i > -1; i --) {
            if (sb.charAt(i) == ' ') sb.deleteCharAt(i);
            else break;
        }
        return sb.toString();
    }
}
```

#### C++

```c++
class Solution {
    void reverse(string& s, int start, int end) {
        for (int i = start, j = end; i < j; i ++, j --) swap(s[i], s[j]);
    }
    void trim(string& s) {
        int slow = 0;
        for (int i = 0; i < s.size(); i ++) {
            if (s[i] != ' ') {
                if (slow > 0) {
                    s[slow] = ' ';
                    slow ++;
                }
                while (i < s.size() && s[i] != ' ') {
                    s[slow] = s[i];
                    slow ++;
                    i ++;
                }
            }
        }
        s.resize(slow);
    }
public:
    string reverseWords(string s) {
        trim(s);
        reverse(s, 0, s.size() - 1);
        for (int l = 0, r = 0; r < s.size(); r ++) {
            if (s[r] == ' ') {
                reverse(s, l, r - 1);
                l = r + 1;
            }
            if (r == s.size() - 1) reverse(s, l, r);
        }
        return s;
    }
};
```

## [55. 右旋字符串（第八期模拟笔试）](https://kamacoder.com/problempage.php?pid=1065)

> 字符串的右旋转操作是把字符串尾部的若干个字符转移到字符串的前面。给定一个字符串 s 和一个正整数 k，请编写一个函数，将字符串中的后面 k 个字符移到字符串的前面，实现字符串的右旋转操作。 
>
> 例如，对于输入字符串 "abcdefg" 和整数 2，函数应该将其转换为 "fgabcde"。

#### Java

```java
class Solution {
    public String rotateString(String s, int k) {
        int len = s.length();
        k = k % len;
        s += s;
        s =s.substring(len - k, 2 * len - k);
        return s;
    }
}
```

#### C++

```c++
#include <iostream>
#include <algorithm>
using namespace std;

void rotate(string& s, int k) {
    reverse(s.begin(), s.end()); // 整体反转
    reverse(s.begin(), s.begin() + k); // 先反转前一段，长度n
    reverse(s.begin() + k, s.end()); // 再反转后一段
}

int main() {
    int k;
    string s;
    cin >> k;
    cin >> s;
    rotate(s, k);
    cout << s << endl;
    return 0;
}

```



## [28. Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/)

***KMP 算法***

```
前缀表: next 数组
needle = a a b a a f
next   =[0,1,0,1,2,0]
next[i]: needle[0..=i]中的最长相同前后缀的长度

当 needle[j] 不匹配, 那么就看看 next[j - 1], 
next[j - 1] 表示 needle[0..j]中的相同前后缀的最大长度 (记为 d)
于是, needle[0..d]就都不需要再比较,
可以直接从 needle[d] 开始比较
```

#### Java

时间复杂度 $O(n + m)$ 

```java
class Solution {
    private int[] getNext(String s) {
        int[] next = new int[s.length()]; // next[0] = 0, next[j]: s[0..=j] 最长公共前后缀
        for (int i = 1, j = 0; i < s.length(); i ++) { // i 从 1 开始, j表示最长相等前缀的长度
            while (j > 0 && s.charAt(j) != s.charAt(i)) j = next[j - 1]; // 当needle[i]与needle[j]不匹配时，回退j的值
            if (s.charAt(j) == s.charAt(i)) j ++; // 如果needle[i] == needle[j]，说明找到了一个更长的相等前缀, 匹配成功，前缀长度加1
            next[i] = j; // 将当前的j值赋给next[i]
        }
        return next;
    }

    public int strStr(String haystack, String needle) {
        int[] next = getNext(needle);
        for (int i = 0, j = 0; i < haystack.length(); i ++) {
            while (j > 0 && haystack.charAt(i) != needle.charAt(j)) j = next[j - 1];
            if (haystack.charAt(i) == needle.charAt(j)) {
                j ++;
                if (j == needle.length()) return i - needle.length() + 1;
            }
        }
        return -1;
    }
}
```

#### C++

```c++
class Solution {
    void fillNext(int* next, const string& s) {
        next[0] = 0;
        for (int i = 1, j = 0; i < s.size(); i ++) {
            while (j > 0 && s[i] != s[j]) j = next[j - 1];
            if (s[i] == s[j]) j ++;
            next[i] = j;
        }
    }
public:
    int strStr(string haystack, string needle) {
        if (needle.size() == 0) return 0;
        vector<int> next(needle.size());
        fillNext(&next[0], needle);
        for (int i = 0, j = 0; i < haystack.size(); i ++) {
            while (j > 0 && haystack[i] != needle[j]) j = next[j - 1];
            if (haystack[i] == needle[j]) {
                j ++;
                if (j == needle.size()) return i - needle.size() + 1;
            }
        }
        return -1;
    }
};
```

#### Rust

```rust
impl Solution {
    pub fn str_str(haystack: String, needle: String) -> i32 {
        let needle: Vec<char> = needle.chars().collect();
        let next: Vec<usize> = Self::get_next(&needle);
        let haystack: Vec<char> = haystack.chars().collect();
        let mut j: usize = 0;
        for (i, &c) in haystack.iter().enumerate() {
            while j > 0 && c != needle[j] { j = next[j - 1];}
            if c == needle[j] {
                j += 1;
                if j == needle.len() {
                    return (i + 1 - needle.len()) as i32;
                }
            }
        }
        -1
    }

    fn get_next(s: &Vec<char>) -> Vec<usize> {
        let mut next: Vec<usize> = vec![0; s.len()];
        let mut j: usize = 0;
        for (i, &c) in s.iter().enumerate().skip(1) {
            while j > 0 && c != s[j] { j = next[j - 1]; }
            if c == s[j] { j += 1; }
            next[i] = j;
        }
        next
    }
}
```

## [459. Repeated Substring Pattern](https://leetcode.com/problems/repeated-substring-pattern/)

#### Java

使用 `String::repeat` Api, 要额外占用很多空间

```java
class Solution {
    public boolean repeatedSubstringPattern(String s) {
        if (s.length() < 2) return false;
        for (int subLen = 1; subLen <= s.length() / 2; subLen ++) {
            if (s.length() % subLen != 0) continue;
            String sub = s.substring(0, subLen);
            if (s.equals(sub.repeat(s.length() / subLen))) return true;
        }
        return false;
    }
}
```

直接在原串上比较

```java
class Solution {
    public boolean repeatedSubstringPattern(String s) {
        for (int subLen = 1; subLen <= s.length() / 2; subLen ++) {
            if (s.length() % subLen != 0) continue;
            boolean flg = true;
            for (int i = subLen; i < s.length(); i ++) {
                int idx = i % subLen;
                if (s.charAt(i) != s.charAt(idx)) {
                    flg = false;
                    break;
                }
            }
            if (flg) return true;
        }
        return false;
    }
}
```

#### C++

```c++
class Solution {
public:
    bool repeatedSubstringPattern(string s) {
        if (s.size() < 2) return false;
        for (int sublen = 1; sublen <= s.size() / 2; sublen ++) {
            if (s.size() % sublen != 0) continue;
            bool flg = true;
            for (int i = sublen; i < s.size(); i ++) {
                if (s[i] != s[i % sublen]) {
                    flg = false;
                    break;
                }
            }
            if (flg) return true;
        }
        return false;
    }
};
```

#### Rust

```rust
impl Solution {
    pub fn repeated_substring_pattern(s: String) -> bool {
        let s: Vec<char> = s.chars().collect();
        for sublen in 1..=s.len() / 2 {
            if s.len() % sublen != 0 { continue; }
            let mut flg: bool = true;
            for i in sublen..s.len() {
                if s[i] != s[i % sublen] {
                    flg = false;
                    break;
                }
            }
            if flg { return true; }
        }
        false
    }
}
```

# Extra

## [925. Long Pressed Name](https://leetcode.com/problems/long-pressed-name/)

#### Java

```java
class Solution {
    public boolean isLongPressedName(String name, String typed) {
        if (name.length() > typed.length()) return false;
        int idx = 0;
        for (int i = 0; i < name.length(); i ++) {
            if (idx == typed.length()) return false;
            char cn = name.charAt(i);
            if (cn != typed.charAt(idx)) { // 简化逻辑
                while (
                    i > 0 && 
                    idx < typed.length() && 
                    typed.charAt(idx) != cn && 
                    typed.charAt(idx) == name.charAt(i - 1)
                ) idx ++;
                if (idx == typed.length() || typed.charAt(idx) != cn) return false;
            }
            idx ++;
        }
        for (int i = idx; i < typed.length(); i ++) if (typed.charAt(i) != name.charAt(name.length()-1)) return false;
        return true;
    }
}
```

```java
class Solution {
    private int cut(String s, int start) {  // 每次从 start 处截取前面相同的字符 (s.substring(start, end) 全是相同的字符构成的字符串)
        char ch = s.charAt(start);
        int end = start + 1;
        while (end < s.length()) {
            if (s.charAt(end) != ch) break;
            end ++;
        }
        return end;
    }

    public boolean isLongPressedName(String name, String typed) {
        if (name.length() > typed.length()) return false;
        int startName = 0, startTyped = 0;
        while (startTyped < typed.length() && startName < name.length()) {
            if (name.charAt(startName) != typed.charAt(startTyped)) return false;
            int endName = cut(name, startName);
            int endTyped = cut(typed, startTyped);
            if ((endTyped - startTyped) < (endName - startName)) return false;
            startName = endName;
            startTyped = endTyped;
        }
        return (startTyped >= typed.length() && startName >= name.length());
    }
}
```

#### C++

```c++
class Solution {
public:
    bool isLongPressedName(string name, string typed) {
        int j = 0;
        for (int i = 0; i < name.size(); i ++) {
            while (name[i] != typed[j]) {
                if (i > 0 && j > 0 && j < typed.size() && typed[j] == name[i - 1]) j ++;
                else return false;
            }
            j ++;
        }
        while (j < typed.size() && typed[j] == name[name.size() - 1]) j ++;
        return j == typed.size();
    }
};
```

```c++
class Solution {
    int cutSameChars(string& s, int start) {
        int end = start + 1;
        while (end < s.size() && s[start] == s[end]) end ++;
        return end;
    }
public:
    bool isLongPressedName(string name, string typed) {
        int start_name = 0, start_typed = 0;
        while (start_name < name.size() && start_typed < typed.size()) {
            if (name[start_name] != typed[start_typed]) return false;
            int end_name = cutSameChars(name, start_name);
            int end_typed = cutSameChars(typed, start_typed);
            if (end_name - start_name > end_typed - start_typed) return false;
            start_name = end_name;
            start_typed = end_typed;
        }
        return start_typed == typed.size() && start_name == name.size();
    }
};
```

#### Rust

```rust
impl Solution {
    pub fn is_long_pressed_name(name: String, typed: String) -> bool {
        let mut i: usize = 0;
        let mut j: usize = 0;
        let name: Vec<char> = name.chars().collect();
        let typed: Vec<char> = typed.chars().collect();
        while i < name.len() && j < typed.len() {
            if name[i] != typed[j] { return false; }
            let ni = Self::cut_same_chars(&name, i);
            let nj = Self::cut_same_chars(&typed, j);
            if ni - i > nj - j { return false; }
            (i, j) = (ni, nj);
        }
        i == name.len() && j == typed.len()
    }

    fn cut_same_chars(s: &Vec<char>, start: usize) -> usize {
        let mut end = start + 1;
        while end < s.len() && s[end] == s[start] { end += 1; }
        end
    }
}
```

## [844. Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/)

#### Java

*Stack*

```java
class Solution {
    public boolean backspaceCompare(String s, String t) {
        StringBuilder ssb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (c == '#') { if (ssb.length() > 0) ssb.deleteCharAt(ssb.length() - 1); }
            else ssb.append(c);
        }
        StringBuilder tsb = new StringBuilder();
        for (char c : t.toCharArray()) {
            if (c == '#') { if (tsb.length() > 0) tsb.deleteCharAt(tsb.length() - 1); }
            else tsb.append(c);
        }
        return ssb.toString().equals(tsb.toString());
    }
}
```

#### C++

```c++
class Solution {
    int backspace(string& s) {
        int idx = 0;
        for (int i = 0; i < s.size(); i ++) {
            if (s[i] == '#') {
                idx --;
                idx = max(0, idx);
            } else {
                s[idx] = s[i];
                idx ++;
            }
        }
        return idx;
    }
public:
    bool backspaceCompare(string s, string t) {
        int slen = backspace(s);
        int tlen = backspace(t);
        if (slen != tlen) return false;
        for (int i = 0; i < slen; i ++) if (s[i] != t[i]) return false;
        return true;
    }
};
```

#### Rust

```rust
impl Solution {
    pub fn backspace_compare(s: String, t: String) -> bool {
        let mut s: Vec<char> = s.chars().collect();
        let mut t: Vec<char> = t.chars().collect();
        let slen = Self::backspace(&mut s);
        let tlen = Self::backspace(&mut t);
        if slen != tlen { return false; }
        for i in 0 .. slen { if s[i] != t[i] { return false; } };
        true
    }

    fn backspace(s: &mut Vec<char>) -> usize {
        let mut idx = 0;
        for i in 0..s.len() {
            if s[i] == '#' {
                if idx > 0 { idx -= 1; }
            } else {
                s[idx] = s[i];
                idx += 1;
            }
        }
        idx
    }
}
```



