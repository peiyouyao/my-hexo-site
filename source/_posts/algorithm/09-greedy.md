---
title: Greedy
date: 2024-05-26 00:00:00
tags: key-algorithm
categories: algorithm
---

---

# Examples

## [455. Assign Cookies](https://leetcode.com/problems/assign-cookies/)

#### Java

```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int res = 0;
        int id = 0;
        for (int need : g) {
            while (id < s.length && s[id] < need) id ++;
            if (id == s.length) break;
            else {
                res ++;
                id ++;
            }
        }
        return res;
    }
}
```

贪心思路: 给需求少的小孩尽可能小的饼干

#### C++

```c++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        int ans = 0;
        for (int i = 0, j = 0; i < g.size() && j < s.size(); j ++) {
            if (g[i] <= s[j]) {
                ans ++;
                i ++;
            }
        }
        return ans;
    }
};
```



## [376. Wiggle Subsequence](https://leetcode.com/problems/wiggle-subsequence/)

#### Java

*DP*

```java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        // 0 i 作为波峰的最大长度
        // 1 i 作为波谷的最大长度
        int dp[][] = new int[nums.length][2];

        dp[0][0] = dp[0][1] = 1;
        for (int i = 1; i < nums.length; i++){
            //i 自己可以成为波峰或者波谷
            dp[i][0] = dp[i][1] = 1;

            for (int j = 0; j < i; j++){
                if (nums[j] > nums[i]){
                    // i 是波谷
                    dp[i][1] = Math.max(dp[i][1], dp[j][0] + 1);
                }
                if (nums[j] < nums[i]){
                    // i 是波峰
                    dp[i][0] = Math.max(dp[i][0], dp[j][1] + 1);
                }
            }
        }

        return Math.max(dp[nums.length - 1][0], dp[nums.length - 1][1]);
    }
}
```



#### C++

*Greedy*

```c++
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        if (nums.size() <= 1) return nums.size();
        int delta = 0;
        int ans = 1;
        for (int i = 1; i < nums.size(); i ++) {
            if (nums[i] > nums[i - 1]) { // 当前递增
                if (delta <= 0) { // 需要之前递减或持平
                    ans ++;
                    delta = 1;
                }
            } else if (nums[i] < nums[i - 1]) { // 当前递减
                if (delta >= 0) { // 需要之前递增或持平
                    ans ++;
                    delta = -1;
                }
            } // else {} 当前持平, 啥也不做
        }
        return ans;
    }
};
```



## [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

#### Java

*DP*

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int len = nums.length;
        int[] dp = new int[len];  // dp[i]表示包括i (i一定要被选中) 之前的最大连续子序列和
        dp[0] = nums[0];
        int res = dp[0];
        for (int i = 1; i < len; i ++) {
            dp[i] = Math.max(nums[i] + dp[i - 1], nums[i]);
            res = Math.max(res, dp[i]);  // 结果是dp数组的最大值
        }
        return res;
    }
}
```



#### C++

*Greedy*

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int sum = 0;
        int ans = INT_MIN;
        for (int num : nums) {
            sum += num;
            ans = max(ans, sum); // 收集结果
            if (sum < 0) sum = 0; // 重置 sum
        }
        return ans;
    }
};
```

贪心思路: 当新加入的元素使得 curSum 小于 0 时, 就重置 curSum; 收集结果和重置的顺序不可颠倒

题目如果允许子序列为空 (大不了一个不取), 则收集结果和重置要颠倒

## [122. Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

#### Java

```java
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        for (int i = 1; i < prices.length; i ++) {
            res += Math.max(0, prices[i] - prices[i - 1]);
        }
        return res;
    }
}
```

贪心思路: 只要第二天比第一天搞, 就第一天买第二天卖



## [55. Jump Game](https://leetcode.com/problems/jump-game/)

#### Java

*Simulation*

