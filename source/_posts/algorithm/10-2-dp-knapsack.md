---
title: DP - Knapsack
date: 2024-06-12 00:00:00
tags: key-algorithm
categories: algorithm
---

---

# 约定

把 cost 和 value 统称为 stuff (物品), 使用 `stuff:[0, i]` 记号方便描述

# 0-1 Knapsack

## Origin

> 给出 n 种选择, 每种选择有对应的 cost 和 value, 在总 cost 不超过 capacity 时, 最大化 value 的总和
>
> 每种选择只能选择一次

**二维 dp**

```java
class Solution {
    public int solve01Knapsack(int[] value, int[] cost, int cap) {
        int n = cost.length;
        int[][] dp = new int[n][cap + 1]; 
        // stuff[0..=i]选择中选取, 其对应的总 cost 不超过 c 的条件下, 能达到的总 value 最大值
        // initialize
        for (int c = cost[0]; c <= cap; c++) dp[0][c] =  value[0];  // i = 0 只放 stuff[0] 的情况
        // calculate 注意计算访问, i 应该从 1 开始, 而 c 从 0 或 1 开始都行
        for (int i = 1; i < n; i ++) {  // i: 1, 2, ..., n - 1
        	for (int c = 0; c <= cap; c++) {  // c: 0, 1, 2, ..., cap
                dp[i][c] = Math.max(
                    dp[i - 1][c],  // un-include choice i
                    (c - cost[i] < 0 ) ? 0 : dp[i - 1][c - cost[i]] + value[i]  // include choice i
                );
            }
        }
        return dp[value.length - 1][cap];
    }
}

```

- 两个 for **可**交换顺序, 但最好**先 c 后 i <=> c 在内 i 在外 <=> 先 cost 后 choice**
- calculate 时, i 从 1 开始, c 从 0 或 1 开始都行

**一维 dp (滚动数组)**

```java
class Solution {
    public int solve01Knapsack(int[] value, int[] cost, int cap) {
        int n = cost.length;
        int[] dp = new int[cap + 1];
        // dp[c]: 从所有 stuff 中选取, 在总 cost 不超过 c 的条件下, 总 value 的最大值
        // initialize 全部初始化成 0
        // calculate 注意倒序; for 不能颠倒
        for (int i = 0; i < n; i ++) {
            for (int c = cap; c - cost[i] >= 0; c --) {  // cost 枚举要倒序
                dp[c] = Math.max(
                    dp[c],  // un-include choice i
                    dp[c - cost[i]] + value[i]  // include choice i
                );
            }
        }
        return dp[cap];
    }
}
```

- c 的遍历必须倒序
- for 不能颠倒: **先 c 后 i**. 原因是如果先遍历i, 后遍历 c, 会出现物品**重复添加**
- **0-1 背包的滚动数组**写法中, 一定是 **先 c 后 i <=> c 在内 i 在外**

## Var 1: Closest Sum

> **找最接近某个值的组合**
>
> 给出一个非负数组 nums 和目标值 target, 找出 nums 中, 和最接近target的值
>
> 相当于原型中 cost[i] == value[i]
>
> 每个 nums[i] 只能用 1 次

**二维 dp**

```java
class Solution {
    public int closestSum(int[] nums, int target) {
        int n = nums.length;
        int[][] dp = new int[n][target + 1];
        // dp[i][t]: 选取 nums[0..=i] 之间的数字, 其和能够达到 target (不能超过) 的最接近的值
        // initialize
        for (int t = nums[0]; t <= target; t++) dp[0][t] = nums[0]; // nums[0] 超过了 t, 故不合法, 初始化为 0
        // calculate
        for (int i = 1; i < n; i++) {
            for (int t = 1; t <= target; t++) {
                dp[i][t] = Math.max(
                    dp[i - 1][t], 
                    (t - nums[i] < 0) ? 0 : dp[i - 1][t - nums[i]] + nums[i]
                );
            }
        }
        return dp[n - 1][target];
    }
}
```

**一维 dp**

```java
class Solution {
    public int closestSum(int[] nums, int target) {
        int n = nums.length;
        int[] dp = new int[target + 1];
        // dp[t]: 选取 nums 中全部数字, 其和能够达到 target (不能超过) 的最接近的值
        // initialize 全部初始化成 0
        // calculate
        for (int i = 0; i < n; i++) {
            for (int t = target; t >= 0; t --) {
                dp[t] = Math.max(
                    dp[t], 
                    (t - nums[i] < 0) ? 0 : dp[t - nums[i]] + nums[i]
                );
            }
        }
        /* 可替换
        for (int num : nums) {
            for (int t = target; t >= 0; t--) {
                dp[t] = Math.max(
                	dp[t], 
                	(t - num < 0) ? 0 : dp[t - num] + num
                );
            }
        }
        */
        return dp[target];
    }
}
```

