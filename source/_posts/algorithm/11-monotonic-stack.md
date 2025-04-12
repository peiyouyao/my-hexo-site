---
title: Monotonic Stack
date: 2024-07-20 00:00:00
tags: key-algorithm
categories: algorithm
---

---

# Examples

```java
class Solution {
	/**
    * 单调栈的模板, 返回各个位置上, 第一个小于它的元素的索引
    */
    private int[] firstSmallerElementIndexOnLeft(int[] nums) {
        int[] ans = new int[nums.length];
        Arrays.fill(ans, nums.length);
        LinkedList<Integer> stk = new LinkedList<>();
        for (int i = 0; i < nums.length; i ++) {
            while (!stk.isEmpty() && nums[i] < nums[stk.getLast()]) ans[stk.removeLast()] = i;
            stk.addLast(i);
        }
        return ans;
    }
}
```

## [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int[] ans = new int[temperatures.length];
        List<Integer> monoStk = new ArrayList<>();
        for (int i = 0; i < temperatures.length; i ++) {
            while (!monoStk.isEmpty() && temperatures[monoStk.getLast()] < temperatures[i]) {
                int lastDay = monoStk.removeLast();
                ans[lastDay] = i - lastDay;
            }
            monoStk.addLast(i);
        }
        return ans;
    }
}
```

过程模拟

```
t = [8, 9, 6, 4, 10]
stk = [0]

i = 1:
    curInd = 1, curT = 9,    
        preInd = 0, preT = 8,
        9 > 8
        res = [9 - 8, 0, 0, 0, 0]
        stk = [1]

i = 2:
    curInd = 2, curT = 6,
        preInd = 1, preT = 9,
        6 < 9,
        res = [1, 0, 0, 0, 0]
        stk = [1, 2]


i = 3:
    curInd = 3, curT = 4,
        preInd = 2, preT = 6,
        4 < 6,
        res = [1, 0, 0, 0, 0]
        stk = [1, 2, 3]

i = 4:
    curInd = 4, curT = 10,
        preInd = 3, preT = 4,
        10 > 4,
        res = [1, 0, 0, 4 - 3, 0]
        stk = [1, 2]

        preInd = 2, preT = 6,
        10 > 6,
        res = [1, 0, 4 - 2, 1, 0]
        stk = [1]

        preInd = 1, preT = 9,
        10 > 9,
        res = [1, 4 - 1, 2, 1, 0]
        stk = []
    stk = [4]
       
res = [1, 3, 2, 1, 0]
```

## [496. Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/)

#### Java

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        int[] nextGreater = new int[nums2.length]; // nums2 中, i 右侧, 大于索引 i 元素的第一个元素的**值**
        Arrays.fill(nextGreater, -1);
        Map<Integer, Integer> pos = new HashMap<>();
        List<Integer> monoStk = new ArrayList<>();
        for (int i = 0; i < nums2.length; i ++) {
            pos.put(nums2[i], i);
            while (!monoStk.isEmpty() && nums2[monoStk.getLast()] < nums2[i]) nextGreater[monoStk.removeLast()] = nums2[i]; // 因为要的是值, 这里要取nums2[i]
            monoStk.addLast(i);
        }
        for (int i = 0; i < nums1.length; i ++) {
            int idx = pos.get(nums1[i]);
            nums1[i] = nextGreater[idx];
        }
        return nums1;
    }
}
```

#### Rust

```rust
use std::collections::HashMap;

impl Solution {
    pub fn next_greater_element(mut nums1: Vec<i32>, mut nums2: Vec<i32>) -> Vec<i32> {
        let mut next_greater: Vec<i32> = vec![-1; nums2.len()];
        let mut pos: HashMap<i32, usize> = HashMap::new();
        let mut monoStk: Vec<usize> = Vec::new();
        for (i, &num) in nums2.iter().enumerate() {
            pos.insert(num, i);
            while let Some(&last) = monoStk.last() {
                if nums2[last] < num {
                    next_greater[last] = num;
                    monoStk.pop();
                } else {
                    break;
                }
            }
            monoStk.push(i);
        }

        for i in 0..nums1.len() {
            if let Some(&idx) = pos.get(&nums1[i]) {
                nums1[i] = next_greater[idx];
            }
        }
        nums1
    }
}
```



## [503. Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/)

#### Java