```java
class Solution {
    private int getNextInd(int nums[], int cur) {
        int curReach = nums[cur] + cur;
        int nextInd = cur;
        int nextReach = curReach;
        for (int i = cur; i <= Math.min(curReach, nums.length - 1); i ++) {
            if (nums[i] + i > nextReach) {
                nextInd = i;
                nextReach = nums[i] + i;
            }
        }
        return nextInd;
    }

    public boolean canJump(int[] nums) {
        int len = nums.length;
        int cur = 0;
        if (cur == len - 1) return true;  // 如果当前已经到达, 返回 true
        while (true) {
            int curReach = nums[cur] + cur;
            if (curReach >= len - 1) return true;  // 如果当前可达范围包含 len - 1, 返回 true
            int nextInd = getNextInd(nums, cur);  // 如果不可到达, 计算最优的下一步索引
            if (nextInd == cur) return false;
            cur = nextInd;
        }
    }
}
```

*Greedy*

```java
class Solution {
    public boolean canJump(int[] nums) {
        int reach = 0; // 可到达的最远位置 (索引)
        for (int i = 0; i <= reach; i ++) {
            if (i == nums.length - 1) return true;
            reach = Math.max(reach, i + nums[i]);
        }
        return false;
    }
}
```



## [45. Jump Game II](https://leetcode.com/problems/jump-game-ii/)

#### Java

```java
class Solution {
    public int jump(int[] nums) {
        int end = 0;
        int reach = 0;
        int cnt = 0;
        for (int i = 0; i <= reach; i ++) {
            if (i == nums.length - 1) break;
            reach = Math.max(reach, i + nums[i]);
            if (i == end) {
                end = reach;
                cnt ++;
            }
        }
        return cnt;
    }
}
```



```java
class Solution {
    private int getNextInd(int nums[], int cur) {
        int curReach = nums[cur] + cur;
        int nextInd = cur;
        int nextReach = curReach;
        for (int i = cur; i <= Math.min(curReach, nums.length - 1); i ++) {
            if (nums[i] + i > nextReach) {
                nextInd = i;
                nextReach = nums[i] + i;
            }
        }
        return nextInd;
    }

    public int jump(int[] nums) {
        int len = nums.length;
        int res = 0;
        int cur = 0;
        if (cur == len - 1) return res;  // 看看一开始是不是就满足条件, 如果是, 就不用跳了, 返回 0
        res ++;  // 如果不满足, 至少跳一次
        while (true) {
            int curReach = nums[cur] + cur;  // 从当前位置可以跳到 (跳一次) 的最大范围
            if (curReach >= len - 1) return res;  // 如果该范围涵盖 len - 1, 直接返回
            res ++;
            cur = getNextInd(nums, cur);
        }
    }
}
```



DP 效率低

```java
class Solution {
    public int jump(int[] nums) {
        int[] dp = new int[nums.length]; // 跳跃到 i 需要的最少次数
        for (int i = 1; i < dp.length; i ++) {
            dp[i] = nums.length;
            for (int j = 0; j < i; j ++) {
                if (j + nums[j] >= i) {  // 从 j 处可以跳跃至 i 的情况下
                    dp[i] = Math.min(dp[j] + 1, dp[i]);
                }
            }
        }
        return dp[nums.length - 1];
    }
}
```



## [1005. Maximize Sum Of Array After K Negations](https://leetcode.com/problems/maximize-sum-of-array-after-k-negations/)

#### Java

```java
class Solution {
    public int largestSumAfterKNegations(int[] nums, int k) {
        Arrays.sort(nums);
        int idx = 0;
        while (k > 0) {
            if (idx < nums.length && nums[idx] < 0) {
                nums[idx] *= -1;
                k --;
                idx ++;
            } else break;
        }
        if (idx > 0 && (idx == nums.length || nums[idx] > nums[idx - 1])) idx --;
        if (k % 2 == 1) nums[idx] *= -1;
        return Arrays.stream(nums).sum();
    }
}
```