## Var 2: How Many Ways

> **找和等于某个值有多少种方法**
>
> 给一个非负数组 nums 和一个 target, 寻找从 nums 中选取一些数, 使其和等于 target 一共有多少种方法

**二维 dp**

```java
class Solution {
    public int howManyWays(int[] nums, int target) {
        int n = nums.length;
        int[][] dp = new int[n][target + 1];
        // dp[i][t]: 从 nums[0..=i] 中选择, 其和 **等于** t 的方法数

        // initialize
        if (target - nums[0] >= 0) dp[0][nums[0]] = 1;  // 对于只考虑第 0 个数, 只有当 t == nums[0] 时, 才有唯一的方法 (只选取 nums[0])
        int cnt0 = 0;
        for (int i = 0 ; i < n ; i++) {
            if (nums[i] == 0) cnt0++;
            dp[i][0] = (int) Math.pow(2, cnt0);
        }  // 如果 t = 0, 那么只有备选元素是 0 才可行, nums[i] == 0, 选与不选产生了 2 种情况

        // calculate
        for (int i = 1; i < n ; i++) {
            for (int t = 1; t <= target; t ++) {
                dp[i][t] = (t - nums[i] < 0) ? dp[i - 1][t] : dp[i - 1][t] + dp[i - 1][t - nums[i]];
                /**
                 * 如果 nums[i] 过大 (> t), 那么 不能选择 nums[i], dp[i][t] = dp[i - 1][t]
                 * 如果 nums[i] 不是很大, 那么
                 * dp[i][t] = dp[i - 1][t]  // 不选取 nums[i] 时的方法数
                 *          + dp[i - 1][t - nums[i]]  // 选取 nums[i] 时的方法数
                 */
            }
        }
        return dp[n - 1][target];
    }
}
```

**一维 dp**

```java
class Solution {
    public int howManyWays(int[] nums, int target) {
        int[] dp = new int[target + 1];
        // dp[t]: 可选所有 nums, 其和 == t 的方法数
        dp[0] = 1;
        for (int num : nums) {
            for (int t = target; t >= 0; t --) {
                dp[t] += (t - num < 0) ? 0 : dp[t - num];
            }
        }
        return dp[target];
    }
}
```



## Var 3: 2D Knapsack

> 同样是 n 种物品, 对应 value 数组, 但物品的约束有两个, (例如 重量 空间)
>
> 有 cost0 数组, 上限是 C0 (capacity0)
>
> 有 cost1 数组, 上限是 C1 (capacity1)

**三维 dp**

```java
class Solution {
    public int solve01Knapsack2D(int[] value, int[] cost0, int[] cost1, int cap0, int cap1) {
        int n = value.length;
        int[][][] dp = new int[n][cap0 + 1][cap1 + 1];

        // init i-axis
        // for (int i = 0; i < n; i ++) dp[i][0][0] = 0;

        // init 初始化 c0 - c1 平面
        for (int c0 = 0; c0 <= cap0; c0++) {
            for (int c1 = 0; c1 <= cap1; c1++) {
                dp[0][c0][c1] = (c0 - cost0[0] < 0 || c1 - cost1[0] < 0) ? 0 : value[0];
            }
        }

        // calculate
        for (int i = 1; i < n; i ++) {
            for (int c0 = 0; c0 <= cap0; c0 ++) {  // 注意 二维背包要从 0 开始
                for (int c1 = 0; c1 <= cap1; c1 ++) {  // 注意 二维背包要从 0 开始
                    dp[i][c0][c1] = Math.max(
                        dp[i - 1][c0][c1],  // 不放 stuff i
                        (c0 - cost0[i] < 0 || c1 - cost1[i] < 0) ? 0  // 放 stuff i
                        : dp[i - 1][c0 - cost0[i]][c1 - cost1[i]] + value[i]
                    );
                }
            }
        }
        return dp[n - 1][cap0][cap1];
    }
}
```

- c0, c1 从 0 开始的原因: 这里对于 init i 的操作是将(i, 0, 0) i = 0, ..., total - 1 一整条线初始化成 0, 而不是 i - c0 和 i - c1 两个平面
- 本题的初始化是 `平面 c0 - c1` + `线段 i-axis`