```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int len = nums.length;
        int[] res = new int[len];
        Arrays.fill(res, -1);
        LinkedList<Integer> monoStk = new LinkedList<>();
        monoStk.addLast(0);
        for (int i = 1; i < len * 2; i ++) {
            int curInd = i % len, curNum = nums[curInd];
            int preInd = monoStk.getLast(), preNum = nums[preInd];
            while (curNum > preNum) {
                res[preInd] = curNum;
                monoStk.removeLast();
                if (monoStk.isEmpty()) break;
                preInd = monoStk.getLast();
                preNum = nums[preInd];
            }
            monoStk.add(curInd);
        }
        return res;
    }
}
```

#### Rust

```rust
impl Solution {
    pub fn next_greater_elements(nums: Vec<i32>) -> Vec<i32> {
        let mut ans = vec![-1; nums.len()];
        let mut monoStk = Vec::new();

        for i in 0..nums.len() * 2 {
            let i = i % nums.len();
            while let Some(&last_idx) = monoStk.last() {
                if nums[last_idx] < nums[i] {
                    ans[last_idx] = nums[i];
                    monoStk.pop();
                } else {
                    break;
                }
            }
            monoStk.push(i);
        }
        ans
    }
}
```



## [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

两边走

```java
class Solution {
    public int trap(int[] height) {
        int h = 0;
        int[] traps = new int[height.length];
        for (int i = 0; i < height.length; i ++) {
            h = Math.max(h, height[i]);
            traps[i] = h;
        }
        h = 0;
        for (int i = height.length-1; i > -1; i --) {
            h = Math.max(h, height[i]);
            traps[i] = Math.min(traps[i], h);
        }
        int ans = 0;
        for (int i = 0; i < height.length; i ++) {
            ans += traps[i] - height[i];
        }
        return ans;
    }
}
```



```java
class Solution {
    public int trap(int[] height) {
        int len = height.length;
        int accum = 0;
        int[] left2right = new int[len];
        for (int i = 0; i < len; i ++) {
            accum = Math.max(height[i], accum);
            left2right[i] = accum;
        }
        accum = 0;
        int[] right2left = new int[len];
        for (int i = len - 1; i > -1; i --) {
            accum = Math.max(height[i], accum);
            right2left[i] = accum;
        }
        int res = 0;
        for (int i = 0; i < len; i ++) {
            int heightWithRain = Math.min(left2right[i], right2left[i]);
            int heightOfRain = heightWithRain - height[i];
            res += heightOfRain;
        }
        return res;
    }
}
```

模拟

```
h              = [0,1,0,2,1,0,1,3,2,1,2,1]
l2r            = [0,1,1,2,2,2,2,3,3,3,3,3]
r2l            = [3,3,3,3,3,3,3,3,2,2,2,1]
heightWithRain = [0,1,1,2,2,2,2,3,2,2,2,1]
heightOfRain   = [0,0,1,0,1,2,1,0,0,1,0,0]  total = 6
```

## [84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        // 对于 i, 要考查 i 左右两边第一个小于它的位置在哪
        // 默认 index = -1 和 len 处, h = 0;
        int[] left = getFirstSmallerElementIndex(heights, true);  // 记录 i 左边第一次小于 nums[i] 的位置 
        int[] right = getFirstSmallerElementIndex(heights, false);  // 记录 i 右边第一次小于 nums[i] 的位置
        int res = 0;
        for (int i = 0; i < heights.length; i ++) {
            res = Math.max(res, heights[i] * (left[i] - right[i] - 1));  // 要完全包含 i 方块的最大矩形面积
        }
        return res;
    }
    
    private int[] firstSmallerElementIndexOnLeft(int[] nums) {
        int[] ans = new int[nums.length];
        Arrays.fill(ans, nums.length);
        LinkedList<Integer> stk = new LinkedList<>();
        for (int i = 0; i < nums.length; i ++) {
            while (!stk.isEmpty() && nums[i] < nums[stk.getLast()]) ans[stk.removeLast()] = i;
            stk.addLast(i);
        }
        return ans;
    }
    private int[] getFirstSmallerElementIndex(int[] nums, boolean left2right) {
        if (left2right == false) reverseArray(nums);
        int[] res = firstSmallerIdx(nums);
        if (left2right == false) {
            reverseArray(nums);
            reverseArray(res);
            res = Arrays.stream(res).map(x->len-1-x).toArray();
        }
        return res;
    }
    private void reverseArray(int[] nums) {
        int l = 0, r = nums.length - 1;
        while (l < r) {
            int t = nums[l];
            nums[l] = nums[r];
            nums[r] = t;
            l ++; r --;
        }
    }
}
```