```java
class Solution {
    public int largestSumAfterKNegations(int[] nums, int k) {
            Integer[] numsObj = Arrays.stream(nums).boxed().toArray(Integer[]::new);
            Arrays.sort(numsObj, (o1, o2) -> Integer.compare(Math.abs(o2), Math.abs(o1)));
            int res = 0;
            for (Integer cur : numsObj) {
                if (cur >= 0) {
                    res += cur;
                } else {
                    if (k > 0) {
                        res += -cur;
                        k --;
                    } else {
                        res += cur;
                    }
                }
            }
            if (k > 0) res -= 2 * Math.abs(numsObj[numsObj.length - 1]) * (k % 2);  // 注意处理剩余的细节
        return res;
    }
}
```

贪心思路: 尽可能把负号加到更小的数字上

#### C++

```c++
class Solution {
public:
    int largestSumAfterKNegations(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        int ans = 0;
        int minAbs = INT_MAX;
        for (int num : nums) {
            if (num < 0 && k > 0) {
                ans -= num;
                k --;
                minAbs = min(minAbs, -num);
            } else {
                ans += num;
                minAbs = min(minAbs, num);
            }
        }
        if (k > 0 && k & 1 == 1) ans -= 2 *minAbs;
        return ans;
    }
};
```



## [134. Gas Station](https://leetcode.com/problems/gas-station/)

#### C++

```c++
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int totalSum = 0; // 记录gas cost相抵消的总和, 决定是否能跑完
        int curSum = 0; // 记录从 startIdx 开始跑, gas cost 相抵的总和
        int startIdx = 0;
        for (int i = 0; i < gas.size(); i ++) {
            totalSum += gas[i] - cost[i];
            curSum += gas[i] - cost[i];
            if (curSum < 0) {
                curSum = 0;
                startIdx = i + 1; // 一旦从上一个开始点 (startIdx) 出发, 跑到当前没油了, 就要把开始点 (startIdx) 设置到下一站, 并且重置 curSum
            }
        }
        return totalSum >= 0 ? startIdx : -1;
    }
};
```



## [135. Candy](https://leetcode.com/problems/candy/)

#### Java

```java
class Solution {
    public int candy(int[] ratings) {
        int len = ratings.length;
        int[] candy = new int[len];
        candy[0] = 1;
        for (int i = 1; i < len; i ++) {
            if (ratings[i] > ratings[i - 1]) candy[i] = candy[i - 1] + 1;
            else candy[i] = 1;
        }

        for (int i = len - 2; i > -1; i --) {
            // && candy[i] <= candy[i + 1] 避免重复计算
            if (ratings[i] > ratings[i + 1] && candy[i] <= candy[i + 1]) candy[i] = candy[i + 1] + 1;
        }
        int res = 0;
        for (int c : candy) res += c;
        return res;
    }
}
```

例如, `ratings = [1,2,1]`, 那么, 在从左到右的第一轮中, 就已经达到了最优candy数列



#### C++

```c++
class Solution {
public:
    int candy(vector<int>& ratings) {
        vector<int> candies(ratings.size(), 1);
        for (int i = 1; i < candies.size(); i ++) {
            if (ratings[i] > ratings[i - 1]) candies[i] = candies[i - 1] + 1;
        }
        for (int i = candies.size() - 2; i > -1; i --) {
            if (ratings[i] > ratings[i + 1] && candies[i] <= candies[i + 1]) candies[i] = candies[i + 1] + 1;
        }
        int sum = 0; for (int c : candies) sum += c;
        return sum;
    }
};
```



## [860. Lemonade Change](https://leetcode.com/problems/lemonade-change/)

#### Java

```java
class Solution {
    public boolean lemonadeChange(int[] bills) {
        int cnt5 = 0, cnt10 = 0;
        for (int bill : bills) {
            switch (bill) {
                case 5 : cnt5 ++; break;
                case 10: {
                    cnt5 --;
                    cnt10 ++;
                    break;
                }
                case 20: {
                    if (cnt10 > 0) {
                        cnt10 --;
                        cnt5 --;
                    } else cnt5 -= 3;
                    break;
                }
            }
            if (cnt5 < 0) return false;
        }
        return true;
    }
}
```