**二维 dp (滚动二维数组)**

```java
class Solution {
    public int solve01Knapsack2D(int[] value, int[] cost0, int[] cost1, int cap0, int cap1) {
        int n = value.length;
        int[][] dp = new int[cap0 + 1][cap1 + 1];

        // init 全部初始化为 0
        // calculate
        for (int i = 0; i < n; i ++) {
            for (int c0 = cap0; c0 >= 0; c0 --) {  // 倒序
                for (int c1 = cap1; c1 >= 0; c1 --) {  // 倒序
                    dp[c0][c1] = Math.max(
                        dp[c0][c1],
                        (c0 - cost0[i] < 0 || c1 - cost1[i] < 0) ? 0 
                        : dp[c0 - cost0[i]][c1 - cost1[i]] + value[i]
                    );
                }
            }
        }
        return dp[cap0][cap1];
    }
}
```

- 0-1 背包滚动数组问题, 遵循 **先 c 后 i, i 的遍历要在最外层**

# Complete Knapsack

## Origin

> 给出 n 种选择, 每种选择有对应的 cost 和 value, 在总 cost 不超过 capacity 时, 最大化 value 的总和
>
> 每种选择的次数不限

```java
class Solution {
    public int solveCompleteKnapsack(int[] value, int[] cost, int cap) {
        int n = value.length;
        int[] dp = new int[cap + 1];
        for (int i = 0; i < n; i ++) {
            for (int c = cost[i]; c <= cap; c ++) {
                dp[c] = Math.max(
                    dp[c],  // 不放 i
                    dp[c - cost[i]] + value[i]  // 放 i
                );
            }
        }
        return dp[cap];
    }
}
```

- 相当于在 0-1 背包问题的滚动数组版本中, 正序遍历 cost

- 在这种写法中, 两个 for 可以交换顺序: 因为每一个选择 (i) 允许重复使用, 而要求得的结果是最大 value, 不受物品添加顺序的影响



## Var 1: Closest Sum

>**找最接近某个值的组合**
>
>给出一个非负数组 nums 和目标值 target, 找出 nums 中, 和最接近target的值
>
>相当于原型中 cost[i] == value[i]
>
>每个 nums[i] 可以用无数次

参考滚动数组实现的 **0-1Knapsack Var 1**, 换成正序遍历 t

```java
class Solution {
    public int closestSum(int[] nums, int target) {
        int[] dp = new int[target + 1];
        for (int i = 0; i < nums.length; i++) {
            for (int t = nums[i]; t <= target; t++) {
                dp[t] = Math.max(dp[t], dp[t - nums[i]] + nums[i]);
            }
        }
        /*
        for (int num : nums) {
            for (int t = num; t <= target; t++) {
                dp[t] = Math.max(dp[t], dp[t - num] + num);
            }
        }
        */
        return dp[target];
    }
}
```

- 两个 for 可以交换顺序

## Var 2: How Many Ways

### 2-1 找和等于某个值有多少种方法 (不考虑顺序)

> 给一个非负数组 nums 和一个 target, 寻找从 nums 中选取一些数, 使其和等于 target 一共有多少种方法
>
> 这里的 nums 是不重复的数组, 里面的值可以多次使用
>
> 例如: target = 5, nums = [1, 2, 5]
>
> 5 = 1+1+1+1+1
>
> 5 = 2+1+1+1
>
> 5 = 2+2+1
>
> 5 = 5
>
> 共 4 种

参考滚动数组实现的 **0-1Knapsack Var 2**, 换成正序遍历 t

```java
public class Solution {
	public int howManyWays(int target, int[] nums) {
        int[] dp = new int[target + 1];  // 总和等于 t 的方法数量
        dp[0] = 1;
        for (int i = 0; i < nums.length; i ++) {
            for (int t = 0; t <= target; t ++) {
                dp[t] += (t - nums[i] < 0) ? 0 : dp[t - nums[i]];
                // dp[t] = dp[t] /*不放i*/ + (t - nums[i] < 0) ? 0 : dp[t - nums[i]] /*放i*/;
            }
        }
		/*
        for (int num : nums) {
            for (int t = 0; t <= target; t++) {
                dp[t] += (t - num < 0) ? 0 : dp[t - num];
            }
        }
        */
        return dp[target];
    }
}
```

- **for 必须 t 在内 i 在外 (先 t 后 i)**, 因为外层按顺序遍历 nums 确保了顺序, nums[i] **不会重复添加** (组合问题)

### 2-1 找和等于某个值有多少种方法 (考虑顺序)

> 给一个非负数组 nums 和一个 target, 寻找从 nums 中选取一些数, 使其和等于 target 一共有多少种方法
>
> 这里的 nums 是不重复的数组, 里面的值可以多次使用
>
> 例如: target = 5, nums = [1, 2, 5]
>
> [1,1,1,1,1]
>
> [1,1,1,2]
>
> [1,1,2,1]
>
> [1,2,1,1]
>
> [1,2,2]
>
> [2,1,1,1]
>
> [2,1,2]
>
> [2,2,1]
>
> [5]
>
> 共 9 种

```java
class Solution {
    public int howManyWays(int[] nums, int target) {
        int[] dp = new int[target + 1];  // 总和等于 t 的方法数量
        dp[0] = 1;
        for (int t = 0; t <= target; t ++) {
            for (int i = 0; i < nums.length; i ++) {
                dp[t] += (t - nums[i] < 0) ? 0 : dp[t - nums[i]];
                // dp[t] = dp[t] /*不放i*/ + (t - nums[i] < 0) ? 0 : dp[t - nums[i]] /*放i*/;
            }
        }
        /*
        for (int t = 0; t <= target; t ++) {
            for (int num : nums) {
                dp[t] += (t - num < 0) ? 0 : dp[t - num];
            }
        }
        */
        return dp[target];
    }
}
```

- **for 必须 i 在内 t 在外 (先 i 后 t)**, 因为外层按顺序遍历 target 确保了 nums[i] **会重复添加, 产生不同顺序**, 符合题目的要求 (排列问题)



# Dual Knapsack

二者都是求 "在总价值达到 target 时, 需要的最少 cost"

使用滚动数组, 不同滚动方向对应不同的对偶背包问题

注意, 对偶问题不能理解成 **Var 1: Closest Sum** 因为:

- 对偶问题要求最小 cost (value 之和要超过 tar)
- 而 **Var 1** 求最接近的 value (cost 之和不可超过 cap)

## Dual 0-1 Knapsack

> 给出 total 种选择, 每种选择有对应的 cost 和 value, 在总价值达到 target 时, 需要的最少 cost
>
> 每种选择只能选择一次

```java
class Solution {
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] cost = {1, 2, 3, 4};
        int[] value = {2, 2, 3, 3};
        int target = 5;
        System.out.println(s.solveDual01Knapsack(cost, value, target));  // 选 0, 2; cost = 4, value = 5
    }

    public int solveDual01Knapsack(int[] cost, int[] value, int tar) {
        int n = cost.length;
        int[] dp = new int[tar + 1];  // 达到 tar 需要的最小 cost
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;

        for (int i = 0; i < n; i++) {
            for (int v = tar; v >= value[i]; v--) {  // 逆序
                if (dp[v - value[i]] != Integer.MAX_VALUE) {
                    dp[v] = Math.min(dp[v], dp[v - value[i]] + cost[i]);
                }
            }
        }
        return dp[tar] == Integer.MAX_VALUE ? -1 : dp[tar];  // 如果没有方案，返回 -1
    }
}
```

- **for 必须先 v 后 i**, 不然会有重复选择, 不符合 0-1 背包的要求

## Dual Complete Knapsack

> 给出 total 种选择, 每种选择有对应的 cost 和 value, 在总价值达到 target 时, 需要的最少 cost
>
> 每种选择可以多次

```java
class Solution {
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] cost = {1, 2, 3, 4};
        int[] value = {2, 2, 3, 3};
        int target = 5;
        System.out.println(s.solveDualCompleteKnapsack(cost, value, target));  // 选 0*3; cost = 3, value = 6
    }

    public int solveDualCompleteKnapsack(int[] cost, int[] value, int tar) {
        int n = cost.length;
        int[] dp = new int[tar + 1];  // 达到 tar 需要的最小 cost
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;

        for (int i = 0; i < n; i++) {
            for (int v = value[i]; v <= tar; v++) {  // 顺序
                if (dp[v - value[i]] != Integer.MAX_VALUE) {
                    dp[v] = Math.min(dp[v], dp[v - value[i]] + cost[i]);
                }
            }
        }
        return dp[tar] == Integer.MAX_VALUE ? -1 : dp[tar];  // 如果没有方案，返回 -1
    }
}
```

- **for 的顺序无要求**

# Multi Knapsack