贪心思路: 尽可能使用10$找钱

#### Rust

```rust
impl Solution {
    pub fn lemonade_change(bills: Vec<i32>) -> bool {
        let mut cnt5 = 0;
        let mut cnt10 = 0;
        for bill in bills {
            match bill {
                5 => { cnt5 += 1; },
                10 => { cnt10 += 1; cnt5 -= 1 },
                20 => {
                    if cnt10 > 0 { cnt10 -= 1; cnt5 -= 1; }
                    else { cnt5 -= 3; }
                },
                _ => (),
            };
            if cnt5 < 0 { return false; }
        }
        true
    }
}
```



## [406. Queue Reconstruction by Height](https://leetcode.com/problems/queue-reconstruction-by-height/)

#### Java

```java
class Solution {
    public int[][] reconstructQueue(int[][] people) {
        // 先按身高排序
        Arrays.sort(people, (p1, p2) -> {
            if (p1[0] == p2[0]) return p1[1] - p2[1];
            return - (p1[0] - p2[0]);
        });
        LinkedList<int[]> queue = new LinkedList<>();
        for (int[] p : people) queue.add(p[1], p);  // add(index, value)
        return queue.toArray(new int[people.length][2]); // return queue.stream().toArray(int[][]::new);
    }
}
```

贪心思路: 先按照从高到矮排 (身高一样就按照k从小到大排), 在此基础上, 每次从插入的元素, 队伍中已有的元素都比它高, 这样, 就只要考虑它的 k 值即可



## [452. Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/)

#### Java

*求最小连通交集*

```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        // Arrays.sort(points, (pa, pb) -> pa[0] - pb[0]); // 对于过大的样例, int 溢出
        Arrays.sort(points, (pa, pb) -> Integer.compare(pa[0], pb[0])); // 这样写可以避免溢出问题
        List<int[]> stk = new ArrayList<>();
        for (int[] point : points) {
            if (stk.isEmpty() || stk.getLast()[1] < point[0]) stk.add(point);
            else stk.getLast()[1] = Math.min(stk.getLast()[1], point[1]);
        }
        return stk.size();
    }
}
```



## [435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

#### Java

*求最小连通交集*

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        Arrays.sort(intervals, (ia, ib) -> Integer.compare(ia[0], ib[0]));
        List<int[]> merged = new ArrayList<>();
        Arrays.stream(intervals).forEach(interval -> {
            if (merged.isEmpty() || merged.getLast()[1] <= interval[0]) merged.add(interval);
            else merged.getLast()[1] = Math.min(merged.getLast()[1], interval[1]);
        });
        return intervals.length - merged.size();
    }
}
```



```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        Arrays.sort(intervals, (ia, ib) -> Integer.compare(ia[0], ib[0]));
        int res = 0;
        for (int i = 1; i < intervals.length; i ++) {
            int[] cur = intervals[i], pre = intervals[i - 1];
            if (cur[0] < pre[1]) {  // 重合了
                // 移除更长的那个
                if (pre[1] <= cur[1]) {  // 只有当 pre 的右端点 未超过 cur 的右端点 -> 去掉 cur; 把 cur 变成 pre
                    cur[0] = pre[0];
                    cur[1] = pre[1];
                }
                // 当 pre 的右端点 超过 cur 的右端点 -> 去掉 pre; 同时 cur 保持不变
                res ++;
            }
        }
        return res;
    }
}
```

同上, 也要注意两种情况:

```
pre = [1, 10]; cur = [2, 4]; -> remove pre
pre = [1,  4]; cur = [2, 5]; -> remove cur 
```



## [763. Partition Labels](https://leetcode.com/problems/partition-labels/)

#### Java

*求最大连通并集*

```java
class Solution {
    public List<Integer> partitionLabels(String s) {
        int[][] intervals = getEachLetterFirstAndLastAppearIntervals(s);
        Arrays.sort(intervals, (ia, ib) -> Integer.compare(ia[0], ib[0]));
        List<int[]> merged = new ArrayList<>();
        Arrays.stream(intervals)
            .forEach(interval -> {
                if (interval[0] == -1) return;
                if (merged.isEmpty() || merged.getLast()[1] < interval[0]) merged.add(interval);
                else merged.getLast()[1] = Math.max(merged.getLast()[1], interval[1]);
            });
        return merged.stream().map(interval -> interval[1] - interval[0] + 1).collect(Collectors.toList());
    }
    private int[][] getEachLetterFirstAndLastAppearIntervals(String s) {
        int[][] intervals = new int[26][2];
        Arrays.stream(intervals).forEach(interval -> Arrays.fill(interval, -1));
        for (int i = 0; i < s.length(); i ++) {
            char c = s.charAt(i);
            int idx = (int)(c - 'a');
            if (intervals[idx][0] == -1) intervals[idx][0] = i;
            intervals[idx][1] = i;
        }
        return intervals;
    }
}
```



## [56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)

*求最大连通并集*

#### Java

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        List<int[]> merged = new ArrayList<>();
        Arrays.sort(intervals, (ia, ib) -> Integer.compare(ia[0], ib[0]));
        Arrays.stream(intervals).forEach(interval -> {
            if (merged.isEmpty() || merged.getLast()[1] < interval[0]) merged.add(interval);
            else merged.getLast()[1] = Math.max(merged.getLast()[1], interval[1]);
        });
        return merged.stream().toArray(int[][]::new);
    }
}
```