[56. 携带矿石资源](https://kamacoder.com/problempage.php?pid=1066)

```java
class Solution {
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] cost = {1,1,1,2,2,3,3,4};
        int[] value = {2,2,2,2,2,4,4,4};
        int cap = 8;
        System.out.println(s.solveMultiKnapsack(value, cost, cap));

    }

    public int solveMultiKnapsack(int[] value, int[] cost, int cap) {
        int n = value.length;
        int[] dp = new int[cap + 1];

        //先遍历物品再遍历背包，作为01背包处理
        for (int i = 0; i < n; i++) {
            for (int c = cap; c >= cost[i]; c--) {
                //遍历每种物品的个数
                for (int k = 1; k <= value[i] && (c - k * cost[i]) >= 0; k++) {
                    dp[c] = Math.max(dp[c], dp[c - k * cost[i]] + k * value[i]);
                }
            }
        }
        return dp[cap];
    }
}
```



# 例题

## [KamaCoder 46. 携带研究材料](https://kamacoder.com/problempage.php?pid=1046)

> ```
> int[] weight = {2, 2, 3, 1, 5, 2};  // 每个物品的重量
> int[] value = {2, 3, 1, 5, 4, 3};  // 每个物品的价值
> int capacity = 4;  // 最大重量限制
> ```

**暴力解法 (回溯)**

相当于枚举每种物品 在与不在 的状态, 时间复杂度 $O(2^n)$

```java
class Solution {
    private int res = Integer.MIN_VALUE;
    private boolean[] path;
    private void backtrack(int capacity, int[] weight, int[] value, int curW, int curV) {
        if (curW > capacity) return;
        res = Math.max(res, curV);
        for (int i = 0; i < path.length; i++) {
            if (path[i]) continue;
            path[i] = true;
            backtrack(capacity, weight, value, curW + weight[i], curV + value[i]);
            path[i] = false;
        }
    }

    public int solveKnapsack(int[] weight, int[] value, int capacity) {
        path = new boolean[weight.length];
        Arrays.fill(path, false);
        backtrack(capacity, weight, value, 0, 0);
        return res;
    }
}
```

**动态规划 (二维数组)**

```java
class Solution {
    public int solveKnapsack(int[] weight, int[] value, int capacity) {
        int stuffTotal = weight.length;
        int[][] dp = new int[stuffTotal][capacity + 1];
        // initialize
        for (int i = 0; i < stuffTotal; i++) dp[i][0] = 0;
        for (int c = 1; c <= capacity; c++) dp[0][c] = (c < weight[0]) ? 0 : value[0];
		
        // calculate        
        for (int i = 1; i < stuffTotal; i ++) {
            for (int c = 0; c <= capacity; c++) {
                int withoutStuffI = dp[i - 1][c];
                int withStuffI = (c - weight[i] < 0 ) ? 0 : dp[i - 1][c - weight[i]] + value[i];
                dp[i][c] = Math.max(withStuffI, withoutStuffI);
            }
        }
        return dp[value.length - 1][capacity];
    }
}
```

```txt
[[0, 0, 2, 2, 2], 
 [0, 0, 3, 3, 5], 
 [0, 0, 3, 3, 5], 
 [0, 5, 5, 8, 8], 
 [0, 5, 5, 8, 8], 
 [0, 5, 5, 8, 8]]
```

**动态规划 (一维滚动数组)**

```java
class Solution {
    public int solveKnapsack(int[] weight, int[] value, int capacity) {
        int stuffTotal = weight.length;
        int[] dp = new int[capacity + 1];
        for (int i = 0; i < stuffTotal; i ++) {
            for (int c = capacity; c - weight[i] >= 0; c --) {
                dp[c] = Math.max(dp[c], dp[c - weight[i]] + value[i]);
            }
        }
        return dp[capacity];
    }
}
```

## [416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)

**0-1 Knapsack Var 1**

- 背包的体积为sum / 2
- 背包要放入的商品（集合里的元素）重量为元素的数值，价值也为元素的数值
- 背包如果正好装满，说明找到了总和为 sum / 2 的子集。
- 背包中每一个元素是不可重复放入。

**二维 dp**

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if ((sum & 1) == 1) return false;
        int cap = sum / 2;
        int n = nums.length;
        int[][] dp = new int[n][cap + 1];
        for (int c = nums[0]; c <= cap; c ++) dp[0][c] = nums[0];
		
        // 范围不能与 initilize 时重合
        for (int i = 1; i < n; i ++ ) {
            for (int c = 1; c <= cap; c ++) {
                dp[i][c] = Math.max( dp[i-1][c] , (c - nums[i] < 0) ? 0 : dp[i-1][c - nums[i]] + nums[i] );
            }
        }
        return dp[n - 1][cap] == cap;
    }
}
```



**一维 dp**

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = Arrays.stream(nums).sum();
        if ((sum & 1) == 1) return false;
        int cap = sum / 2;
        int[] dp = new int[cap + 1];
        for (int num : nums) {
            for (int c = cap; c >= num; c--) {
                dp[c] = Math.max(dp[c], dp[c - num] + num);
            }
        }
        return dp[cap] == cap;
    }
}
```