#### Kotlin

```kotlin
class Solution {
    fun merge(intervals: Array<IntArray>): Array<IntArray> {
        intervals.sortWith { itv1, itv2 -> 
            if (itv1[0] != itv2[0]) itv1[0] - itv2[0]
    		else itv1[1] - itv2[1]
        }

        val ans = mutableListOf<IntArray>()
        for (itv: IntArray in intervals) {
            if (ans.isEmpty()) ans.add(itv)
            else {
                val last = ans.last()
                if (itv[0] > last[1]) ans.add(itv)
                else last[1] = maxOf(itv[1], last[1])
            }
        }
        return ans.toTypedArray()
    }
}
```



## [738. Monotone Increasing Digits](https://leetcode.com/problems/monotone-increasing-digits/)

#### Java

```
操作思路: 从高位到地位遍历数字各位, 遇到 digits[i] > digits[i + 1] 则 digits[i] --; 然后把 i 之后的 digits 全部设置为 9, 不断重复这一过程, 指导不需要再执行操作 (代码中的 digits 是反序的)

e.g.
n = 332
第一轮 329
第二轮 299

n = 100
第一轮 099

n = 3234
 -> 2999
```



```java
class Solution {
    public int monotoneIncreasingDigits(int n) {
        List<Integer> digits = new ArrayList<>();
        while (n > 0) {
            digits.add(n % 10);
            n /= 10;
        }
        while (true) {
            boolean flg = true;
            for (int i = digits.size() - 1; i > 0; i --) {
                if (digits.get(i) > digits.get(i - 1)) { // 当批量设置 9 后, 这里会产生很多重复判断
                    flg = false;
                    digits.set(i, digits.get(i) - 1);
                    for (int j = i - 1; j > -1; j --) digits.set(j, 9);
                    break;
                }
            }
            if (flg) break;
        }
        int ans = 0;
        for (int i = digits.size() - 1; i > -1; i --) {
            ans *= 10;
            ans += digits.get(i);
        }
        return ans;
    }
}
```

类似的思路

```java
class Solution {
    public int monotoneIncreasingDigits(int n) {
        StringBuilder digits = new StringBuilder(String.valueOf(n));
        int idx = digits.length(); // idx 之后 (包括 idx) 要被设置为 9
        for (int i = digits.length() - 2; i > -1; i --) { // 从后向前是关键
            if (digits.charAt(i) > digits.charAt(i + 1)) {
                digits.setCharAt(i, (char)(digits.charAt(i) - 1));
                idx = i + 1;
            }
        }
        for (int i = idx; i < digits.length(); i ++) digits.setCharAt(i, '9');
        return Integer.parseInt(digits.toString()); // Integer.valueOf(digits.toString());
    }
}
```