## [1049. Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/)

**0-1 Knapsack Var 1**

```java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int sum = Arrays.stream(stones).sum();
        int cap = sum / 2;
        int n = stones.length;
        int[][] dp = new int[n][cap + 1];
        for (int c = stones[0]; c <= cap; c ++) dp[0][c] = stones[0];
        for (int i = 1; i < n; i ++) {
            for (int c = 1; c <= cap; c ++) {
                dp[i][c] = dp[i-1][c];
                if (c >= stones[i]) dp[i][c] = Math.max(dp[i-1][c], dp[i-1][c - stones[i]] + stones[i]);
            }
        }
        return Math.abs(dp[n-1][cap] * 2 - sum);
    }
}
```

与上一题类似, 都是利用动态规划, 取 `value[i] = weight[i]`, 探索最接近 target (capacity) 的情况



## [494. Target Sum](https://leetcode.com/problems/target-sum/)

**0-1 Knapsack Var 2**

```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int sum = Arrays.stream(nums).sum();
        int tar = (target + sum) / 2;
        if (sum < Math.abs(target)) return 0;
        if (tar * 2 != target + sum) return 0;
        int n = nums.length;
        int[][] dp = new int[n][tar + 1]; //使用[0..=i]中的物品填满容量 c (重量之和 == c) 的所有方法数量
        if (nums[0] <= tar) dp[0][nums[0]] = 1;
        int cnt0 = 0;
        for (int i = 0; i < n; i ++) {
            if (nums[i] == 0) cnt0 ++;
            dp[i][0] = (int) Math.pow(2, cnt0);
        }

        for (int i = 1; i < n; i ++) {
            for (int t = 1; t <= tar; t ++) {
                dp[i][t] = dp[i - 1][t];
                if (t >= nums[i]) dp[i][t] += dp[i - 1][t - nums[i]];
            }
        }
        return dp[n - 1][tar];
    }
}
```

```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int sum = Arrays.stream(nums).sum();
        int tar = (target + sum) / 2;
        if (sum < Math.abs(target)) return 0;
        if (tar * 2 != target + sum) return 0;
        int[] dp = new int[tar + 1];
        dp[0] = 1;
        for (int num : nums) {
            for (int t = tar; t >= num; t --) {
                dp[t] += dp[t - num];
            }
        }
        return dp[tar];
    }
}
```



## [474. Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/)

**0-1 Knapsack Var 3**

```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int[][] ret = foo(strs);
        int[] cost0 = ret[0];
        int[] cost1 = ret[1];
        int[][] dp = new int[m + 1][n + 1];
        int total = strs.length;
        for (int i = 0; i < total; i ++) {
            for (int c0 = m; c0 >= 0; c0 --) {
                for (int c1 = n; c1 >= 0; c1 --) {
                    dp[c0][c1] = Math.max(
                        dp[c0][c1],
                        c0 - cost0[i] < 0 || c1 - cost1[i] < 0 ? 0
                        : dp[c0 - cost0[i]][c1 - cost1[i]] + 1
                    );
                }
            }
        }
        return dp[m][n];
    }
    private int[][] foo(String[] strs) {
        int l = strs.length;
        int[] cost0 = new int[l];
        int[] cost1 = new int[l];
        for (int i = 0; i < l; i ++) {
            String str = strs[i];
            int cnt0 = 0;
            for (char c : str.toCharArray()) if (c == '0') cnt0 ++;
            cost0[i] = cnt0;
            cost1[i] = str.length() - cnt0;
        }
        int[][] ret = new int[2][l];
        ret[0] = cost0;
        ret[1] = cost1;
        return  ret;
    }
}
```



```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int[][] ret = foo(strs);
        int[] cost0 = ret[0];
        int[] cost1 = ret[1];
        int total = strs.length;
        int[][][] dp = new int[total][m + 1][n + 1];
        for (int c0 = cost0[0]; c0 <= m; c0 ++) { // 初始化 c0 - c1 平面
            for (int c1 = cost1[0]; c1 <= n; c1 ++) {
                dp[0][c0][c1] = 1;
            }
        }

        for (int i = 1; i < total; i ++) {
            for (int c0 = 0; c0 <= m; c0 ++) {
                for (int c1 = 0; c1 <= n; c1 ++) {
                    dp[i][c0][c1] = Math.max(
                        dp[i - 1][c0][c1], 
                        (c0 - cost0[i] < 0 || c1 - cost1[i] < 0) ? 0 
                        : dp[i - 1][c0 - cost0[i]][c1 - cost1[i]] + 1 /* value[i] === 1 */
                    );
                }
            }
        }
        return dp[total - 1][m][n];
    }
    private int[][] foo(String[] strs) {
        int l = strs.length;
        int[] cost0 = new int[l];
        int[] cost1 = new int[l];
        for (int i = 0; i < l; i ++) {
            String str = strs[i];
            int cnt0 = 0;
            for (char c : str.toCharArray()) if (c == '0') cnt0 ++;
            cost0[i] = cnt0;
            cost1[i] = str.length() - cnt0;
        }
        int[][] ret = new int[2][l];
        ret[0] = cost0;
        ret[1] = cost1;
        return  ret;
    }
}
```



## [KamaCoder 52. 携带研究材料](https://kamacoder.com/problempage.php?pid=1052)

**Complete Knapsack**



## [518. Coin Change II](https://leetcode.com/problems/coin-change-ii/)

**Complete Knapsack Var 2-1** 

```java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;
        for (int coin : coins) {
            for (int a = 0; a <= amount; a ++) {
                dp[a] += (a - coin < 0) ? 0 : dp[a - coin];
            }
        }
        return dp[amount];
    }
}
```

## [377. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)

**Complete Knapsack Var 2-2** 

```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int t = 0; t <= target; t ++) {
            for (int num : nums) {
                dp[t] += t - num < 0 ? 0 : dp[t - num];
            }
        }
        return dp[target];
    }
}
```

###### Rust

**回溯**超时

```rust
impl Solution {
    pub fn combination_sum4(nums: Vec<i32>, target: i32) -> i32 {
        let mut ans = 0;
        Solution::backtrack(&mut 0, &mut ans, target, &nums);
        ans
    }

    fn backtrack(
        // idx: usize,
        sum: &mut i32,
        ans: &mut i32,
        tar: i32,
        nums: &Vec<i32>,
    ) {
        if *sum > tar { return; }
        if *sum == tar {
            *ans += 1;
            return;
        }
        for num in nums.iter() {
            *sum += *num;
            Solution::backtrack(sum, ans, tar, nums);
            *sum -= *num;
        }
    }
}
```

**DP**

```rust
impl Solution {
    pub fn combination_sum4(nums: Vec<i32>, target: i32) -> i32 {
        let mut dp = vec![0; target as usize + 1];
        dp[0] = 1;
        for t in 0..=target {
            for i in 0..nums.len() {
                dp[t as usize] += if t - nums[i] < 0 {
                    0
                } else {
                    dp[(t - nums[i]) as usize]
                };
            }
        }
        dp[target as usize]
    }
}
```





## [KamaCoder 57. 爬楼梯](https://kamacoder.com/problempage.php?pid=1067)

> 假设你正在爬楼梯。需要 n 阶你才能到达楼顶。 
>
> 每次你可以爬至多m (1 <= m < n)个台阶。你有多少种不同的方法可以爬到楼顶呢？ 

**Complete Knapsack Var 2-2** 

target = n, nums = [1, 2, 3, ..., m]

```java
class Solution {
    public int climbStairs(int n, int m) {
        int[] nums = new int[m];
        for (int i = 0; i < m; i ++) nums[i] = i + 1;
        
        int[] dp = new int[n + 1];
        dp[0] = 1;
        
        for (int _n = 0; _n <= n; _n ++) {
            for (int num : nums) {
                dp[_n] += (_n < num) ? 0 : dp[_n - num];
            }
        }
        return dp[n];
    }
}
```

**递归法**

```java
public int climbStairs(int n, int m) {
        if (m == 1) return 1;
        if (n <= 1) return 1;
        int methodsCnt = 0;
        for (int i = 1; i <= m; i ++) {
            methodsCnt += climbStairs(n - i, Math.min(n - i, m));  // 关键
        }
        return methodsCnt;
    }
```