## [968. Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/)

#### Java

**原始写法**

对每个节点的val赋值, **0 - 未被照亮, 1 - 放了红太阳, 2 - 被照亮**

使用后续遍历, 从底部向上收集结果

贪心思路: 叶子节点**一定不**装红太阳

注意: 题目中的 root 各个节点的初始值是 0, 可以认为一开始所有节点都没有被照亮 (0 个 红太阳)

```java
class Solution {
    private int res = 0;

    private void traversal(TreeNode root) {
        if (root == null) return;
        traversal(root.left);
        traversal(root.right);
        if (root.left == null && root.right == null) return; // 叶子节点, 叶子节点不能装红太阳, 返回时刻也没有被照亮
        else if (root.left != null && root.right == null) {
            if (root.left.val == 0) {  // 如果该节点的子节点没被照亮, 那么当前节点必须装红太阳
                res ++;
                root.val = 1;  // 1 代表给当前节点装红太阳
                root.left.val = 2;  // 2 代表它的子节点可以被照到
                return;
            }
            if (root.left.val == 1) {  // 如果该节点的子节点已经装了红太阳, 那么当前节点就可以被照亮
                root.val = 2;  // 把当前节点照亮
                return;
            }
            if (root.left.val == 2) {  // 如果该节点的子节点仅仅是被照亮, 那么当前节点依然是黑暗的, 直接 return
                return;
            }
        }
        else if (root.left == null && root.right != null) {  // 处理 仅有右子树的情况同上
            if (root.right.val == 0) {
                res ++;
                root.val = 1;
                root.right.val = 2;
                return;
            }
            if (root.right.val == 1) {
                root.val = 2;
                return;
            }
            if (root.right.val == 2) {
                return;
            }
        } else {  // 左右子树都不为 null
            if (root.left.val == 0 || root.right.val == 0) {  // 如果当前节点的孩子, 有一个没有被照亮, 则当前节点要安装红太阳; 注意这一段逻辑要先行判断, 因为要消除 "一个安装了红太阳, 另一个未被照亮" 的情况, 要优先确保所有节点至少被照亮
                res ++;
                root.val = 1;  // 给当前节点装红太阳
                root.left.val = (root.left.val == 0) ? 2 : 0;  // 修改没有被照亮的子节点
                root.right.val = (root.right.val == 0) ? 2 : 0;  // 子节点有可能是一个安装了红太阳, 另一个未被照亮, 所以要用三目运算符
                return;
            }

            if (root.left.val == 1 || root.right.val == 1) {  // 如果左右节点中, 有一个是红太阳, 那么改变当前节点的状态 (2 被照亮); 注意, 在这里不可能出现 "一个安装了红太阳, 另一个未被照亮" 的情况, 因为上面的if 会消除这种可能
                root.val = 2;
                return;
            }
            // 当左右节点都是 2 (被照亮), 当前节点无法被照亮, 直接 return
            return;
        }
    }

    public int minCameraCover(TreeNode root) {
        if (root == null) return 0;
        traversal(root);
        if (root.val == 0) res ++;  // 确保root 节点不能是 0 (未被照亮的状态)
        return res;
    }
}
```



**简化代码**

```java
class Solution {
    private int res = 0;

    private int traversal(TreeNode root) {
        if (root == null) return 2; // 空节点视为已照亮，不需处理

        int left = traversal(root.left);
        int right = traversal(root.right);

        // 如果左右子节点有一个未被照亮（为0），则当前节点需要安装红太阳
        if (left == 0 || right == 0) {
            res++;
            return 1; // 当前节点安装了红太阳
        }

        // 如果左右子节点有一个安装了红太阳（为1），则当前节点被照亮
        if (left == 1 || right == 1) return 2;

        // 左右子节点均被照亮，当前节点未被照亮，返回 0 表示该节点未被照亮
        return 0;
    }

    public int minCameraCover(TreeNode root) {
        // 如果根节点返回0，意味着根节点未被照亮，需要安装一个红太阳
        if (traversal(root) == 0) res++;
        return res;
    }
}
```