```
当 m = 2 时, f(n, 2) = f(n - 1, 2) + f(n - 2, 2)
故有递归公式 f(n, m) = f(n - 1, m) + ... + f(n - m, m)
但是, 当 n < m 之后, 第二个参数会退化成 n, 例如 f(2, 3) = f(2, 2)
```

也可以这样写

```java
public class Solution {
	public int climbStairs(int n, int m) {
        if (n < m) m = n;  // 关键
        if (m == 1) return 1;
        if (n <= 1) return 1;
        int methodsCnt = 0;
        for (int i = 1; i <= m; i ++) {
            methodsCnt += climbStairs(n - i, m);
        }
        return methodsCnt;
    }
}
```



## [322. Coin Change](https://leetcode.com/problems/coin-change/)

**Dual Complete Knapsack**

```
amount <=> V
coins <=> value
[1, 1, ..., 1] <=> cost
```

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int n = coins.length;
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
        for (int i = 0; i < n; i ++) {
            for (int a = coins[i]; a <= amount; a ++) {
                if (dp[a - coins[i]] != Integer.MAX_VALUE) {
                    dp[a] = Math.min(
                        dp[a],
                        dp[a - coins[i]] + 1
                    );
                }
            }
        }
        return dp[amount] == Integer.MAX_VALUE ? -1 : dp[amount];
    }
}
```

###### Rust

```rust
impl Solution {
    pub fn coin_change(coins: Vec<i32>, amount: i32) -> i32 {
        let total: usize = coins.len();
        let mut dp: Vec<i32> = vec![i32::MAX; amount as usize + 1];
        dp[0] = 0;
        for i in 0..total {
            for a in coins[i]..=amount {
                if dp[(a - coins[i]) as usize] != i32::MAX {
                    dp[a as usize] = dp[a as usize].min(dp[(a - coins[i]) as usize] + 1);
                }
            }
        }
        if dp[amount as usize] == i32::MAX { -1 } else { dp[amount as usize] }
    }
}
```



## [279. Perfect Squares](https://leetcode.com/problems/perfect-squares/)

**Dual Complete Knapsack**

```
n <=> V
nums <=> value
[1, 1, ..., 1] <=> cost
```

```java
class Solution {
    public int numSquares(int n) {
        int[] value = getValue(n);
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
        for (int i = 0; i < value.length; i ++) {
            for (int v = value[i]; v <= n; v ++) {
                if (dp[v - value[i]] != Integer.MAX_VALUE) {
                    dp[v] = Math.min(dp[v], dp[v - value[i]] + 1);
                }
            }
        }
        return dp[n];
    }

    private int[] getValue(int n) {
        List<Integer> value = new ArrayList<>();
        for (int i = 1; i <= n; i ++) {
            if (i * i > n) break;
            value.add(i * i);
        }
        return value.stream().mapToInt(Integer::intValue).toArray();
    }
}
```



## [139. Word Break](https://leetcode.com/problems/word-break/)

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        boolean[] dp = new boolean[s.length() + 1]; // dp[l] 表示 s.substring(0, l) (长度为 l 的子串) 是否可由 wordDict 组成; dp[0] 表示空字符串的情况 (true); dp[s.length()] 为结果
        dp[0] = true;
        for (int l = 0; l <= s.length(); l ++) {
            for (String word : wordDict) {
                if (l < word.length()) continue;
                String sub = s.substring(l - word.length(), l);
                dp[l] = dp[l] || ( dp[l - word.length()] && sub.equals(word) );
            }
        }
        return dp[s.length()];
    }
}
```

- **for 必须 先 l 后 word**: 不然的话, 无法确保遍历所有的word查看它们是否有等于sub的 (**需要重复添加** word)

```java
// 另一种思路的背包算法
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        boolean[] dp = new boolean[s.length() + 1];
        dp[0] = true;

        for (int i = 1; i <= s.length(); i++) {
            for (String word : wordDict) {
                int len = word.length();
                if (i >= len && dp[i - len] && word.equals(s.substring(i - len, i))) {
                    dp[i] = true;
                    break;  // 巧妙, 当匹配到一个 word 后就不用继续匹配了
                }
            }
        }
        return dp[s.length()];
    }
}
```

**普通DP**

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        HashSet<String> set = new HashSet<>(wordDict);
        boolean[] valid = new boolean[s.length() + 1];
        valid[0] = true;

        for (int i = 1; i <= s.length(); i++) {
            for (int j = 0; j < i && !valid[i]; j++) {
                if (set.contains(s.substring(j, i)) && valid[j]) {
                    valid[i] = true;
                }
            }
        }

        return valid[s.length()];
    }
}
```