```java
class Solution {
    public int minCameraCover(TreeNode root) {
        cnt = 0;
        int state = traversal(root);
        if (state == 0) cnt ++;
        return cnt;
    }
    private int cnt;
    private int traversal(TreeNode root) {
        if (root == null) return 1;
        int stateL = traversal(root.left);
        int stateR = traversal(root.right);
        if (stateL == 0 || stateR == 0) {
            cnt ++;
            return 2;
        } else if (stateL == 2 || stateR == 2) return 1;
        else return 0;
    }
}
```



#### C++

```c++
class Solution {
    // 0 - 未被照亮; 1 - 被照亮; 2 - 被安装了灯
    int traversal(TreeNode* root, int& cnt) {
        if (!root) return 1;
        int lState = traversal(root->left, cnt);
        int rState = traversal(root->right, cnt);
        if (lState == 0 || rState == 0) {
            cnt ++;
            return 2;
        }
        if (lState == 2 || rState == 2) return 1;
        return 0;
    }
public:
    int minCameraCover(TreeNode* root) {
        int cnt = 0;
        int rootState = traversal(root, cnt);
        if (rootState == 0) cnt ++;
        return cnt;
    }
};
```



# Extra

## [649. Dota2 Senate](https://leetcode.com/problems/dota2-senate/)

#### Java

使用环形数组模拟

```java
class Solution {
    public String predictPartyVictory(String senate) {
        StringBuilder senates = new StringBuilder(senate);
        for (int ture = 0; ; ture ++) {
            int idx = ture % senates.length();
            if (senates.charAt(idx) == 'X') continue;
            else if (!kill(senates, idx)) return senates.charAt(idx) == 'R' ? "Radiant" : "Dire";
        }
    }
    // 每次沿着循环数组从左往右寻找第一个对手并杀死之
    private boolean kill(StringBuilder senates, int idx) {
        for (int i = 0; i < senates.length(); i ++) {
            int counterIdx = (idx + i) % senates.length();
            if (senates.charAt(counterIdx) != 'X' && senates.charAt(idx) != senates.charAt(counterIdx)) {
                senates.setCharAt(counterIdx, 'X');
                return true;
            }
        }
        return false;
    }
}
```

更巧妙的算法

```java
class Solution {
    public String predictPartyVictory(String senateStr) {
        // R = true表示本轮循环结束后，字符串里依然有R。D同理
        boolean R = true, D = true;
        // 当flag大于0时，R在D前出现，R可以消灭D。当flag小于0时，D在R前出现，D可以消灭R
        int flag = 0;
        byte[] senate =  senateStr.getBytes();
        while (R && D) { // 一旦R或者D为false，就结束循环，说明本轮结束后只剩下R或者D了
            R = false;
            D = false;
            for (int i = 0; i < senate.length; i++) {
                switch (senate[i]) {
                    case 'R':
                        if (flag < 0) senate[i] = 0; // 消灭R，R此时为false
                        else R = true; // 如果没被消灭，本轮循环结束有R
                        flag++;
                        break;
                    case 'D':
                        if (flag > 0) senate[i] = 0;
                        else D = true;
                        flag--;
                        break;
                }
            }
        }
        // 循环结束之后，R和D只能有一个为true
        return R == true ? "Radiant" : "Dire";
    }
}
```



## [1221. Split a String in Balanced Strings](https://leetcode.com/problems/split-a-string-in-balanced-strings/)

#### Java

贪心, 已发现有合适的 balanced string 就立马收集

```java
class Solution {
    public int balancedStringSplit(String s) {
        int cntL = 0, cntR = 0, cnt = 0;
        for (char c : s.toCharArray()) {
            if (c == 'L') cntL ++;
            else cntR ++;
            if (cntL == cntR) {
                cnt ++;
                cntL = 0;
                cntR = 0;
            }
        }
        return cnt;
    }
}
```

