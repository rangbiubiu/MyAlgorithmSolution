----

# 目录

[TOC]



# :triangular_flag_on_post: 需要注意的点

## 子串子序列问题

1. [关于dp无后效性以及一般线性数据结构的dp[i]定义为什么要是以i为结尾的···](https://leetcode.cn/problems/maximum-subarray/solutions/9058/dong-tai-gui-hua-fen-zhi-fa-python-dai-ma-java-dai)

## 背包问题

### 1. dp定义

1. 二维dp数组的dp.size()初始化为n还是n+1都行，也就是说，dp\[i][j]定义是：下标[0,i-1]的元素能装满容量为j的背包的···，还是下标[0,i]的元素能装满容量为j的背包的···这两种都行。但是==通常初始化为 n+1 时第3步dp数组初始化的逻辑会简单很多。==
2. 当dp\[i][j]定义是：下标[0,**i-1**]的元素能装满容量为j的背包的···时，注意第4步for遍历中要用`nums[i-1]`表示当前物品，而不是`nums[i]`。（经常因为忘记这个报越界错）

### 2. 递推公式

### 3. 初始化

- 要想清楚i=0以及j=0时需不需要单独拿出来初始化

### 4. for遍历

1. 遍历顺序：一般在**状态压缩dp为滚动数组**时，有时候需要尽可能避免覆盖，这时遍历顺序就有讲究。

2. for循环嵌套的顺序：

    - 在**选取物品讲究顺序**，即**求的是排列而非组合**时，for循环嵌套的顺序就不是任意的了，此时必须是==外层遍历背包，内层遍历物品。==

        > 举例：
        >
        > 比如爬楼梯，为什么求的是排列？因为同样是爬3级，先爬两级再爬一级，和先爬一级再爬两级是不同的爬法。
        >
        > "每次可以最多可以爬 m 阶，爬n阶有多少种方法？"就是完全背包问题，背包容量即n阶楼梯，物品即1\~m阶，问题转换为"有体积为1\~m的物品，和一个容量为n的背包，这些物品每次都可以重复选，装满这个背包有多少种方法？"。
        >
        > 如果外层遍历物品，内层遍历背包，那么计算dp[4]的时候，结果集只有 {1,3} 这样的集合，不会有{3,1}这样的集合，因为物品遍历放在外层，3只能出现在1后面！

3. 二维dp和一维dp在这一步中要注意：

- 二维的内层循环应该写成这样：

```cpp
for (int j = 1; j <= amount; j++) {
    if (j >= coins[i]) { 
        dp[i][j] = min(dp[i - 1][j], dp[i][j - coins[i]] + 1); 
    }  else { 
        dp[i][j] = dp[i - 1][j];
    }
}
```

- 而不能跟一维一样写成这样：

```cpp
for (int j = coins[i - 1]; j <= amount; ++j) { 
    dp[i][j] = min(dp[i - 1][j], dp[i][j - coins[i - 1]] + 1);
}
```

- 因为如果写成这样，那么对于容量 < coins[i - 1]的背包，就无法计算到dp值。

>比如coins={1,2,5}, amount=11时，
>
>i=2,j=1时, dp\[2][1]表示 use [0,1] to get amount = 1, 此时如果按这样写，由于背包容量1<coins[1], 因此就不会计算dp\[2][1]，仍为无穷大。实际上dp\[2][1]=1

- 而一维的是滚动刷新的，如果容量 < coins[i - 1]，那就不刷新即可，即还是滚动前的值（本来也装不了这个物品）。



# 基础问题

## :fire:[70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

### 题意

假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。

每次你可以爬 `1` 或 `2` 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**示例 1：**

```
输入：n = 2
输出：2
解释：有两种方法可以爬到楼顶。
1. 1 阶 + 1 阶
2. 2 阶
```

**示例 2：**

```
输入：n = 3
输出：3
解释：有三种方法可以爬到楼顶。
1. 1 阶 + 1 阶 + 1 阶
2. 1 阶 + 2 阶
3. 2 阶 + 1 阶
```

**提示：**

- `1 <= n <= 45`

### 代码

==:star: **核心思路：dp**:star:==

关键是初始化。

```c++
int climbStairs(int n) {
    if (n < 2) return 1; //不然后面dp[2]会越界
    // 1. dp[i]表示爬i阶有多少种方法
    vector<int> dp(n + 1);
    // 2. 递推公式: dp[i] = dp[i - 1] + dp[i - 2]
    // 3. dp数组初始化
    dp[1] = 1;
    dp[2] = 2;
    for (int i = 3; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n];
}
```

状态压缩

```c++
int climbStairs(int n) {
    if (n < 2) return 1;
    int dp1 = 1; //爬1阶楼梯的方法数
    int dp2 = 2; //爬2阶楼梯的方法数
    for (int i = 3; i <= n; i++) {
        int dpi = dp1 + dp2; //爬i阶楼梯的方法数，
        dp1 = dp2;
        dp2 = dpi;
    }
    return dp2;
}       
```

**复杂度分析**

- 时间复杂度：O(n)  

- 空间复杂度：O(n) / O(1)

### :star:拓展变形

1. 要求输出每条路径。

2. 给你两个**正整数** k1 和 k2 ，每次你可以爬 k1 或 k2 个台阶。有多少种爬法？

3. 如果每次可以爬 1 或 2 或 3 个台阶呢？有多少种爬法？

4. 给你一个**正整数**数组nums， 每次可以爬nums中任意一个数nums[i]个台阶。有多少种爬法？

    > 完成了4 后，很容易发现：2 是 4 在 nums.size()=2 条件下的特例；3 是 4 在 nums=[1,2,3] 条件下的特例。

5. 如果每次可以最多可以爬 m 阶，有多少种爬法？

#### 1.输出每条路径。

```cpp
class Solution {
public:
    vector<vector<int>> ans; //所有爬法的路径
    vector<int> path; //一种爬法的路径
    int res = 0; //有多少种爬法

    void dfs(int curStair, int n) {
        // 当前所处阶梯为第n阶时, 收集结果并return
        if (curStair == n) {
            ans.push_back(path);
            res++;
            return;
        }
        // 先爬一阶
        path.push_back(curStair + 1);
        dfs(curStair + 1, n); 
        path.pop_back();// 回溯
        // 再爬两阶 (如果能爬两阶)
        if (curStair + 2 <= n) {
            path.push_back(curStair + 2);
            dfs(curStair + 2, n);
            path.pop_back();// 回溯
        }
    }
    int climbStairs(int n) {
        // 从第0阶开始爬, 直到爬到第n阶
        dfs(0, n);
        // 打印所有爬法的路径
        cout << "ans = [";
        for (int i = 0; i < ans.size(); i++) {
            cout << "[";
            for (int j = 0; j < ans[i].size(); j++) {
                cout << ans[i][j];
                if (j != ans[i].size() - 1) {
                    cout << ", ";
                }
            }
            cout << "]";
            if (i != ans.size() - 1) {
                cout << ", ";
            }
        }
        cout << "]" << endl;
        return res;
    }
};
```

#### 2.每次可以爬 k1 或 k2 个台阶 (k1,k2是给定值)

```cpp
int climbStairs(int n, int k1, int k2) {
    vector<int> dp(n + 1);
    dp[0] = 1;
    for (int i = 0; i <= n; i++) {
        dp[i] += (i >= k1 ? dp[i - k1] : 0) + (i >= k2 ? dp[i - k2] : 0);
    }
    return dp[n];
}
```

#### 3.每次可以爬 1 或 2 或 3 个台阶

```cpp
int climbStairs(int n) {
    if (n <= 2) return n; //不然后面dp[2]会越界
    // 1. dp[i]表示爬i阶有多少种方法
    vector<int> dp(n + 1);
    // 2. 递推公式: dp[i] = dp[i - 1] + dp[i - 2] + dp[i - 3]
    // 3. dp数组初始化
    dp[1] = 1;
    dp[2] = 2;
    dp[3] = 4;//1+2,3,2+1,1+1+1
    for (int i = 4; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2] + dp[i - 3];
    }
    return dp[n];
}
```

#### 4.给你一个**正整数**数组nums， 每次可以爬nums中任意一个数nums[i]个台阶。有多少种爬法？

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    int climbStairs(int n, vector<int>& steps) {
        vector<int> dp(n + 1);
        dp[0] = 1;
        for (int i = 0; i <= n; i++) {
            for (int step : steps) {
                if (step > i) continue;
                dp[i] += dp[i - step];
            }
        }
        return dp[n];
    }
};
int main() {
    vector<int> steps = { 1, 2 }; // 可以扩展成任意级台阶
    Solution solution;
    cout << solution.climbStairs(20, steps)  << endl;//10946
    return 0;
}
```

#### 5.每次可以最多可以爬 m 阶

完全背包问题。问题可以转换为：有体积为1~m的物品，和一个容量为n的背包，这些物品每次都可以重复选，装满这个背包有多少种方法？

注意for循环嵌套的顺序！！！ 

- 在**选取物品讲究顺序**，即**求的是排列而非组合**时，for循环嵌套的顺序就不是任意的了，此时必须是==外层遍历背包，内层遍历物品。==

为什么这里求的是排列？因为同样是爬3级，先爬两级再爬一级，和先爬一级再爬两级是不同的爬法。

如果外层遍历物品，内层遍历背包，那么计算dp[4]的时候，结果集只有 {1,3} 这样的集合，不会有{3,1}这样的集合，因为物品遍历放在外层，3只能出现在1后面！

```cpp
int climbStairs(int n, int m) {
    // dp[i] 表示爬到第 i 阶楼梯有多少种方法
    vector<int> dp(n + 1, 0);
    // 递推：每次可以选择爬1~m阶，设选择爬k阶，选完后剩下i-k阶有多少种爬法，就加到dp[i]中 
    // 初始化
    dp[0] = 1;
    // 遍历: 因为，这里必须是外层遍历背包，内层遍历物品
    for (int i = 1; i <= n; i++) { //背包容量
        for (int k = 1; k <= m; k++) { //物品体积
            if (k <= i) { 
                dp[i] += dp[i - k];
            }
        }
    }
    return dp[n];
}
```

# 子串子序列问题


## :fire:[1143. 最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)

### 题意

给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 **公共子序列** 的长度。如果不存在 **公共子序列** ，返回 `0` 。

一个字符串的 **子序列** 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

- 例如，`"ace"` 是 `"abcde"` 的子序列，但 `"aec"` 不是 `"abcde"` 的子序列。

两个字符串的 **公共子序列** 是这两个字符串所共同拥有的子序列。

**示例 1：**

```
输入：text1 = "abcde", text2 = "ace" 
输出：3  
解释：最长公共子序列是 "ace" ，它的长度为 3 。
```

**示例 2：**

```
输入：text1 = "abc", text2 = "abc"
输出：3
解释：最长公共子序列是 "abc" ，它的长度为 3 。
```

**示例 3：**

```
输入：text1 = "abc", text2 = "def"
输出：0
解释：两个字符串没有公共子序列，返回 0 。
```

**提示：**

- `1 <= text1.length, text2.length <= 1000`
- `text1` 和 `text2` 仅由小写英文字符组成。

### 思路

==:star: **核心思路：dp   **:star:==

**:small_red_triangle_down:  steps: **

![SmartSelect_20240228_205705_Samsung Notes](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202402281654911.jpg)

### 代码

```c++
class Solution {
public:
    int longestCommonSubsequence(string s1, string s2) {
        int m = s1.size() + 1;
        int n = s2.size() + 1;
        // dp[i][j]表示s1[0,i-1]和s2[0,j-1]的最长公共子序列的长度
        vector<vector<int>> dp(m, vector<int>(n));
        // dp数组初始化：i=0和j=0时都为0，不用单独初始化
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (s1[i - 1]  == s2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]); 
                }
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

状态压缩

```c++
class Solution {
public:
    int longestCommonSubsequence(string s1, string s2) {
        // dp[i][j]表示s1[0,i-1]和s2[0,j-1]的最长公共子序列的长度
        int m = s1.size() + 1;
        int n = s2.size() + 1;
        vector<int> dp(n);
        // 要用到 左上dp[i - 1][j - 1]，左dp[i][j - 1]，上dp[i - 1][j]的值
        for (int i = 1; i < m; ++i) {
            int leftUp = 0; //初始化leftUp，每行刚开始的时候都是0
            for (int j = 1; j < n; ++j) {
                // 暂存未刷新的dp[i-1][j]作为下一次循环(dp[j+1])的左上角值
                int tmp = dp[j]; 
                // 该次循环后dp[j]被刷新, 从原来的dp[i-1][j]刷新为了dp[i][j]
                if (s1[i - 1] == s2[j - 1]) {
                    dp[j] = 1 + leftUp;
                } else {
                    dp[j] = max(leftUp, max(dp[j], dp[j - 1]));
                }
                // 更新左上角的值
                leftUp = tmp; 
            }
        }
        return dp[n - 1];
    }
};
```

### 复杂度分析

- 时间复杂度：O(m*n)  

- 空间复杂度：O(m*n) / O(1)

## :fire:[718. 最长重复子数组](https://leetcode.cn/problems/maximum-length-of-repeated-subarray/)

### 题意

给两个整数数组 `nums1` 和 `nums2` ，返回 *两个数组中 公共的 、长度最长的子数组的长度* 。

 **示例 1：**

```
输入：nums1 = [1,2,3,2,1], nums2 = [3,2,1,4,7]
输出：3
解释：长度最长的公共子数组是 [3,2,1] 。
```

**示例 2：**

```
输入：nums1 = [0,0,0,0,0], nums2 = [0,0,0,0,0]
输出：5
```

 **提示：**

- `1 <= nums1.length, nums2.length <= 1000`
- `0 <= nums1[i], nums2[i] <= 100`

### 参考题解

1. [滑动窗口解法可以参考的题解，看上去还不错](https://leetcode.cn/problems/maximum-length-of-repeated-subarray/solutions/28583/wu-li-jie-fa-by-stg-2)

### 法一

==:star: **核心思路：dp   **:star:==

和1143不同，1143求的是公共子序列（不要求连续），本题是公共子数组（要求连续）。

因此这里计算dp\[i][j]就只需要看nums1[i-1]和nums2[j-1]这两个当前位置是否相等，如果相等则dp\[i][j]就看左上格子dp\[i-1][j-1]为多少。

也就是说，本题同1143不同，本题最多只需要关注一个格子（左上），左、上两个格子都不需要关注。

```c++
int findLength(vector<int>& nums1, vector<int>& nums2) {
    // dp定义: dp[i][j]表示以i-1下标结尾的nums1和以j-1下标结尾的nums2的最长公共子数组的长度
    // 这样定义的好处是不需要单独对dp数组进行初始化，而可以合并到遍历逻辑中
    int m = nums1.size();
    int n = nums2.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1));
    /* 递推公式
           1, 2, 3, 2, 1
        3   
        2
        1
        4
        7
    */ 
    int maxLen = 0;
    // 遍历
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (nums1[i - 1] == nums2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
                maxLen = max(maxLen, dp[i][j]);
            }
        }
    }
    return maxLen;
}
```

状态压缩

```cpp
int findLength(vector<int>& nums1, vector<int>& nums2) {
    int m = nums1.size();
    int n = nums2.size();
    vector<int> dp(n + 1); 
    int maxLen = 0;
    // 需要左上格子的值, 即左边格子更新前的值(从右往左更新即保证了遍历到dp[j]时dp[j-1]还未更新，即dp[j-1]除了是左边的值之外，还是上一层的值，因此要从右往左从上往下遍历
    for (int i = 1; i <= m; ++i) {
        for (int j = n; j >= 1; --j) {
            dp[j] = nums1[i - 1] == nums2[j - 1] ? dp[j - 1] + 1 : 0; // 注意这里不相等的时候一样要更新值（赋0）
            maxLen = max(maxLen, dp[j]);
        }
    }
    return maxLen;
}
```

### 法二

==:star: **核心思路：滑动窗口   **:star:==

待补充。。。

## :fire:[32. 最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/)

### 题意

给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号子串的长度.

**示例 1：**

```
输入：s = "(()"
输出：2
解释：最长有效括号子串是 "()"
```

**示例 2：**

```
输入：s = ")()())"
输出：4
解释：最长有效括号子串是 "()()"
```

**示例 3：**

```
输入：s = ""
输出：0
```

**提示：**

- `0 <= s.length <= 3 * 104`
- `s[i]` 为 `'('` 或 `')'`

### 参考题解

1. [zhanganan0425](https://leetcode.cn/problems/longest-valid-parentheses/solutions/206995/dong-tai-gui-hua-si-lu-xiang-jie-c-by-zhanganan042)

### 法一

==:star: **核心思路：dp   **:star:==

 ![SmartSelect_20240305_150847_Samsung Notes](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403051019535.jpg)

#### 代码

```c++
int longestValidParentheses(string s) {
    int maxLen = 0;
    // 1、确定dp数组以及下标含义: dp[i]表示以i为结尾的最长有效括号子串的长度 
    vector<int> dp(s.size());
    // 2、确定递推公式
    // 3、dp数组初始化 
    // 4、遍历顺序
    for (int i = 1; i < s.size(); ++i) {
        if (s[i] == ')') {
            if (s[i - 1] == '(') {
                dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
            } else if (i - dp[i - 1] > 0 && s[i - dp[i - 1] - 1] == '(') {
                // 必须要有与s[i]配对的'(', 即s[i - dp[i - 1] - 1]=='(', dp[i]才能继续算下去
                // 否则由于下面的式子中并不会涉及到i - dp[i - 1] - 1这个位置，因此如果该位置为')',
                // dp[i]仍会按该位置为'('去算
                dp[i] = dp[i - 1] + 2 + (i - dp[i - 1] >= 2 ? dp[i - dp[i - 1] - 2] : 0); 
                //      "((..))"的长度 +  "((..))"前面的配对括号序列的长度
            }
        }
        maxLen = max(maxLen, dp[i]);
    }
    return maxLen;
}
```

## :fire:[53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

### 题意

给你一个整数数组 `nums` ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**子数组** 是数组中的一个连续部分。

**示例 1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**示例 2：**

```
输入：nums = [1]
输出：1
```

**示例 3：**

```
输入：nums = [5,4,-1,7,8]
输出：23
```

**提示：**

- `1 <= nums.length <= 105`
- `-104 <= nums[i] <= 104`

**进阶：**如果你已经实现复杂度为 `O(n)` 的解法，尝试使用更为精妙的 **分治法** 求解。

### 法一

==:star: **核心思路： 贪心  **:star:==

**:key:  key：**

- 当前"连续和"为负数的时候立刻放弃，从下一个元素重新计算连续和，因为负数加上下一个元素"连续和"只会越来越小。![image-20240131143607389](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202401311436522.png)

#### 代码

```c++
int maxSubArray(vector<int>& nums) {
    // 贪心
    // 当前"子数组和"为负数的时候立刻放弃，从下一个元素重新计算和，
    // 因为负数加上下一个元素"子数组和"只会越来越小。
    // 但是注意!!!!!如果最大子数组和都是负数, 那么就不能放弃!
    int maxSum = 0xc0c0c0c0; 
    int sum = 0;
    for (auto& num : nums) {
        sum += num;
        maxSum = max(maxSum, sum);
        if (sum < 0) {
            sum = 0;
        } 
    }
    return maxSum;
}
```

#### 复杂度分析

- 时间复杂度：O(n)  

- 空间复杂度：O(1)

### 法二

==:star: **核心思路：dp   **:star:==

##### 无状态压缩

```c++
int maxSubArray(vector<int>& nums) { 
    int maxSum = nums[0];//不能初始化为INT_MIN,否则当数组只有一个元素时会返回INT_MIN
    //1.确定dp数组及其下标含义:dp[i]表示以下标i为结尾的连续子数组的最大和 
    vector<int> dp(nums.size()); 
    //2.dp递推公式: dp[i] = max(dp[i - 1] + nums[i], nums[i]);//子数组延续到i 或者 子数组不延续了,重新从i开始
    //3.数组初始化
    dp[0] = nums[0];
    //4.遍历
    for (int i = 1; i < nums.size(); ++i) {
        dp[i] = nums[i] + max(dp[i - 1], 0);
        maxSum = max(dp[i], maxSum); 
    }
    return maxSum;
}
```

##### 状态压缩

```c++
int maxSubArray(vector<int>& nums) {
    int maxSum = nums[0];
    int dp0 = nums[0];
    for (int i = 1; i < nums.size(); ++i) {
        dp0 = max(dp0 + nums[i], nums[i]);
        maxSum = max(dp0, maxSum); 
    }
    return maxSum;
}
```

#### 复杂度分析

- 时间复杂度：O(n)  

- 空间复杂度：不压缩就是O(n)，压缩为O(1)

### 法三

==:star: **核心思路：分治法 **:star:==

![image-20240131143710540](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202401311437709.png)

#### 代码

```c++
class Solution {
public:
    struct Status { //区间要计算的四个和
        int lSum,rSum,mSum,iSum;
        // lSum表示 [l,r]内以 l 为左端点的最大子段和
        // rSum表示 [l,r]内以 r 为右端点的最大子段和
        // mSum表示 [l,r]的最大子段和
        // iSum表示 [l,r]的区间和
    };
    Status Merge(Status A, Status B) { //合并左右区间
        int iSum = A.iSum + B.iSum;
        int lSum = max(A.lSum, A.iSum + B.lSum);
        int rSum = max(B.rSum, B.iSum + A.rSum);
        int mSum = max(max(A.mSum, B.mSum), A.rSum + B.lSum);//不跨越、跨越
        return (Status) {lSum, rSum, mSum, iSum};
    }
    Status get(vector<int>& nums, int l, int r) {
        //1.递归结束条件：当递归逐层深入直到区间长度缩小为 1 的时候，递归「开始回升」
        if (l == r) return (Status) {nums[l], nums[l],nums[l],nums[l]};
        //2.分治
        int m = (l + r) >> 1;//右移一位即 /2
        Status lSub = get(nums, l, m);//区间[l,m]
        Status rSub = get(nums, m + 1, r);//区间[m+1,r]
        //3.合并左右区间
        return Merge(lSub, rSub);
    }
    int maxSubArray(vector<int>& nums) {
        return get(nums, 0, nums.size() - 1).mSum;
        // 得到区间[0,nums.size()-1]的最大子段和
    }
};
```

#### 复杂度分析

- 时间复杂度：O(n)  。把递归的过程看作是一颗二叉树的先序遍历，总时间就相当于遍历这颗二叉树的所有节点 

- 空间复杂度：O(logn)。递归会使用 O(log⁡n)的栈空间



## [:fire:300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

### 题意

:  给你一个整数数组 `nums` ，找到其中最长**严格**递增**子序列**的长度。

**进阶：**你能将算法的时间复杂度降低到 `O(n log(n))` 吗?

```c++
示例 1：
输入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]（或者说[2,5,7,101]），因此长度为 4 。
```

### 参考题解

1. [liweiwei佬的gif图把过程模拟的很清晰](https://leetcode.cn/problems/longest-increasing-subsequence/solutions/7196/dong-tai-gui-hua-er-fen-cha-zhao-tan-xin-suan-fa-p)

### 总

```cpp
int findFirst(vector<int>& nums, int left, int right, int target) {
    while (left < right) {
        int mid = (left + right) / 2;
        if (nums[mid] >= target) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }
    return left;//因为一定找得到
}
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
/*  ========= 法一、贪心+前缀和 =========
    用一个cell数组，我们用这个数组的长度来代表最长递增上升子序列的长度（cell未必是真实的最长上升子序列，但长度是对的）
    先将nums[0]加到cell数组中，然后遍历nums，
    如果当前遍历元素nums[i]>cell.back()，则直接将nums[i] push_back到cell
    否则二分找到cell中第一个比它大的元素，将其覆盖.
    （这样，虽然最长递增子序列的长度不变，但是它之后能容纳更多的元素）
    时间复杂度：O(nlogn), 空间复杂度：O(n)
*/
    vector<int> cell;
    cell.emplace_back(nums[0]);
    for (int i = 0; i < n; ++i) {
        if (nums[i] > cell.back()) {
            cell.emplace_back(nums[i]);
        } else {
            // 若自己实现:
            int index = findFirst(cell, 0, cell.size() - 1, nums[i]);
            // 也可以用lower_bound库函数，注意它的参数以及返回值:
            // int index = lower_bound(cell.begin(), cell.end(), nums[i]) - cell.begin();
            cell[index] = nums[i];
        }
    }
    return cell.size();

/*  ========= 法二、dp =========
    时间复杂度：O(n^2), 空间复杂度：O(n)
*/
    int maxlen = 1;
    // 1. dp定义：dp[i]表示由下标i结尾的最长递增子序列的长度
    vector<int> dp(n, 1);
    // 3. dp初始化: 定义即初始化了
    // 4. 遍历
    for (int i = 1; i < n; ++i) {
        for (int j = 0; j < i; j++) {
            if (nums[i] > nums[j]) {
                dp[i] = max(dp[i], dp[j] + 1); 
            }
        }
        maxlen = max(maxlen, dp[i]);
    }
    return maxlen;
}
```



### 法一

==:star: **核心思路： dp  **:star:==

初始化容易错！！一开始就要全初始化为1  ！！

#### 代码

```c++
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    int maxLen = 1;
    // dp[i]表示以下标i为结尾的最长严格递增子序列的长度
    vector<int> dp(n, 1);
    // 初始化:任何一个单独的元素都是一个长为1的递增序列
    for (int i = 1; i < n; ++i) {
        for (int j = 0; j < i; ++j) {
            // 如果前面有比nums[i]小的，则让nums[i]接在它后面并计算长度比较一下
            if (nums[i] > nums[j]) {
                dp[i] = max(dp[j] + 1, dp[i]);
            }
            // 如果没有，则nums[i]也应该是1而不是0，因此一开始就要统一初始化为1
        }
        maxLen = max(dp[i], maxLen);
    }
    return maxLen;
}
```

#### 复杂度分析

- 时间复杂度：O(n^2^)。因为计算dp[i]时，需要 O(n) 的时间遍历dp[0…i−1] 的所有状态，所以总时间复杂度为 O(n^2^)。
- 空间复杂度：O(n)，因为需要额外使用长度为n的dp数组。

### 法二

==:star: **核心思路： 贪心 + 二分  **:star:==   **操作只有两个：覆盖和追加**，只有大于最后一个元素才会追加，其他时候都是覆盖。

**:question:   problems:** 

1. 「子序列」和「子串」的概念？
2. 为什么可以用更小的值覆盖前面的值？
3. 为什么法二中说"cell 未必是真实的最长上升子序列，但长度是对的"？

:heavy_check_mark:  **answer:** 

1.  首先要对「子序列」和「子串」这两个概念进行区分；
    - 子序列（subsequence）**并不要求连续**，例如[1, 7, 5]是[1, 2, 4, 3, 7, 6, 5] 的一个子序列  
    - 子串（substring、subarray）一定是原始字符串的**连续子串**。
2.  ![image-20240411174200194](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202404111742304.png)
3.  比如序列2,5,1。按这个步骤，就会变成1,5。长度对的但数据错了。不过，和正确的2,5相比，这个1,5能够更好得衔接之后的比较。又比如 [2，4，5，3，7]按照算法最终结果输出是 [2,3,5,7]并非[2,4,5,7] ，虽然长度是对的。 其实也就是贪心的思想。

**:small_red_triangle_down: steps: **

1. 遍历nums每个元素

2. 若num比cell最后一个元素还大，则直接尾插，递增子序列长度+1

3. 否则，二分找到cell中第一个比nums[i]大的元素 并覆盖，递增子序列长度不变但其之后能装的元素个数增加了

    - 为什么是找第一个比它大的元素？为什么要覆盖？

        1. 因为首先要满足cell是升序的维护的是递增的子序列，那么肯定不能覆盖在比它小的元素，而如果覆盖 第一个比它大的元素其后面更大的那些元素，那么肯定也不满足递增，因此要覆盖在第一个比它大的元素上。

        2. 因为越小的元素就越能接住更多比它大的元素（即递增子序列能装的元素个数能更多），递增子序列就能更长 。

            > 比如2 5 3, 当遇到3之前，最长递增子序列是2 5; 遇到3后，应该将最长递增子序列变为2 3，因为这样能接住更多值，让最长递增子序列更长.

        3. 总之，思想就是让 cell 中存储比较小的元素。这样，cell 未必是真实的最长上升子序列，但长度是对的。

#### 代码

```c++
class Solution {
public:
    int binarySearchFirst(vector<int>& nums, int target) {
        int left = 0; 
        int right = nums.size() - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    } 
    int lengthOfLIS(vector<int>& nums) {
        vector<int> cell; //存最长递增子序列
        cell.emplace_back(nums[0]);
        for (auto num : nums) {
            if (num > cell.back()) {
                // 直接接在末尾就行
                cell.emplace_back(num);
            } else {
                // 二分找到第一个大于等于num的值, 覆盖

                // 若自己实现: 
                // int index = binarySearchFirst(cell, num);
                // cell[index] = num;

                // 也可以用lower_bound库函数
                auto iter = lower_bound(cell.begin(), cell.end(), num);
                *iter = num;
            }
        }
        return cell.size();
    }
};
```

#### 复杂度分析

- 时间复杂度：O(nlogn)

- 空间复杂度：O(n)

## :fire:[152. 乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/)

### 题意

给你一个整数数组 `nums` ，请你找出数组中乘积最大的非空连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

测试用例的答案是一个 **32-位** 整数。

**示例 1:**

```
输入: nums = [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```

**示例 2:**

```
输入: nums = [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
```

**提示:**

- `1 <= nums.length <= 2 * 104`
- `-10 <= nums[i] <= 10`
- `nums` 的任何前缀或后缀的乘积都 **保证** 是一个 **32-位** 整数

### 代码

由于存在负数，那么会导致最大的变最小的，最小的变最大的。因此还需要维护一个最小值 

```c++
int maxProduct(vector<int>& nums) {
    int ans = nums[0];
    // dp定义：dp[i][0]、dp[i][1]分别表示以下标i结尾的连续子数组的最大乘积、最小乘积
    vector<vector<int>> dp(nums.size(), vector<int>(2));
    // 初始化：
    dp[0][0] = nums[0];
    dp[0][1] = nums[0];
    // 遍历：
    for (int i = 1; i < nums.size(); ++i) {
        dp[i][0] = max({nums[i], dp[i - 1][0] * nums[i], dp[i - 1][1] * nums[i]});
        dp[i][1] = min({nums[i], dp[i - 1][1] * nums[i], dp[i - 1][0] * nums[i]});
        ans = max(ans, dp[i][0]);
    }
    return ans;
}
```

优化空间

```c++
int maxProduct(vector<int>& nums) {
    int ans = nums[0];
    // 分别表示以下标i结尾的连续子数组的最大乘积、最小乘积 
    int curMax = nums[0];
    int curMin = nums[0];
    for (int i = 1; i < nums.size(); ++i) {
        int preMax = curMax;
        // 由于在计算curMin之前会更新curMax，而我们想要的是旧值，因此要暂存curMax
        curMax = max({nums[i], curMax * nums[i], curMin * nums[i]});
        curMin = min({nums[i], curMin * nums[i], preMax * nums[i]});
        ans = max(ans, curMax);
    }
    return ans;
}
```



## :fire:[32. 最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/)

### 题意

给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

**示例 1：**

```
输入：s = "(()"
输出：2
解释：最长有效括号子串是 "()"
```

**示例 2：**

```
输入：s = ")()())"
输出：4
解释：最长有效括号子串是 "()()"
```

**示例 3：**

```
输入：s = ""
输出：0
```

**提示：**

- `0 <= s.length <= 3 * 104`
- `s[i]` 为 `'('` 或 `')'`

### 参考题解

1. [官解](https://leetcode.cn/problems/longest-valid-parentheses/solutions/314683/zui-chang-you-xiao-gua-hao-by-leetcode-solution)

![SmartSelect_20240308_102825_Samsung Notes](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403081031155.jpg)

### 法一

==:star: **核心思路：dp   **:star:==

含注释版

```c++
int longestValidParentheses(string s) {
    int maxLen = 0;
    // 1、确定dp数组以及下标含义: dp[i]表示以i为结尾的最长有效括号子串的长度 
    vector<int> dp(s.size());
    // 2、确定递推公式
    // 3、dp数组初始化 
    // 4、遍历顺序
    for (int i = 1; i < s.size(); ++i) {
        if (s[i] == '(') continue; 
        if (s[i - 1] == '(') {
            dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
        } else if (i - dp[i - 1] > 0 && s[i - dp[i - 1] - 1] == '(') {
        /* 关于条件的解读：
            必须要有与s[i]配对的'(', 即s[i - dp[i - 1] - 1]=='(', dp[i]才能继续算下去
            否则由于下面的式子中并不会涉及到i - dp[i - 1] - 1这个位置，因此如果该位置为')'
            dp[i]仍会把该位置当作'('去算
        */
            dp[i] = dp[i - 1] + 2 + (i - dp[i - 1] >= 2 ? dp[i - dp[i - 1] - 2] : 0); 
            //      "((..))"的长度 +  "((..))"前面的配对括号序列的长度
        }
        maxLen = max(maxLen, dp[i]);
    }
    return maxLen;
}
```

无注释版

```c++
int longestValidParentheses(string s) {
    int maxLen = 0; 
    vector<int> dp(s.size()); 
    for (int i = 1; i < s.size(); ++i) {
        if (s[i] == '(') continue; 
        if (s[i - 1] == '(') {
            dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
        } else if (i - dp[i - 1] > 0 && s[i - dp[i - 1] - 1] == '(') { 
            dp[i] = dp[i - 1] + 2 + (i - dp[i - 1] >= 2 ? dp[i - dp[i - 1] - 2] : 0);  
        }
        maxLen = max(maxLen, dp[i]);
    }
    return maxLen;
}
```

#### 复杂度分析

- 时间复杂度：O(n)  

- 空间复杂度：O(n)

### 法二

==:star: **核心思路：栈   **:star:==

一种写法：push(-1)作为最后一个未匹配上的右括号。

```c++
int longestValidParentheses(string s) {
    int maxLen = 0;
    int start = 0;
    stack<int> st;
    st.push(-1);
    for (int i = 0; i < s.size(); ++i) {
        if (s[i] == '(') {
            st.push(i);
            continue;
        }
        st.pop();
        if (st.empty()) { //当前')'要替换刚刚弹出的栈顶作为最后一个未匹配上的右括号
            st.push(i);
        } else {          //当前')'和之前的栈顶'('配对了
            maxLen = max(maxLen, i - st.top());
        }
    }
    return maxLen;
}
```

另一种写法：不用push(-1)作为最后一个未匹配上的右括号。

```c++
int longestValidParentheses(string s) {
    int maxLen = 0;
    int start = 0;
    stack<int> st;
    for (int i = 0; i < s.size(); ++i) {
        if (s[i] == '(') {
            st.push(i);
            continue;
        }
        if (st.empty()) { 
            start = i + 1;
        } else {
            st.pop();
            maxLen = max(maxLen, (st.empty() ? i - start + 1 : i - st.top()));
            // 为什么不是i-st.top()+1 ？因为当前s[i]是和pop()前的栈顶配对的
        }
    }
    return maxLen;
}
```

#### 复杂度分析

- 时间复杂度：O(n)  

- 空间复杂度：O(n)

### 法三

> 不需要额外的空间

==:star: **核心思路：记录左右括号数量，数量相等时统计子串长度，当它们的数量成某种大于小于关系使之不可能称为配对子串时，重置左右括号数量 。 **:star:==

官解法三中说：

```c++
但这样会漏掉一种情况，就是遍历的时候左括号的数量始终大于右括号的数量，即 (() ，这种时候最长有效括号是求不出来的。
解决的方法也很简单，我们只需要从右往左遍历用类似的方法计算即可，只是这个时候判断条件反了过来
```

为什么`这种时候最长有效括号是求不出来的`?——因为此时左括号的数量永远不可能等于右括号的数量。就永远无法统计结果。

**代码**

```c++
int longestValidParentheses(string s) {
    int maxLen = 0;
    // 左右括号数
    int leftCnt = 0;
    int rightCnt = 0;
    // 从左往右遍历
    for (int i = 0; i < s.size(); ++i) {
        if (s[i] == '(') {
            leftCnt++;
        } else {
            rightCnt++;
        }
        if (leftCnt == rightCnt) {
            maxLen = max(maxLen, 2 * leftCnt);
        } else if (leftCnt < rightCnt) {
            leftCnt = rightCnt = 0;
        }
    }
    leftCnt = rightCnt = 0;
    // 从右往遍左历
    for (int i = s.size() - 1; i >= 0; --i) {
        if (s[i] == '(') {
            leftCnt++;
        } else {
            rightCnt++;
        }
        if (leftCnt == rightCnt) {
            maxLen = max(maxLen, 2 * leftCnt);
        } else if (leftCnt > rightCnt) {
            leftCnt = rightCnt = 0;
        }
    }
    return maxLen;
}
```

**复杂度分析**

- 时间复杂度：O(n)  

- 空间复杂度：O(1)



# 回文子串子序列

## [:car: 647. 回文子串](https://leetcode.cn/problems/palindromic-substrings/)

### 题意

给你一个字符串 `s` ，请你统计并返回这个字符串中 **回文子串** 的数目。

**子串** 是字符串中的由连续字符组成的一个序列。

**示例 1：**

```
输入：s = "abc"
输出：3
解释：三个回文子串: "a", "b", "c"
```

**示例 2：**

```
输入：s = "aaa"
输出：6
解释：6个回文子串: "a", "a", "a", "aa", "aa", "aaa"
```

**提示：**

- `1 <= s.length <= 1000`
- `s` 由小写英文字母组成

### 参考题解

1. 卡哥

### 暴解

将s的每一个子串都拿出来看看是否是回文的。

那就需要一个循环作为子串起点 i ，再一个循环遍历当子串起点固定时子串的末尾 j ，则该子串为s[i, j]；

此外还需要确定该子串是否回文，就需要用左右双指针从两端向中间遍历子串。

因此时间复杂度为O(n^3^)。

### 法一

==:star: **核心思路：dp   **:star:==

**:key:  key：**

- 由暴解知，确定某子串是否回文需要O(n)的时间，那能不能让这个时间为O(1)呢？也就是说在循环遍历子串末尾的同时就能确定子串是否回文。
    —— 那就要用到dp！dp数组存储了起点相同时更短的子串是否回文的信息，那就能根据递推关系【：若中间的区间是回文串，那么如果在其两端都再包含一个相同字符进来，则这个更大的区间也是回文串】 用O(1)的时间知道当前子串是否回文。

原始版本：

```c++
int countSubstrings(string s) {
    int res = 0;
    // 1.确定dp数组以及下标含义：dp[i][j]表示区间[i,j]是否是回文串
    vector<vector<bool>> dp(s.size(), vector<bool>(s.size(), false)); 
    // 2.递推关系：若中间的区间是回文串，那么如果在其两端都再包含一个相同字符进来，则这个更大的区间也是回文串 
    // 4.因为dp[i + 1][j - 1]是左下角的值,所以要从下往上从左往右遍历
    for (int i = s.size() - 1; i >= 0; --i) {
        // j要从i开始!
        for (int j = i; j < s.size(); ++j) {
            // 区间[i,j]只有一个或两个元素时,若s[i]==s[j]则dp[i][j]就一定为true
            // 区间[i,j]元素个数超过两个时,dp[i][j]要为true,那除了s[i]==s[j]之外还需要dp[i+1][j-1]=true
            if (s[i] == s[j] && (j - i <= 1 || dp[i + 1][j - 1])) {
                dp[i][j] = true;
                res++;
            }
        }
    }
    return res;
}
```

状态压缩：

```c++
string longestPalindrome(string s) {
    int start = 0;
    int maxLen = 0;
    vector<int> dp(s.size()); 
    // 与二维dp不同, 为避免dp[i + 1][j - 1]左下角值被覆盖, 要从下往上从右往左遍历
    for (int i = s.size() - 1; i >= 0; --i) {
        for (int j = s.size() - 1; j >= i; --j) { 
            if (s[i] == s[j] &&  (j - i + 1 <= 2 || dp[j - 1])) {
                dp[j] = true;
                if (j - i + 1 > maxLen) {
                    maxLen = j - i + 1;
                    start = i;
                }
            } else { //这里和前面不一样！必须写上else！因为是滚动数组因此必须刷新
                dp[j] = false;
            }
        }
    }
    return s.substr(start, maxLen);
}
```

**复杂度分析**

- 时间复杂度：O(n^2^)  

- 空间复杂度：O(n^2^)  / O(n)

### 法二

==:star: **核心思路：左右双指针  中心扩散法 **:star:==

```c++
int countSubstrings(string s) {
    int res = 0;
    for (int i = 0; i < s.size(); i++) {      // 遍历回文中心点
        res += extend(s, i, i, s.size());     // 以i为中心
        res += extend(s, i, i + 1, s.size()); // 以i和i+1为中心
    }
    return res;
}
int extend(const string& s, int i, int j, int n) { //以s[i],s[j]为中心向两边扩
    int res = 0;
    // 若s[i] != s[j], 说明以s[i],s[j]为中心点的所有子串都不可能回文
    while (i >= 0 && j < n && s[i] == s[j]) {  
        res++; // 回文子串数+1
        i--;   //继续向两边扩
        j++;
    }
    return res;
}
```

**复杂度分析**

- 时间复杂度：O(n^2^)  

- 空间复杂度：O(1)

## [:fire: 5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

### 题意

给你一个字符串 `s`，找到 `s` 中最长的回文子串。

如果字符串的反序与原始字符串相同，则该字符串称为回文字符串。

**示例 1：**

```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

**示例 2：**

```
输入：s = "cbbd"
输出："bb"
```

**提示：**

- `1 <= s.length <= 1000`
- `s` 仅由数字和英文字母组成

> 647 题是找字符串中回文子串数，而第 5 题是求一个字符串中最长的回文子串，很明显求解出所有的字符串自然能够找到最大的，所以这两题解法几乎一模一样。就不再详细写了，如有疑问可以看647题

### 法一

==:star: **核心思路：dp   **:star:==

原始版本：

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        int start = 0;
        int maxLen = 1;
        int n = s.size();
        // 1.dp定义：dp[i,j]表示子串s[i,j]是否回文
        vector<vector<bool>> dp(n, vector<bool>(n));
        // 2.递推关系：若中间的区间是回文串，那么如果在其两端都再包含一个相同字符进来，则这个更大的区间也是回文串
        // 如果s[i] == s[j], s[i, j]是否回文
        // - 如果i==j, 一定回文
        // - 如果i==j-1, 也回文
        // - 如果i<j-1, 那么还需要dp[i+1][j-1]为true, 才回文

        // 3.dp初始化: 可以放在for逻辑中
        // 4.遍历: 因为要用到dp[i+1][j-1]左下角的值，因此要从下往上从左往右遍历
        for (int i = s.size(); i >= 0; --i) {
            for (int j = i; j < s.size(); ++j) {
                if (s[i] == s[j]) {
                    if (i == j || i == j - 1 || (i < j - 1 && dp[i + 1][j - 1])) {
                    // 或者写成 if (j - i + 1 <= 2 || dp[i + 1][j - 1]) { 
                        dp[i][j] = true;
                        if (j - i + 1 > maxLen) {
                            start = i;
                            maxLen = j - i + 1;
                        } 
                    }
                }            
            }
        }
        return s.substr(start, maxLen);
    }
};
```

状态压缩：

```c++
string longestPalindrome(string s) {
    int start = 0;
    int maxLen = 1;
    vector<bool> dp(s.size(), false);
    // 与上面不同的是，为了避免滚动数组覆盖，水平方向应从右往左遍历
    for (int i = s.size() - 1; i >= 0; --i) { 
        for (int j = s.size() - 1; j >= i; --j) {
            if (s[i] == s[j] && (j - i <= 1 || dp[j - 1])) {
                dp[j] = true;
                if (j - i + 1 > maxLen) {
                    maxLen = j - i + 1;
                    start = i;
                }
            } else dp[j] = false;
            //这里和前面不一样！必须写上else！因为是滚动数组因此必须刷新
        }
    }
    return s.substr(start, maxLen);
}
```

**复杂度分析**

- 时间复杂度：O(n^2^)  

- 空间复杂度：O(n^2^)  / O(n)

### 法二

==:star: **核心思路：左右双指针  中心扩散法  **:star:==

回文子串分为两种情况：以一个中心点回文、以两个中心点回文

我们遍历回文中心点，对于每个或每两个回文中心点，我们从中心点往两边扩散，具体如下：

- 用left和right双指针分别指向回文子串的两端，判断它们指向的元素是否相同，
- 如果相同就让left--,right++，如果不同就退出循环

```c++
class Solution {
private:
    int start = 0;
    int maxLen = 1;
public:
    string longestPalindrome(string s) {
        for (int i = 0; i < s.size(); i++) { // 遍历回文中心点
            extend(s, i, i, s.size());       // 以i为中心
            extend(s, i, i + 1, s.size());   // 以i和i+1为中心
        }
        return s.substr(start, maxLen);
    } 
    void extend(const string& s, int i, int j, int n) { //以s[i],s[j]为中心向两边扩
        // 若s[i] != s[j], 说明以s[i],s[j]为中心点的所有子串都不可能回文
        while (i >= 0 && j < n && s[i] == s[j]) {  
            if (j - i + 1 > maxLen) {
                maxLen = j - i + 1;
                start = i;
            }
            i--;   //继续向两边扩
            j++;
        }
    }
};
```

**复杂度分析**

- 时间复杂度：O(n^2^)  

- 空间复杂度：O(1)

## :car:516.最长回文子序列

该题是求最长的回文子序列长度（子序列不要求连续）。

### 法一、dp

- dp 二维数组

    - 1、dp定义：用dp[i][j]表示区间[i,j]的最长回文子序列的长度，最终要返回的就是dp[0][s.size()-1]

    - 2、递推公式：``if(s[i]==s[j]) dp[i][j] = dp[i+1][j-1] + 2; else dp[i][j] = 1 + max(dp[i + 1][j], dp[i][j - 1]);``

        - 区间[i,j]的最长回文子序列的长度  能由什么推导而来？

            - 若``s[i]==s[j]``，则其可以延申在s[i+1,j-1]的最长回文子序列的两端，``dp[i][j] = dp[i+1][j-1] + 2;``

            - 否则，就看s[i]或s[j] 能否延申在s[i+1,j-1]的最长回文子序列的一端 
                - `dp[i][j] = 1 + max(dp[i + 1][j], dp[i][j - 1]);`  //比较  s[i+1,j-1]算上s[i]或s[j]其中一端  后  哪个区间的回文子序列最长，取长的

    - 3、初始化：
        - 因为为了防止递推公式中下标越界，我们的内层循环变量 j 是初始化为 i + 1的（因为如果初始化为i, j == i 那则 dp`[i+1][j-1]`就是=``dp[i+1][i-1]``了）。也就是说使用递推公式给dp[i][j]赋值是从  至少有两个元素 的区间开始的。那么对于只有一个元素的区间dp[i][i]就没有赋到值，因此我们需要单独给其赋值：遍历二维数组让dp[i][i] = 1;

    - 4、遍历顺序：因为要用到正下方，正左方，左下角的值，因此要从下往上从左往右遍历

- dp 一维滚动数组

    - 有两个关键点：
        - 1.左下角的值滚动更新为正左方的值之前要保存（用leftdown）
        - 2. 滚动数组怎么给单个元素区间初始化

    - 关于1，怎么保存？

        - 在还没进入到内层循环之前先将leftdown初始化为dp[0]`` leftdown = dp[0];``，此时的dp[0]是滚动前的值，也就是[0]位置之前的值，我们还没刷新该位置

        - 内层循环中最开始就要将当前位置还未刷新前的值保存 tmp = dp[j];，在该次循环末尾的逻辑中 将前面保存的 该位置未刷新前的值tmp 赋值给leftdown`leftdown = tmp`，作为下一循环要刷新的位置的左下角的dp值

    - 关于2，和前面二维数组一样，内层循环变量 j 是初始化为 i + 1的，也就是说使用递推公式给``dp[i][j]``赋值是从  至少有两个元素 的区间开始的。那么对于只有一个元素的区间``dp[i][i]``就没有赋到值，因此我们需要单独给其赋值：因为此时是滚动数组了，不能和二维数组一样直接遍历数组给``dp[i][i]``赋值，而是要在每次还没进入内层循环滚动刷新之前就给其初始化好。外层循环变量 i 对应的是区间[i,j]的左端点，dp[j]中的 j 对应右端点，因此要初始化``dp[i][i]``，也就是要初始化``dp[i] dp[i] = 1;``

    ```c++
    int longestPalindromeSubseq(string s) {
        // 二维dp数组
        // // 1、确定dp数组以及下标的含义:dp[i][j]表示区间s[i,j]内回文序列的长度
        // vector<vector<int>> dp(s.size(), vector<int>(s.size()));
        // // 2、递推公式
        // // 3、初始化
        // for (int i = 0; i < s.size(); ++i) dp[i][i] = 1;
        // // 4、遍历顺序
        // for (int i = s.size() - 1; i >= 0; --i) {
        //     for (int j = i + 1; j < s.size(); ++j) {
        //         if (s[i] == s[j]) dp[i][j] = dp[i + 1][j - 1] + 2;
        //         else dp[i][j] = max(dp[i][j - 1],dp[i + 1][j]);
        //     }
        // }
        // return dp[0][s.size()-1];
    
        //一维滚动dp数组
        vector<int> dp(s.size());
        for (int i = s.size() - 1; i >= 0; --i) { //从下往上从左往右遍历
            //初始化操作
            int leftDown = dp[0];
            dp[i] = 1;
            for (int j = i + 1; j < s.size(); ++j) {
                int tmp = dp[j];
                if (s[i] == s[j]) dp[j] = leftDown + 2;
                else dp[j] = max(dp[j - 1], dp[j]);
                leftDown = tmp;
            }
        }
        return dp[s.size()-1];
    }
    ```

# 01背包

## [kama46. 携带研究材料](https://kamacoder.com/problempage.php?pid=1046)

### 题意

小明是一位科学家，他需要参加一场重要的国际科学大会，以展示自己的最新研究成果。他需要带一些研究材料，但是他的行李箱空间有限。这些研究材料包括实验设备、文献资料和实验样本等等，它们各自占据不同的空间，并且具有不同的价值。 

小明的行李空间为 N，问小明应该如何抉择，才能携带最大价值的研究材料，**每种研究材料只能选择一次，并且只有选与不选两种选择，不能进行切割。**

###### 输入描述

第一行包含两个正整数，第一个整数 M 代表研究材料的种类，第二个正整数 N，代表小明的行李空间。

第二行包含 M 个正整数，代表每种研究材料的所占空间。 

第三行包含 M 个正整数，代表每种研究材料的价值。

###### 输出描述

输出一个整数，代表小明能够携带的研究材料的最大价值。

###### 输入示例

```
6 1
2 2 3 1 5 2
2 3 1 5 4 3
```

###### 输出示例

```
5
```

###### 提示信息

小明能够携带 6 种研究材料，但是行李空间只有 1，而占用空间为 1 的研究材料价值为 5，所以最终答案输出 5。 

数据范围：
1 <= N <= 1000
1 <= M <= 1000
研究材料占用空间和价值都小于等于 1000’

### 代码

```c++
#include <bits/stdc++.h>
using namespace std;
int n, bagweight;// bagweight代背包容量
void solve() { 
    vector<int> weight(n, 0);//物品体积
    vector<int> value(n, 0);//物品价值
    for (int i = 0; i < n; ++i) cin >> weight[i];
    for (int j = 0; j < n; ++j) cin >> value[j];
    //法一、二维数组dp
    // //(1)确定dp数组以及下标的含义:
    // // dp[i][j]表示背包容量为j的情况下,从下标为[0,i]的物品中任意取，能达到的最大价值
    // vector<vector<int>> dp(n, vector<int>(bagweight + 1, 0));
    // //(2)确定递推公式
    // //(3)初始化dp数组--因为需要用到dp[i-1]的值
    // for (int j = weight[0]; j <= bagweight; ++j) {
    //     //j >= weight[0]能装下第一个物品dp[0][j]=value[0]
    //     //否则容量装不下第一个物品dp[0][j]=0;--上面已经初始化过二维数组为全0
    //     dp[0][j] = value[0];
    // }
    // //(4)遍历顺序--对于这题内外层顺序可颠倒
    // for (int i = 1; i < n; ++i) { //遍历物品
    //     for (int j = 1; j < bagweight + 1; ++j) {//遍历背包容量
    //         if (j < weight[i]) dp[i][j] = dp[i - 1][j];//若装不下当前物品
    //         else dp[i][j] = max(dp[i-1][j], value[i] + dp[i-1][j-weight[i]]);//装得下
    //     }
    // }
    // cout << dp[n-1][bagweight] << endl;
     
    //法一、一维滚动数组--二维dp优化
    vector<int> dp(bagweight + 1, 0);//0~1~bagweight
    //下面的遍历顺带就初始化了
    for (int i = 0; i < n; ++i) {//滚动n次
        for (int j = bagweight; j >= weight[i]; --j) {//从大背包开始遍历
            // if (j < weight[i]) dp[j]不动
             dp[j] = max(dp[j], value[i]+dp[j-weight[i]]);
        }
        /*
         内层for循环的遍历顺序为什么是倒序？

         因为随着遍历我们要不断刷新该层数值以实现滚动，而每个位置的值都要靠比较左上角才能确定。
         如果是正序那么遍历到某个位置时，其左边的元素都已经被刷新成当前层的值了，
         即不是我们想要的左边的上一层（左上角）的值，而是左边的本层（同一层左邻元素）的值。

         而倒序的时候每个位置的左边所有元素都还没遍历到，即还没刷新，都是上一层的值，
         但正序的时候，左边的元素刚刚刷新过，也就是左边的元素已经是本层的了，意味着
         我们本来想要的是上一层的dp[j-weight[i]]实际却是刷新后的、本层的dp[j-weight[i]]
         也就是本想要的是dp[i-1][j-weight[i]],实际却是dp[i][j-weight[i]]，一个物品多加了几次。
        */
    }
    cout << dp[bagweight] << endl;
}
int main() {
    while(cin >> n >> bagweight) {//输入物品数量以及背包容量
        solve();
    }
    return 0;
}
```

## :fire:[494. 目标和](https://leetcode.cn/problems/target-sum/)

### 题意

给你一个非负整数数组 `nums` 和一个整数 `target` 。

向数组中的每个整数前添加 `'+'` 或 `'-'` ，然后串联起所有整数，可以构造一个 **表达式** ：

- 例如，`nums = [2, 1]` ，可以在 `2` 之前添加 `'+'` ，在 `1` 之前添加 `'-'` ，然后串联起来得到表达式 `"+2-1"` 。

返回可以通过上述方法构造的、运算结果等于 `target` 的不同 **表达式** 的数目。

**示例 1：**

```
输入：nums = [1,1,1,1,1], target = 3
输出：5
解释：一共有 5 种方法让最终目标和为 3 。
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
```

**示例 2：**

```
输入：nums = [1], target = 1
输出：1
```

**提示：**

- `1 <= nums.length <= 20`
- `0 <= nums[i] <= 1000`
- `0 <= sum(nums[i]) <= 1000`
- `-1000 <= target <= 1000`

### 参考题解

1. [卡哥](https://leetcode.cn/problems/target-sum/solutions/816584/dai-ma-sui-xiang-lu-494-mu-biao-he-01bei-rte9)

### 思路

==:star: **核心思路：dp 01背包问题   **:star:==

**:key:  key：**

- 一、先想清楚该题怎样转化为背包问题求解？

    1、正的部分a - 负的部分b = target

    2、正的部分a + 负的部分b = sum

    （注意a,b都是绝对值形式！）

    由1,2得：2a = sum + target ==> a=(sum + target)/2

    因此问题就转换成了:容量为(sum + target)/2的背包可以被正的部分装满多少次。

    因为每个数只能取一次，因此是01背包问题。

    如果(sum+target)/2除不尽，说明不存在满足条件的背包，也就不存在任何解

    （因为若有解则上面两个式子联立之后2a=sum+target, 也就是说sum+target必须是2的倍数）

- 二、递归四步

    1. dp数组及下标含义：dp[j]表示用正的部分装满容量为j的包有多少种方法

    2. 递推公式：`新的dp[j] = 旧的dp[j] + dp[j-nums[i]]`. 也就是 ==> `dp[j] += dp[j-nums[i]]`。即 = 不装第i件物品能装满的方案数 + 装第i件物品能装满的方案数

    3. dp数组初始化：为什么要初始化:dp[0] = 1 ？

        - 若装第i件物品剩下空间为0，那也是一个方案数。因此当nums[i]==j时，也即dp[j-nums[i]]=dp[0]时，dp[0]应该为1

    4. 遍历顺序

        - 递推公式如果写成二维的就是: `dp[i][j] = dp[i - 1][j] + dp[i - 1][j-nums[i]]`
        - 因此dp[j]依赖于滚动前的dp[j]以及滚动前的左边的dp，因此要从右往左，从上往下遍历
        - 另外，由于背包容量和nums[i]有关系，如果j<nums[i]那j就没必要再往左遍历了。所以必须是外层是物品，内层是背包。

    5. 打印dp数组

        ```c++
        case ：nums = [1,1,1,1,1], target = 3，Output: 5
        
        nums[0]遍历背包 1 1 0 0 0
        
        nums[1]遍历背包 1 2 1 0 0
        
        nums[2]遍历背包 1 3 3 1 0
        
        nums[3]遍历背包 1 4 6 4 1
        
        nums[4]遍历背包 1 5 10 10 5
        ```

### 代码

```c++
int findTargetSumWays(vector<int>& nums, int target) {
    int n = nums.size(), sum = 0;
    for(int num : nums) {
        sum += num;
    }
    if ((sum + target) % 2 != 0) {
        return 0;
    } 
    if(sum < abs(target)) {
        return 0; //比如nums=[100,100],target=-400
    }
    int bagSize = (sum + target) / 2;
    //dp数组及下标含义: dp[j]表示容量为j的背包能被0~i的正数装满的方案数
    vector<int> dp(bagSize + 1, 0); 
    dp[0] = 1;
    // 从右往左，从上往下遍历 
    for(int i = 0; i < n; ++i) { 				   //从上往下
        for (int j = bagSize; j >= nums[i]; --j) { //从右往左
            dp[j] += dp[j - nums[i]];
        }
    }
    return dp[bagSize];
}
```

### 复杂度分析

- 时间复杂度：O(n * bagSize)  

- 空间复杂度：O(bagSize)

## :fire:[416. 分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

### 题意

给你一个 **只包含正整数** 的 **非空** 数组 `nums` 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

**示例 1：**

```
输入：nums = [1,5,11,5]
输出：true
解释：数组可以分割成 [1, 5, 5] 和 [11] 。
```

**示例 2：**

```
输入：nums = [1,2,3,5]
输出：false
解释：数组不能分割成两个元素和相等的子集。
```

**提示：**

- `1 <= nums.length <= 200`
- `1 <= nums[i] <= 100`

### 参考题解

1. [卡哥](https://leetcode.cn/problems/partition-equal-subset-sum/solutions/553978/bang-ni-ba-0-1bei-bao-xue-ge-tong-tou-by-px33)

### 思路

==:star: **核心思路：dp 01背包问题 **:star:==

和 :fire:[494. 目标和](https://leetcode.cn/problems/target-sum/)  思路一模一样

### 二维

```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for (int& val : nums) {
            sum += val;
        }
        if (sum % 2 == 1) {
            return false;
        }
        int target = sum / 2;
        // dp[i][j]表示下标[0,i-1]的元素能否把容量为j的背包装满
        vector<vector<bool>> dp(nums.size() + 1, vector<bool>(target + 1, false));
        // i=0时表示空能否把容量为j的背包装满，显然不能
        // j=0时：
        for (int i = 0; i <= nums.size(); ++i) {
            dp[i][0] = 1;
        } 
        for (int i = 1; i <= nums.size(); ++i) {
            for (int j = 1; j <= target; ++j) {
                if (j - nums[i - 1] >= 0) {
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i - 1]];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[nums.size()][target]; 
    }
};
```

### 一维

#### 第一种dp定义

```c++
bool canPartition(vector<int>& nums) {
    int sum = 0;
    for (int num : nums) {
        sum += num;
    }
    if (sum % 2 != 0) {
        return false;
    } 
    int target = sum / 2; 
    // dp[j]表示容量为j的背包最多能装的值为多少
    vector<int> dp(target + 1); 
    // 从上往下，从右往左。外层遍历物品，内层遍历背包
    for (int i = 0; i < nums.size(); ++i) {
        for (int j = target; j >= nums[i]; --j) {
            dp[j] = max(dp[j - nums[i]] + nums[i], dp[j]);
        }
    }
    return dp[target] == target;
}
```

#### 第二种dp定义

```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for (int& val : nums) {
            sum += val;
        }
        if (sum % 2 == 1) {
            return false;
        }
        int target = sum / 2;
        // dp[j]表示容量为j的背包能否装满
        vector<bool> dp(target + 1, false);
        dp[0] = 1;
        for (int i = 0; i < nums.size(); ++i) {
            for (int j = target; j >= nums[i]; --j) {
                dp[j] = dp[j] || dp[j - nums[i]];//不装，装
            }
        }
        return dp[target]; 
    }
};
```

### 复杂度分析

- 时间复杂度：O(n * target)  

- 空间复杂度：O(target)

# 完全背包

## [kama52. 携带研究材料](https://kamacoder.com/problempage.php?pid=1052)

### 题意

###### 题目描述

小明是一位科学家，他需要参加一场重要的国际科学大会，以展示自己的最新研究成果。他需要带一些研究材料，但是他的行李箱空间有限。这些研究材料包括实验设备、文献资料和实验样本等等，它们各自占据不同的空间，并且具有不同的价值。

小明的行李空间为 N，问小明应该如何抉择，才能携带最大价值的研究材料，**每种研究材料可以选择无数次，并且可以重复选择。**

###### 输入描述

第一行包含两个整数，N，V，分别表示研究材料的种类和行李空间 

接下来包含 N 行，每行两个整数 wi 和 vi，代表第 i 种研究材料的重量和价值

###### 输出描述

输出一个整数，表示最大价值。

###### 输入示例

```
4 5
1 2
2 4
3 4
4 5
```

###### 输出示例

```
10
```

###### 提示信息

第一种材料选择五次，可以达到最大值。

数据范围：

1 <= N <= 10000;
1 <= V <= 10000;
1 <= wi, vi <= 10^9.

### 代码

```c++
#include <iostream>
#include <vector>
using namespace std;
void pack(int N, int V) {
    vector<int> dp(V + 1, 0);
    for (int i = 0; i < N; ++i) {
        int wi,vi;//物品重量和价值
        cin >> wi >> vi;
        for (int j = 0; j <= V; ++j) {
            if(j >= wi) dp[j] = max(dp[j], dp[j - wi] + vi);
        }
    }
    cout << dp[V] << endl;
}
int main() {
    int N, V;//物品种类和背包容量
    cin >> N >> V;
    pack(N, V);
    return 0;
}
```



## :fire:[322. 零钱兑换](https://leetcode.cn/problems/coin-change/)

### 题意

给你一个整数数组 `coins` ，表示不同面额的硬币；以及一个整数 `amount` ，表示总金额。

计算并返回可以凑成总金额所需的 **最少的硬币个数** 。如果没有任何一种硬币组合能组成总金额，返回 `-1` 。

你可以认为每种硬币的数量是无限的。

**示例 1：**

```
输入：coins = [1, 2, 5], amount = 11
输出：3 
解释：11 = 5 + 5 + 1
```

**示例 2：**

```
输入：coins = [2], amount = 3
输出：-1
```

**示例 3：**

```
输入：coins = [1], amount = 0
输出：0
```

**提示：**

- `1 <= coins.length <= 12`
- `1 <= coins[i] <= 2^31 - 1`
- `0 <= amount <= 104`

### 二维dp

==:star: **核心思路：完全背包 **:star:==

**:question:   problems:** 

1. dp.size()到底初始化为 n 还是 n+1？这和dp定义有什么关系？

:heavy_check_mark:  **answer:** 

1. 都可以。只不过通常来说，初始化为n+1的话，dp数组初始化的逻辑就简单很多。因此==一般初始化为n+1更好！==
    - 初始化为n+1，dp\[i][j]定义为: use [0,i-1] to get amount = j, min number
    - 初始化为n  ，dp\[i][j]定义为: use [0,i]   to get amount = j, min number

 :balloon:  **收获: **

- 一般需要用`INT_MAX`的地方，最好换成`0x3f3f3f3f`，它比INT_MAX更好，不容易溢出

**:x: careless | ignore :**

- 最后返回的时候要注意判断是否有解！

#### dp.size()初始化为n+1

关键是第二步初始化。

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) { 
        int n = coins.size();
        // dp[i][j]: use [0, i - 1] to get amount = j, min number
        vector<vector<int>> dp(n + 1, vector<int>(amount + 1, 0x3f3f3f3f)); 
        // 初始化：
        // i=0时，dp[0][j]: use [空集]  to get amount = j, min number=0x3f3f3f3f
        // j=0时，dp[i][0]: use [0,i-1] to get amount = 0, min number=0
        for (int i = 0; i <= n; i++) { 
            dp[i][0] = 0;
        }
        // 用到左上和上的值，因此从左往右从上往下遍历
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= amount; j++) {
                if (j >= coins[i - 1]) { 
                    // 如果背包容量能装当前物品coins[i - 1]，那就需要判断装不装该物品
                    dp[i][j] = min(dp[i - 1][j], dp[i][j - coins[i - 1]] + 1);
                    // 不装，装。装的时候不是dp[i - 1][j - coins[i - 1]]！因为这是完全背包！可重复选
                }  else { 
                    // 否则，那就不能装该物品，只需要看不装它的时候所需的最少硬币数
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        // 一定不要忘了判断！因为可能装不满
        return dp[n][amount] == 0x3f3f3f3f ? -1 : dp[n][amount];
        // // 打印日志：
        // for(int i = 1; i <= n; i++){
        //     for(int j = 0; j <= amount; j++){
        //         cout << dp[i][j] << " ";
        //     }
        //     cout << endl;
        // }
    }
};
```

#### dp.size()初始化为n

```cpp
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) { 
        int n = coins.size();
        // dp[i][j]: use [0, i] to get amount = j, min number
        vector<vector<int>> dp(n, vector<int>(amount + 1, 0x3f3f3f3f)); 
        // 初始化：
        // i=0时，dp[0][j]: use [0]   to get amount = j, min number=需要for循环求
        // j=0时，dp[i][0]: use [0,i] to get amount = 0, min number=0
        
        // i=0时，用2是否能把容量为j的背包装满，能的话几个？dp[2]要1个,dp[4]要2个···
        // ==> 初始化容量为coins[0]的背包的dp为1，之后从容量coins[0]+1开始遍历求dp
        
        if (amount >= coins[0]) { // 必须判断！否则coins = {1}, amount = 0时会下标越界
            dp[0][coins[0]] = 1;
            for (int j = coins[0] + 1; j <= amount; j++) {
                dp[0][j] = min(dp[0][j], 1 + dp[0][j - coins[0]]); // 不装,装 
            }
        } 
        for (int i = 0; i < n; i++) {
            dp[i][0] = 0;
        }
        for (int i = 1; i < n; i++) {
            for (int j = 1; j <= amount; j++) {
                if (j >= coins[i]) { 
                    dp[i][j] = min(dp[i - 1][j], dp[i][j - coins[i]] + 1); 
                }  else { 
                    dp[i][j] = dp[i - 1][j];
                }
            }
        } 
        return dp[n - 1][amount] == 0x3f3f3f3f ? -1 : dp[n - 1][amount];
    }
};
```

### 疑问与解答（背包通用？）:star::star::star:

为什么二维的内层循环必须写成这样：

```cpp
for (int j = 1; j <= amount; j++) {
    if (j >= coins[i]) { 
        dp[i][j] = min(dp[i - 1][j], dp[i][j - coins[i]] + 1); 
    }  else { 
        dp[i][j] = dp[i - 1][j];
    }
}
```

不能写成一维这样？

```cpp
for (int j = coins[i - 1]; j <= amount; ++j) { 
    dp[i][j] = min(dp[i - 1][j], dp[i][j - coins[i - 1]] + 1);
}
```

因为如果写成这样，那么对于容量 < coins[i - 1]的背包，就无法计算到dp值。

>比如coins={1,2,5}, amount=11时，
>
>i=2,j=1时, dp\[2][1]表示 use [0,1] to get amount = 1, 此时如果按这样写，由于背包容量1<coins[1], 因此就不会计算dp\[2][1]，仍为无穷大。实际上dp\[2][1]=1

而一维的是滚动刷新的，如果容量 < coins[i - 1]，那就不刷新即可，即还是滚动前的值（本来也装不了这个物品）。

### 一维dp

**模拟：**

coins={1,2,5}, amount=11

![image-20240418130543651](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202404181305813.png)

#### 对应二维的版本1

```cpp
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        // 1、确定dp数组以及下标含义：dp[j]表示装满容量为j的背包需要的最少物品件数 
        vector<int> dp(amount + 1, 0x3f3f3f3f);
        // 2、确定递推公式：dp[j] = min(dp[j], 1 + dp[j - coins[i-1]])
        // 3、dp数组初始化：除了dp[0]=0之外都初始化为0x3f3f3f3f
        dp[0] = 0;
        // 4、遍历顺序: 都可
        for(int i = 1; i <= coins.size(); ++i) {
            for (int j = coins[i - 1]; j <= amount; ++j) { 
                dp[j] = min(dp[j], 1 + dp[j - coins[i - 1]]);
            }
        }
        return dp[amount] == 0x3f3f3f3f ? -1 : dp[amount];
    }
};
```

#### 对应二维的版本2

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        // 1、确定dp数组以及下标含义：dp[j]表示装满容量为j的背包需要的最少物品件数 
        vector<int> dp(amount + 1, 0x3f3f3f3f);
        // 2、确定递推公式：dp[j] = min(dp[j], 1 + dp[j - coins[i]])
        // 3、dp数组初始化：除了dp[0]=0之外都初始化为0x3f3f3f3f
        dp[0] = 0;
        // 4、遍历顺序: 都可
        for(int i = 0; i < coins.size(); ++i) {
            for (int j = coins[i]; j <= amount; ++j) { 
                dp[j] = min(dp[j], 1 + dp[j - coins[i]]);
            }
        }
        return dp[amount] == 0x3f3f3f3f ? -1 : dp[amount];
    }
};
```

## :fire:[518. 零钱兑换 II](https://leetcode.cn/problems/coin-change-ii/)

### 题意

给你一个整数数组 `coins` 表示不同面额的硬币，另给一个整数 `amount` 表示总金额。

请你计算并返回可以凑成总金额的硬币组合数。如果任何硬币组合都无法凑出总金额，返回 `0` 。

假设每一种面额的硬币有无限个。 

题目数据保证结果符合 32 位带符号整数。

**示例 1：**

```
输入：amount = 5, coins = [1, 2, 5]
输出：4
解释：有四种方式可以凑成总金额：
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

**示例 2：**

```
输入：amount = 3, coins = [2]
输出：0
解释：只用面额 2 的硬币不能凑成总金额 3 。
```

**示例 3：**

```
输入：amount = 10, coins = [10] 
输出：1
```

**提示：**

- `1 <= coins.length <= 300`
- `1 <= coins[i] <= 5000`
- `coins` 中的所有值 **互不相同**
- `0 <= amount <= 5000`

### 思路

==:star: **核心思路： 完全背包  **:star:==

**:key:  key：**

- 关键是dp数组初始化，当j=0时需要初始化为1。

### 代码

#### 二维

```c++
int change(int amount, vector<int>& coins) {
    int n = coins.size();
    // dp[i][j]表示用coins[0,i-1]凑成j, 有多少种组合
    vector<vector<int>> dp(n + 1, vector<int>(amount + 1));
    // dp数组初始化: j=0时
    for (int i = 0; i <= n; ++i) {
        dp[i][0] = 1;
    }
    // 遍历: 由于求的是组合，因此嵌套顺序没有讲究
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= amount; ++j) {
            dp[i][j] += dp[i - 1][j]; //这次不选coins[i-1]
            if (j >= coins[i - 1]) {
                dp[i][j] += dp[i][j - coins[i - 1]]; //这次选
            }
        }
    }
    return dp[n][amount];
}
```

#### 一维

```c++
int change(int amount, vector<int>& coins) {
    vector<int> dp(amount + 1);
    // dp数组初始化: j=0时
    dp[0] = 1;
    // 遍历: 由于要用到刷新后左、刷新前上的值，因此是从上往下从左往右遍历
    for (int i = 1; i <= coins.size(); ++i) {
        for (int j = coins[i - 1]; j <= amount; ++j) {
            dp[j] += dp[j - coins[i - 1]];  
        }
    }
    return dp[amount];
}
```

 

## :fire:[139. 单词拆分](https://leetcode.cn/problems/word-break/)

### 题意

给你一个字符串 `s` 和一个字符串列表 `wordDict` 作为字典。如果可以利用字典中出现的一个或多个单词拼接出 `s` 则返回 `true`。

**注意：**不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。

**示例 1：**

```
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以由 "leet" 和 "code" 拼接成。
```

**示例 2：**

```
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以由 "apple" "pen" "apple" 拼接成。
     注意，你可以重复使用字典中的单词。
```

**示例 3：**

```
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

**提示：**

- `1 <= s.length <= 300`
- `1 <= wordDict.length <= 1000`
- `1 <= wordDict[i].length <= 20`
- `s` 和 `wordDict[i]` 仅由小写英文字母组成
- `wordDict` 中的所有字符串 **互不相同**

### 参考题解

1. [卡哥](https://leetcode.cn/problems/word-break/solutions/850456/dai-ma-sui-xiang-lu-139-dan-ci-chai-fen-50a1a)  每一步都很清晰，详细讲了遍历顺序的问题。 

### 思路

==:star: **核心思路：完全背包  **:star:==

1. dp定义：dp[i]表示长度为 i 的字符串s[0,i)能否被wordDict中的单词完整拆分
    - 转换成背包问题就是，容量为i的背包是否能被wordDict中的物品装满
2. dp递推公式: 如果`子串s[j, i) == 单词`, 且`dp[j]=true`,则dp[i]=true
3. dp数组初始化：`dp[0] = true; ` 为什么？？？
    - 因为背包刚好被一个物品装满时，剩下容量为0，根据dp公式，dp[0]当然要为true
4. 遍历：因为求的是排列而不是组合，因此是外层遍历背包，内层遍历物品。

### 代码

第一种写法：内层for通过遍历wordDict中的单词判断s[0,i)是否能被装满：`for (auto& str : wordDict)`

```cpp
bool wordBreak(string s, vector<string>& wordDict) {
/*
    字符串--背包，字典里的单词--物品
    问题可以转换为：字典中的物品是否可以刚好装满背包（可以任意多次）。完全背包问题。
*/ 
    // dp定义：dp[i]表示字符串s[0,i-1]是否能被字典里的单词完整拆分
    vector<bool> dp(s.size() + 1);
    // 初始化：
    dp[0] = true;
    // 遍历: 
    for (int i = 1; i <= s.size(); ++i) {
        for (const string &str : wordDict) {
            int wordLen = str.size();
            if (i >= wordLen) {
                // 尝试装str看是否能装满, 除了大小要合适之外，子串也要能对应上。
                // 计算开始截取的下标start，s[0,start,i-1]，str.size() = i-1-start+1 = i-start
                string partOfLast = s.substr(i - wordLen, wordLen);  
                dp[i] = (dp[i - wordLen] && str == partOfLast) || dp[i];
            }
        }
    }
    return dp[s.size()];
}
```

第二种写法：一开始就将所有wordDict中的单词存到uset中，在内层for中遍历子串起点：`for (int j = 0; j < i; ++j)`

```cpp
bool wordBreak(string s, vector<string>& wordDict) {
    vector<bool> dp(s.size() + 1);
    
    dp[0] = true;

    unordered_set<string> uset;
    for (auto& str : wordDict) {
        uset.insert(str);
    }
    for (int i = 1; i <= s.size(); ++i) {
        for (int j = 0; j < i; ++j) {
            // 判断当前子串s[j,i)是否==字典中的某个单词，
            // 如果是，则判断剩余容量=i-(i-1-j+1)=i-(i-j)=j, 即s[0,j)是否能被装满
            string partOfLast = s.substr(j, i - j);
            if (dp[j] && uset.count(partOfLast)) {
                dp[i] = true;
            }
        }
    }
    return dp[s.size()];
}
```

### 复杂度分析

- 时间复杂度：O(n^2^) .卡哥这里写的O(n^3^)我感觉不太对，因为wordDict[i].length <= 20， 因此这里substr的时间复杂度应该是O(1)
- 空间复杂度：O(1)

## :fire:[279. 完全平方数](https://leetcode.cn/problems/perfect-squares/)

### 题意

给你一个整数 ，返回 和为 `n` 的完全平方数的最少数量 。

**完全平方数** 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，、、 和 都是完全平方数，而 和 不是。`1``4``9``16``3``11`

**示例 1：**

```
输入：n = 12
输出：3 
解释：12 = 4 + 4 + 4
```

**示例 2：**

```
输入：n = 13
输出：2
解释：13 = 4 + 9
```

**提示：**

- `1 <= n <= 104`

### 参考题解

1. [卡哥](https://leetcode.cn/problems/perfect-squares/solutions/823192/dai-ma-sui-xiang-lu-279-wan-quan-ping-fa-9ieo)

### 思路

==:star: **核心思路：完全背包  **:star:==

此题关键不在于dp怎么写，而在于怎么想到用dp来写。

```c++
class Solution {
public:
    int numSquares(int n) {
        // dp[j]表示装满容量为j的背包所需的最少完全平方数
        vector<int> dp(n + 1, 0x3f3f3f3f);
        dp[0] = 0;
        // 外层遍历背包、内层遍历物品，还是外层遍历物品、内层遍历背包，都可以
        for (int j = 1; j <= n; ++j) {
            for (int i = 1; i * i <= j; ++i) { 
                dp[j] = min(dp[j], dp[j - i * i] + 1); //不装，装
            }
        }
        return dp[n];
    }
};
```

## [爬楼梯进阶](https://kamacoder.com/problempage.php?pid=1067)

### 题意

每次可以最多可以爬 m 阶，爬到第n阶有多少种方法？

**示例 1：**

```cpp
输入：3
输出：3
```

**示例 2：**

```cpp
输入：20
输出：10946
```

### 代码

完全背包问题。问题可以转换为：有体积为1~m的物品，和一个容量为n的背包，这些物品每次都可以重复选，装满这个背包有多少种方法？

注意for循环嵌套的顺序！！！ 

- 在**选取物品讲究顺序**，即**求的是排列而非组合**时，for循环嵌套的顺序就不是任意的了，此时必须是==外层遍历背包，内层遍历物品。==

为什么这里求的是排列？因为同样是爬3级，先爬两级再爬一级，和先爬一级再爬两级是不同的爬法。

如果外层遍历物品，内层遍历背包，那么计算dp[4]的时候，结果集只有 {1,3} 这样的集合，不会有{3,1}这样的集合，因为物品遍历放在外层，3只能出现在1后面！

```cpp
int climbStairs(int n, int m) {
    // dp[i] 表示爬到第 i 阶楼梯有多少种方法
    vector<int> dp(n + 1, 0);
    // 递推：每次可以选择爬1~m阶，设选择爬k阶，选完后剩下i-k阶有多少种爬法，就加到dp[i]中 
    // 初始化
    dp[0] = 1;
    // 遍历: 因为，这里必须是外层遍历背包，内层遍历物品
    for (int i = 1; i <= n; i++) { //背包容量
        for (int k = 1; k <= m; k++) { //物品体积
            if (k <= i) { 
                dp[i] += dp[i - k];
            }
        }
    }
    return dp[n];
}
```





# 打家劫舍

## :fire:[198. 打家劫舍](https://leetcode.cn/problems/house-robber/):star:==内含常见进阶: 打印路径==:star:

### 题意

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下** ，一夜之内能够偷窃到的最高金额。

**示例 1：**

```
输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

**示例 2：**

```
输入：[2,7,9,3,1]
输出：12
解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

**提示：**

- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 400`

### 代码

原始版本

```c++
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n <= 1) {
        return nums[0];
    }
    // dp[i]表示截至走到第i家能获得的最高金额=max(偷第i家，不偷第i家)
    vector<int> dp(n);
    // 初始化
    dp[0] = nums[0];
    dp[1] = max(nums[0], nums[1]);
    // 遍历
    for (int i = 2; i < n; ++i) {
        dp[i] = max(dp[i - 2] + nums[i], dp[i - 1]); 
    }
    return dp[n - 1];
}
```

dp状态压缩

1. 第一种写法，按着上面的原始版本直接改的，但是这种写法边界很容易漏！最好一开始就特判！

```c++
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n <= 1) {
        return nums[0];
    }
    int dp0 = nums[0];				//相当于dp[i-2]，要偷第i家
    int dp1 = max(nums[0], nums[1]);//相当于dp[i-1]，不偷第i家
    // dp表示截至走到第i家能获得的最高金额=max(偷第i家，不偷第i家)
    int dp = max(dp0, dp1);
    for (int i = 2; i < n; ++i) {
        dp = max(dp0 + nums[i], dp1); // = max(偷第i家，不偷第i家)
        dp0 = dp1;
        dp1 = dp;
    }
    return dp;
}
```

2. 第二种写法

```c++
int rob(vector<int>& nums) {
    // 3.初始化
    int dp0 = 0; //对标dp[n-2]
    int dp1 = 0; //对标dp[n-1]
    // 4.遍历
    for (const int& val : nums) {
        int dp2 = max(dp1, val + dp0); 
        dp0 = dp1; 
        dp1 = dp2; //这两行的顺序不能反
    }
    // 不要return dp2 !!! 因为nums.size()==2时不会进for循环dp2就无法更新
    return dp1;
}
```

```c++
1. i=0
    dp2 = max(0, nums[0]);
    dp0 = 0;
    dp1 = nums[0];
2. i=1
    dp2 = max(nums[0], nums[1] + 0);
    dp0 = nums[0];
    dp1 = max(nums[0], nums[1] + 0);
```

### 进阶：输出偷窃的房屋编号

> 详细证明请看：[LeetCode198打家劫舍（动态规划）+ 返回最优路径](https://blog.csdn.net/Chenguanxixixi/article/details/119540929)

#### 思路

要打印路径的话当然就不能状态压缩了

![SmartSelect_20240315_161620_Samsung Notes](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403151621407.jpg)

#### 打印结果

![image-20240315155659999](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403151557090.png)

#### 代码

```c++
int rob(vector<int>& nums) {
    int n = nums.size();
    if (n <= 1) {
        return nums[0];
    }
    // 1.dp定义：dp[i]表示截至走到第i家能获得的最高金额=max(偷第i家，不偷第i家)
    vector<int> dp(n);
    // 2.初始化
    dp[0] = nums[0];
    dp[1] = max(nums[0], nums[1]);
    // 3.遍历
    for (int i = 2; i < n; ++i) {
        dp[i] = max(dp[i - 2] + nums[i], dp[i - 1]); 
    }
    
    // 4.进阶：打印路径
    vector<int> path;
    // 4.1 找到偷的最后一家的索引, 函数返回数组中第一个不小于指定元素(第三个参数)的索引位置
    int index = lower_bound(dp.begin(), dp.end(), dp[nums.size() - 1]) - dp.begin();
    path.emplace_back(index);
    // 4.2 从后往前逐个找偷的上一家
    while (dp[index] > nums[index]) { // 如果dp[index] <= nums[index], 说明已经找完了
        // 接着找偷的上一家（即第一个dp值 = dp[index]-nums[index]的）
        index = lower_bound(dp.begin(), dp.end(), dp[index] - nums[index]) - dp.begin();
        path.emplace_back(index);
    }
    // 退出循环时dp[index] == nums[index]
    // 4.4 从后往前输出
    for (int i = path.size() - 1; i >= 0; i--) {
        cout << "nums[" << path[i] << "] = " << nums[path[i]] << endl;
    }
    return dp[n - 1];
}
```





# 股票问题

## :fire:[121. 买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)

### 题意

给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。

你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

**示例 1：**

```
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

**示例 2：**

```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。
```

**提示：**

- `1 <= prices.length <= 105`
- `0 <= prices[i] <= 104`

### 法一

==:star: **核心思路：贪心   **:star:==

- 每一天都选择在这天之前的股票最低点卖出股票，看是否会比之前的最大利润还大
- 每天我们都可以选择出售股票与否。在第 `i` 天，如果我们要在今天卖股票，什么时候买入能赚得最多呢？当然是之前价格最低的时候！

#### 代码

原始版本

```c++
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    int maxprofit = 0;
    int dp[n];//dp[i]表示截止到第i天,股票最低点是多少
    dp[0] = prices[0];
    for (int i = 1; i < n; i++) {
        dp[i] = min(dp[i - 1], prices[i]);//更新 截至第i天的股票最低点
        maxprofit = max(prices[i] - dp[i], maxprofit);//看当天卖出,最大利润是否会更新
        //若会，则当天卖出，更新maxprofit;
        //若不会，则当天不卖出，不更新maxprofit
    }
    return maxprofit;
}
```

优化版本

```c++
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    int maxprofit = 0; 
    int lowest = prices[0];//股票价格最低点
    for (int i = 1; i < n; i++) {
        lowest = min(lowest, prices[i]);
        maxprofit = max(prices[i] - lowest, maxprofit);
    }
    return maxprofit;
}
```

#### 复杂度分析

- 时间复杂度：O(n)  

- 空间复杂度：O(n) / O(1)

### 法二

==:star: **核心思路：dp   **:star:==

#### 代码

原始版本

```c++
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    int maxprofit = 0; 
    // 1.确定dp数组以及下标含义：dp[i][0],dp[i][1]分别表示第i天持有股票,不持有股票的最多现有现金
    vector<vector<int>> dp(n, vector<int>(2));
    // 2.递推公式：
    // dp[i][0] = max(dp[i-1][0], -prices[i]);//维持持有股票的状态/当天才持有股票
    // dp[i][1] = max(dp[i-1][1], dp[i-1][0]-prices[i]);//维持不持有股票的状态/当天才不持有股票
    // 3.初始化
    dp[0][0] = -prices[0], dp[0][1] = 0;
    // 4.遍历
    for (int i = 1; i < n; ++i) {
        dp[i][0] = max(dp[i-1][0], -prices[i]); 
        dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i]);
    }
    return dp[n-1][1];
}
```

优化版本

```c++
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    int maxprofit = 0; 
    int dp0 = -prices[0], dp1 = 0; 
    for (int i = 1; i < n; ++i) {
        dp0 = max(dp0, -prices[i]);//维持持有股票的状态/当天才持有股票 
        dp1 = max(dp1, dp0 + prices[i]);//维持不持有股票的状态/当天才不持有股票
    }
    return dp1;
}
```

#### 复杂度分析

- 时间复杂度：O(n)  

- 空间复杂度：O(n) / O(1)

## :fire:[122. 买卖股票的最佳时机 II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

### 题意

给你一个整数数组 `prices` ，其中 `prices[i]` 表示某支股票第 `i` 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 **最多** 只能持有 **一股** 股票。你也可以先购买，然后在 **同一天** 出售。

返回 *你能获得的 **最大** 利润* 。

**示例 1：**

```
输入：prices = [7,1,5,3,6,4]
输出：7
解释：在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4。
随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6 - 3 = 3。
最大总利润为 4 + 3 = 7 。
```

**示例 2：**

```
输入：prices = [1,2,3,4,5]
输出：4
解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4。
最大总利润为 4 。
```

**示例 3：**

```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 交易无法获得正利润，所以不参与交易可以获得最大利润，最大利润为 0。
```

 **提示：**

- `1 <= prices.length <= 3 * 104`
- `0 <= prices[i] <= 104`

### 法一

==:star: **核心思路：贪心   **:star:==

画个折线图就很清晰了，只要股票呈上升趋势（prices[i - 1] < prices[i]），就计算此时的收益，并将其加到最终结果profit中。

> 贪心: 每一步总是做出在当前看来最好的选择。和动态规划相比，它既不看前面（也就是说它不需要从前面的状态转移过来），也不看后面（无后效性，后面的选择不会对前面的选择有影响）。

这道题 「贪心」 的地方在于，对于 「今天的股价 - 昨天的股价」，得到的结果有 3 种可能：① 正数，② 0，③负数。
贪心算法的决策是： 只加正数 。     

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int profit = 0;
        for (int i = 1; i < n; ++i) {
            if (prices[i - 1] < prices[i]) {
                profit += prices[i] - prices[i - 1];
            }
        }
        return profit;
    }
};
```

**复杂度分析**

- 时间复杂度：O(n)  

- 空间复杂度：O(n) / O(1)

### 法二

==:star: **核心思路：dp   **:star:==

原始版本

```c++
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    int maxprofit = 0; 
    // 1.确定dp数组以及下标含义：dp[i][0],dp[i][1]分别表示第i天持有股票,不持有股票的最多现有现金
    vector<vector<int>> dp(n, vector<int>(2));
    // 2.递推公式：
    // dp[i][0] = max(dp[i-1][0], -prices[i]);//维持持有股票的状态/当天才持有股票
    // dp[i][1] = max(dp[i-1][1], dp[i-1][0]-prices[i]);//维持不持有股票的状态/当天才不持有股票
    // 3.初始化
    dp[0][0] = -prices[0];
    dp[0][1] = 0;
    // 4.遍历
    for (int i = 1; i < n; ++i) {
        dp[i][0] = max(dp[i - 1][0], -prices[i]); 
        dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
    }
    return dp[n-1][1];
}
```

优化版本

```c++
int maxProfit(vector<int>& prices) {
    int n = prices.size();
    // dp1表示第i天持有股票能获得的最大利润
    // dp2表示第i天不持有股票能获得的最大利润
    int dp1 = -prices[0];
    int dp2 = 0;
    for (int i = 1; i < n; ++i) {
        dp1 = max(dp1, dp2 - prices[i]);//维持持有股票的状态  /当天才持有股票
        dp2 = max(dp2, dp1 + prices[i]);//维持不持有股票的状态/当天才不持有股票
    }
    return dp2;
}
```

复杂度分析

- 时间复杂度：O(n)  

- 空间复杂度：O(n) / O(1)

## :two:[123. 买卖股票的最佳时机 III](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/)

### 题意

给定一个数组，它的第 `i` 个元素是一支给定的股票在第 `i` 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 **两笔** 交易。

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。 

**示例 1:**

```
输入：prices = [3,3,5,0,0,3,1,4]
输出：6
解释：在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
     随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。
```

**示例 2：**

```
输入：prices = [1,2,3,4,5]
输出：4
解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。   
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。   
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
```

**示例 3：**

```
输入：prices = [7,6,4,3,1] 
输出：0 
解释：在这个情况下, 没有交易完成, 所以最大利润为 0。
```

**示例 4：**

```
输入：prices = [1]
输出：0
```

 **提示：**

- `1 <= prices.length <= 105`
- `0 <= prices[i] <= 105`

### 参考题解

1. [代码随想录](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/solutions/552849/123-mai-mai-gu-piao-de-zui-jia-shi-ji-ii-zfh9)

### 思路

==:star: **核心思路：dp  **:star:==

关键是分四个状态求dp以及初始化的问题（主要是第二次买卖的初始化）。

第0天第二次买入操作，初始值是多少？第一次还没买入呢，怎么初始化第二次买入？

—— 第二次买入依赖于第一次卖出的状态，其实就是第0天第一次买入又卖出再买入。

### 代码

```c++
int maxProfit(vector<int>& prices) {
    // 1. dp定义  
    // dp[i][0]表示在第i天第一次  持有股票能获取的最大利润
    // dp[i][1]表示在第i天第一次不持有股票能获取的最大利润
    // dp[i][2]表示在第i天第二次  持有股票能获取的最大利润
    // dp[i][3]表示在第i天第二次不持有股票能获取的最大利润
    vector<vector<int>> dp(prices.size(), vector<int>(4));

    // 2. 递推公式  
    // dp[i][0] = max(保持 持有状态，当天才 持有) = max(dp[i-1][0], -prices[i])
    // dp[i][1] = max(保持不持有状态，当天才不持有) = max(dp[i-1][1], dp[i-1][0] + prices[i])
    // dp[i][2] = max(保持 持有状态，当天才 持有) = max(dp[i-1][2], dp[i-1][1] - prices[i])
    // dp[i][3] = max(保持不持有状态，当天才不持有) = max(dp[i-1][3], dp[i-1][2] + prices[i])

    // 3. 初始化
    dp[0][0] = -prices[0];
    dp[0][2] = -prices[0]; //关键！

    // 4. 遍历
    for (int i = 1; i < prices.size(); ++i) {
        dp[i][0] = max(dp[i - 1][0], -prices[i]);
        dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
        dp[i][2] = max(dp[i - 1][2], dp[i - 1][1] - prices[i]);
        dp[i][3] = max(dp[i - 1][3], dp[i - 1][2] + prices[i]);

    }
    return dp[prices.size() - 1][3];
}
```

状态压缩

```cpp
int maxProfit(vector<int>& prices) {
    int dp0 = -prices[0]; // 第i天第一次  持有股票能获取的最大利润
    int dp1 = 0;          // 第i天第一次不持有股票能获取的最大利润
    int dp2 = -prices[0]; // 第i天第二次  持有股票能获取的最大利润
    int dp3 = 0;          // 第i天第二次不持有股票能获取的最大利润
    
    for (int i = 1; i < prices.size(); ++i) {
        dp3 = max(dp3, dp2 + prices[i]);
        dp2 = max(dp2, dp1 - prices[i]);
        dp1 = max(dp1, dp0 + prices[i]);
        dp0 = max(dp0, -prices[i]); 
    }
    return dp3;
}
```

其实这里就按从dp0\~dp3的顺序计算也行，为什么？

dp0 = max(dp0, - prices[i]); 

- 如果dp0取dp0，即保持持有状态，那么 dp1= max(dp1,  dp0 + prices[i]); 中dp0 + prices[i] 就是今天卖出。
- 如果dp0取 - prices[i]，即今天买入股票，那么dp1 = max(dp1, dp0 + prices[i]); 中dp0+ prices[i]相当于是今天再卖出股票，一买一卖收益为0，对所得现金没有影响。等于没有操作，保持昨天卖出股票的状态。

**复杂度分析**

- 时间复杂度：O(n)  

- 空间复杂度：O(n) / O(1)

# 编辑距离

## :fire:[72. 编辑距离](https://leetcode.cn/problems/edit-distance/)

### 题意

给你两个单词 `word1` 和 `word2`， *请返回将 `word1` 转换成 `word2` 所使用的最少操作数* 。

你可以对一个单词进行如下三种操作：

- 插入一个字符
- 删除一个字符
- 替换一个字符

**示例 1：**

```
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

**示例 2：**

```
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

**提示：**

- `0 <= word1.length, word2.length <= 500`
- `word1` 和 `word2` 由小写英文字母组成

### 思路

==:star: **核心思路：dp**:star:==  和:car:[1143. 最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/) 是差不多的思路

**:key:  key：**

![image-20240229101951255](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202402291019395.png)

**:question:   problems:** 

1. 可不可以让`dp[i][j]表示w1[0,i]与w2[0,j]要相等最少的操作次数` （dp数组初始化为`vector<vector<int>> dp(word1.size(), vector<int>(word2.size());`）?

:heavy_check_mark:  **answer:** 

1. 最好不要！你可能想写成这样：

    ```cpp
    int minDistance(string word1, string word2) {
        int size1 = word1.size();
        int size2 = word2.size();
        // 这样的dp定义必须要判断数组是否为空
        if (size1 == 0 || size2 == 0) {
            return size1 == 0 ? size2 : size1;
        }
        // dp定义: dp[i][j]表示使word1[0,i]==word2[0,j]所需的最少操作数
        vector<vector<int>> dp(size1, vector<int>(size2));
        // 初始化:
        for (int i = 0; i < size1; i++) {
            dp[i][0] = i; 
        }
        for (int j = 0; j < size2; j++) {
            dp[0][j] = j;
        } 
        // 遍历:
        for (int i = 1; i < size1; i++) {
            for (int j = 1; j < size2; j++) {
                if (word1[i] == word2[j]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = 1 + min(dp[i - 1][j - 1], min(dp[i - 1][j], dp[i][j - 1]));
                }
            }
        }
        return dp[size1 - 1][size2 - 1];
    }
    ```

    如果这样写，初始化那里的逻辑就错了! 如果按`"dp[i][j]表示使word1[0,i]==word2[0,j]所需的最少操作数"`这个dp定义, 那么dp\[0][0]就表示"使word1[0]==word2[0]所需的最少操作数", 不是使两个空字符串相等所需的最少操作数,不要想岔了！

    那如果初始化逻辑改成这样呢？

    ```cpp
    dp[0][0] = word1[0] == word2[0] ? 0 : 1;
    for (int i = 1; i < size1; i++) {
        dp[i][0] = dp[i - 1][0] + 1; 
    }
    for (int j = 1; j < size2; j++) {
        dp[0][j] = dp[0][j - 1] + 1;
    }
    ```

    同样会初始化错误! 因为比如"sea"和"eat", dp\[0][0] = 1, 如果按这样计算的话,dp\[1][0]=1+1=2, 而事实上dp\[1][0]=1。

    因此如果按`"dp[i][j]表示使word1[0,i]==word2[0,j]所需的最少操作数"`这个dp定义, 那么初始化逻辑就会比较麻烦！

    而如果按`"dp[i][j]表示w1[0,i-1]与w2[0,j-1]要相等最少的操作次数"`这个dp定义, 那么初始化逻辑: dp\[0][j]或dp\[i][0]都是一个字符串与另一个**空字符串**相等需要的最少操作次数，直接就=其元素个数了，初始化很方便。

### 代码 

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size() + 1;
        int n = word2.size() + 1; 
        // dp定义: dp[i][j]表示使word1[0,i-1]==word2[0,j-1]所需的最少操作数
        vector<vector<int>> dp(m, vector<int>(n));
        // 初始化: dp[0][j]或dp[i][0]都是一个字符串与另一个空字符串相等需要的最少操作次数，=其元素个数
        for (int i = 0; i < m; i++) {
            dp[i][0] = i; 
        }
        for (int j = 0; j < n; j++) {
            dp[0][j] = j;
        }
        // 遍历顺序：由于要用到左、上、左上三个格子，因此从上往下从左往右遍历
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                // 这里最好自己在脑子里面画出一个二维dp数组的图，或者手动画出来，不要凭空想
                /*
                      h  o  r  
                    r    2  2
                    o    1  2
                */
                    dp[i][j] = 1 + min(dp[i - 1][j - 1], min(dp[i - 1][j], dp[i][j - 1]));
                }
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

状态压缩：

```c++
因为dp[i][j]只和dp[i-1][j],dp[i][j-1],dp[i-1][j-1]三个量有关，即当前元素的上边，左边，左上角三个元素。
状态压缩为一维数组后dp[i-1][j],dp[i][j-1],dp[i-1][j-1]分别对应：
    dp[i-1][j]  :当前位置刷新前的值（当前位置上一层的值）、
    dp[i][j-1]  :当前位置的左边元素刷新后的值（当前位置的左边元素当前层的值）、
    dp[i-1][j-1]:当前位置的左边元素刷新前的值（当前位置的左边元素上一层的值）.
遍历到dp[j]时，左边上一层的值dp[i-1][j-1]已经被当前层的值覆盖了(已经被刷新为dp[i][j-1]了)，因此我们需要一个变量leftTop保存左上角的值。
那在什么时候保存？从左往右每次刷新一个值之前(tmp)，下一次循环 刷新时用的leftTop就是上一次循环刷新完成后更新的leftTop，刷新后再将leftTop更新为该次刷新前保存的值(tmp)以用于下一循环
```

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size() + 1;
        int n = word2.size() + 1; 
        // dp定义: dp[j]表示使word1[0,i-1]==word2[0,j-1]所需的最少操作数
        vector<int> dp(n);
        // 初始化i=0这一行作为滚动数组的原型 (和[1143. 最长公共子序列]的压缩dp不一样是因为1143中dp[0][j]本来就全为0)
        for (int j = 0; j < n; ++j) {
            dp[j] = j;
        }
        for (int i = 1; i < m; i++) { 
            int leftUp = dp[0];//初始化leftUp
            dp[0] = i;//刷新dp[0]
            for (int j = 1; j < n; j++) {
                int tmp = dp[j];//保存当前格子刷新前的值，作为下一层内存循环的leftUp
                if (word1[i - 1] == word2[j - 1]) {
                    dp[j] = leftUp;
                } else {
                    dp[j] = 1 + min(leftUp, min(dp[j], dp[j - 1]));
                }
                leftUp = tmp;// 更新左上角的值
            }
        }
        return dp[n - 1];
    }
};
```

### 复杂度分析

- 时间复杂度：O(n*m)  

- 空间复杂度：O(n*m) / O(1)



# 方阵型

## :fire:[221. 最大正方形](https://leetcode.cn/problems/maximal-square/)

### 题意

在一个由 `'0'` 和 `'1'` 组成的二维矩阵内，找到只包含 `'1'` 的最大正方形，并返回其面积。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/11/26/max1grid.jpg)

```
输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
输出：4
```

**示例 2：**

![img](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403081047086.jpeg)

```
输入：matrix = [["0","1"],["1","0"]]
输出：1
```

**示例 3：**

```
输入：matrix = [["0"]]
输出：0
```

**提示：**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 300`
- `matrix[i][j]` 为 `'0'` 或 `'1'`

### 思路

==:star: **核心思路：dp  **:star:==

![SmartSelect_20240308_111217_Samsung Notes](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403081112263.jpg)

### 代码

==看清楚！matrix存放的是char类型的字符，而不是int！==

```c++
int maximalSquare(vector<vector<char>>& matrix) {
    int maxSide = 0;
    int m = matrix.size(); 
    int n = matrix[0].size(); 
    // 1.dp定义: dp[i][j]表示以(i,j)为右下角的只包含'1'的最大正方形的边长
    vector<vector<int>> dp(m, vector<int>(n, 0));
    // 2.递推公式: dp[i][j]和左、上、左上三个格子有关 
    // 3.初始化: 写在4.遍历的逻辑中
    // 4.遍历
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (matrix[i][j] == '0' || i == 0 || j == 0) {
                dp[i][j] = matrix[i][j] - '0';
            } else {
                dp[i][j] = 1 + min(dp[i - 1][j - 1], min(dp[i][j - 1], dp[i - 1][j]));
            }
            maxSide = max(maxSide, dp[i][j]);
        }
    }
    int maxSquare = maxSide * maxSide;
    return maxSquare;
}
```

### 复杂度分析

- 时间复杂度：O(n*m)  

- 空间复杂度：O(n*m)

## :fire:[64. 最小路径和](https://leetcode.cn/problems/minimum-path-sum/)

### 题意

给定一个包含非负整数的 `m x n` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：**每次只能向下或者向右移动一步。 

**示例 1：**

![img](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403081323697.jpeg)

```
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

**示例 2：**

```
输入：grid = [[1,2,3],[4,5,6]]
输出：12
```

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 200`
- `0 <= grid[i][j] <= 200`

### 代码

一种写法：不单独初始化，而是写在遍历逻辑中

```c++
int minPathSum(vector<vector<int>>& grid) {
    int m = grid.size(); 
    int n = grid[0].size(); 
    // 1.dp定义: dp[i][j]表示走到(i,j)的最小路径和
    vector<vector<int>> dp(m, vector<int>(n));
    // 2.递推公式: dp[i][j]和左、上有关 
    // 3.初始化: 写在4.遍历逻辑中
    // 4.遍历
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if(i == 0 && j == 0) {
                dp[0][0] = grid[0][0];
            } else if(i == 0) {
                dp[0][j] = dp[0][j - 1] + grid[0][j];
            } else if (j == 0) {
                dp[i][0] = dp[i - 1][0] + grid[i][0];
            } else {
                dp[i][j] = grid[i][j] + min(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    return dp[m - 1][n - 1];
}
```

另一种写法：将第一行和第一列单独拿出来初始化

```c++
int minPathSum(vector<vector<int>>& grid) {
    int m = grid.size(); 
    int n = grid[0].size(); 
    // 1.dp定义: dp[i][j]表示走到(i,j)的最小路径和
    vector<vector<int>> dp(m, vector<int>(n));
    // 2.递推公式: dp[i][j]和左、上有关 
    // 3.初始化 
    dp[0][0] = grid[0][0];
    for (int i = 1; i < m; i++) {
        dp[i][0] = dp[i - 1][0] + grid[i][0];
    }
    for (int j = 1; j < n; j++) {
        dp[0][j] = dp[0][j - 1] + grid[0][j];
    }
    // 4.遍历
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            dp[i][j] = grid[i][j] + min(dp[i - 1][j], dp[i][j - 1]);
        }
    }
    return dp[m - 1][n - 1];
}
```

**复杂度分析**

- 时间复杂度：O(n*m)  

- 空间复杂度：O(n*m)

### 进阶：要求输出最小路径

可参考：[宫水三叶](https://leetcode.cn/problems/minimum-path-sum/solutions/650010/dong-tai-gui-hua-lu-jing-wen-ti-ni-bu-ne-fkil)、[多次拒绝刘亦菲](https://leetcode.cn/problems/minimum-path-sum/solutions/1958877/ji-suan-chu-jie-guo-bing-qie-shengc-by-m-dbac)、[Go刷算法 - 最小路径和](https://zhuanlan.zhihu.com/p/680605185)

> 从右下角开始回溯路径

左边和上面值一样的话，怎么判断从哪条路继承过来的？——这种情况走哪条路都是一样的。





## :fire:[62. 不同路径](https://leetcode.cn/problems/unique-paths/)

### 题意

一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

**提示：**

- `1 <= m, n <= 100`
- 题目数据保证答案小于等于 `2 * 109`

### 法一

dp

```c++
int uniquePaths(int m, int n) {
    // dp定义：dp[i][j]表示机器人走到(i,j)有多少种路径
    vector<vector<int>> dp(m, vector<int>(n));
    // 递推公式：dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
    // 初始化：i=0||j=0时都只有一条路径，即往下或往右直走，dp[i][j]=1
    // 遍历:
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (i == 0 || j == 0) {
                dp[i][j] = 1;
            } else {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
    }
    return dp[m - 1][n - 1];
}
```

dp状态压缩

```c++
int uniquePaths(int m, int n) {
    // 递推公式：当前格子的dp值 = 滚动前当前格子的值 + 左边格子滚动后的值
    //  dp[j] += dp[j - 1]
    // 初始化：i=0||j=0时都只有一条路径
    // 遍历
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (i == 0 || j == 0) {
                dp[j] = 1;
            } else {
                dp[j] += dp[j - 1];
            }
        }
    }
    return dp[n - 1];
}
```

**复杂度分析**

- 时间复杂度：O(m*n)  

- 空间复杂度：O(m*n) / O(n)

### 法二

数论方法

- 因为不论怎么走都需要往下走m-1步，往右走n-1步，因此总共需要走m+n-2步，而这m+n-2步中往下的m-1步怎么走(有几种走法)，就是组合问题了，因此**路径的总数就等于从m+n-2次移动中选择m−1次向下移动的方案数**，即组合数：`C（m+n-2，m-1）=(m+n-2)!/(m-1)!((m+n-2)-(m-1))!)=(m+n-2)!/(m-1)!(n-1)!`

- 另外计算累乘求组合的时候注意！要防止相乘溢出！ 所以不能把分子分母都算出来再做除法！而是 分母的累乘 与 分母/分子 要同时进行

![SmartSelect_20240311_143118_Samsung Notes](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403111432513.jpg)

```c++
int uniquePaths(int m, int n) {
    long long res = 1;
    for (int x = n, y = 1; y < m; ++x, ++y) { //循环m-1次
        res = res * x / y;
    }
    return res;
}
```

**复杂度分析**

- 时间复杂度：O(m) 

- 空间复杂度：O(1)

# 游戏

## :two:[55. 跳跃游戏](https://leetcode.cn/problems/jump-game/)

### 题意

给你一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标，如果可以，返回 `true` ；否则，返回 `false` 。

**示例 1：**

```
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

**示例 2：**

```
输入：nums = [3,2,1,0,4]
输出：false
解释：无论怎样，总会到达下标为 3 的位置。但该下标的最大跳跃长度是 0 ， 所以永远不可能到达最后一个下标。
```

**提示：**

- `1 <= nums.length <= 104`
- `0 <= nums[i] <= 105`

### 参考题解

1. [Boah-Blake](https://leetcode.cn/problems/jump-game/solutions/2087455/dong-tai-gui-hua-jie-fa-dpi-maxdpi-1-num-94bw)

### 思路

- 暴力：从位置 i 开始，判断从位置0~i-1跳，是否能跳到 i 。这样需嵌套for循环。
- 优化：遍历nums，计算当前位置 i 能到达的最远的下标。如果出现有某个位置能到达的最远的下标 >= nums.size()-1，就说明可以跳到最后一个下标。这样只需一个for循环。
- 进一步优化：状态压缩

### 代码

```c++
bool canJump(vector<int>& nums) {
    // dp定义：dp[i]表示是否能跳到下标i的位置
    vector<int> dp(nums.size());
    // 递推公式：如果 dp[j]==true 且 j + nums[j] >= i，dp[i] = true;
    // 初始化
    dp[0] = true;
    for (int i = 1; i < nums.size(); ++i) {
        for (int j = 0; j < i; ++j) {
            if (dp[j] && j + nums[j] >= i) {
                dp[i] = true;
                break;
            }
        }
    }
    return dp[nums.size() - 1];

}
```

优化：遍历nums，计算当前位置 i 能到达的最远的下标。如果出现有某个位置能到达的最远的下标 >= nums.size()-1，就说明可以跳到最后一个下标。这样只需一个for循环。

```cpp
bool canJump(vector<int>& nums) {
    if (nums.size() <= 1) {
        return true;
    }
    // dp定义：dp[i]表示从[0,i]的任意一点处出发，最远可以到哪
    vector<int> dp(nums.size());
    dp[0] = nums[0];
    for (int i = 1; i < nums.size(); ++i) {
        if (dp[i - 1] < i) {
            return false;
        }
        // 当前位置 i 能到达的最远的下标，和前一个位置能到达的最远的下标有关
        // 但如果前一个位置都到不了，当前位置就不用再看了，反正也到不了。因此在计算dp[i]之前需判断是否到的了i-1下标的位置
        dp[i] = max(dp[i - 1], nums[i] + i); 
        if (dp[i] >= nums.size() - 1) {
            return true;
        }
    }
    return false;
}
```

进一步优化：状态压缩

```c++
bool canJump(vector<int>& nums) {
    if (nums.size() <= 1) {
        return true;
    }
    int dp = nums[0]; //表示能到达的最远下标
    for (int i = 1; i < nums.size(); ++i) {
        if (dp < i) {
            return false;
        }
        dp = max(dp, nums[i] + i);
        if (dp >= nums.size() - 1) {
            return true;
        }
    }
    return false;
}
```

### 复杂度分析

- 时间复杂度：O(n)  

- 空间复杂度：O(n) / O(1)

# 树

## :fire:[96. 不同的二叉搜索树](https://leetcode.cn/problems/unique-binary-search-trees/)

### 题意

给你一个整数 `n` ，求恰由 `n` 个节点组成且节点值从 `1` 到 `n` 互不相同的 **二叉搜索树** 有多少种？返回满足题意的二叉搜索树的种数。

**示例 1：**

![img](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403261044676.jpeg)

```
输入：n = 3
输出：5
```

**示例 2：**

```
输入：n = 1
输出：1
```

**提示：**

- `1 <= n <= 19`

### 参考题解

1. [笨猪爆破组](https://leetcode.cn/problems/unique-binary-search-trees/solutions/330990/shou-hua-tu-jie-san-chong-xie-fa-dp-di-gui-ji-yi-h)

### 思路

==:star: **核心思路：dp  **:star:==

```c++
int numTrees(int n) {
    // 1.dp[i]表示由i个结点组成的二叉搜索树有多少种
    vector<int> dp(n + 1); 
    // 2.递推公式: dp[i] += dp[j] * dp[i - 1 - j];
    // 3.dp初始化
    dp[0] = 1; //空树也是二叉搜索树!!!
    // 4.遍历
    for (int i = 1; i <= n; i++) {
        // 根占一个结点，剩下左右子共i-1个结点
        for (int j = 0; j < i; ++j) {
            dp[i] += dp[j] * dp[i - 1 - j];
        }
    }
    return dp[n];
}
```

### 复杂度分析

- 时间复杂度：O(n^2^)  

- 空间复杂度：O(n)

## :fire:[337. 打家劫舍 III](https://leetcode.cn/problems/house-robber-iii/)

### 题意

小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为 `root` 。

除了 `root` 之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果 **两个直接相连的房子在同一天晚上被打劫** ，房屋将自动报警。

给定二叉树的 `root` 。返回 ***在不触动警报的情况下** ，小偷能够盗取的最高金额* 。

 **示例 1:**

![img](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202407231000580.jpeg)

```
输入: root = [3,2,3,null,3,null,1]
输出: 7 
解释: 小偷一晚能够盗取的最高金额 3 + 3 + 1 = 7
```

**示例 2:**

![img](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202403301120582.jpeg)

```
输入: root = [3,4,5,1,3,null,1]
输出: 9
解释: 小偷一晚能够盗取的最高金额 4 + 5 = 9
```

**提示：**

- 树的节点数在 `[1, 104]` 范围内
- `0 <= Node.val <= 104`

### 参考题解

1. 

### 法一

==:star: **核心思路：『树形』『DP』   **:star:==

**:key:  key：**

- 『树形』『DP』：每一个节点能偷到的最大价值由偷与不偷左右子树的情况转移而来

- 后序遍历，dp[i] = max(不偷当前结点，偷当前结点);
    - 不偷当前结点，那就可以偷其左右子，但是到底偷不偷左右子还要取决于左右子树的最大收益，比如可能左子树的最大收益是不偷左子得到的。
    - 偷当前结点，那就不能偷左右节点

#### 代码

```c++
pair<int, int> postTraversal(TreeNode* root) {
    // 1. 递归函数返回值：pair.first表示不偷当前节点能得到的最大价值，pair.second表示偷··· 
    // 2. 递归结束条件：到空结点时
    if(!root) return {0, 0};
    // 3. 单层递归做啥事：求root的dp值 => 通过偷与不偷子节点能获取的收益来决定自己要不要偷
    auto [notSteal_left, Steal_left] = postTraversal(root->left);   // 左
    auto [notSteal_right, Steal_right] = postTraversal(root->right);// 右
    // 不偷当前节点，此时最大价值考虑的是左/右整个两个子树的最大收益，而不单单是左右子这两个结点的，
    // 因此不能写成 notsteal = Steal_left + Steal_right
    int notsteal = max(notSteal_left, Steal_left) + max(notSteal_right, Steal_right);
    // 偷当前节点，则其左右孩子都不能偷
    int steal = root->val + notSteal_left + notSteal_right;
    return {notsteal, steal};
}
int rob(TreeNode* root) {
    auto [notSteal, Steal] = postTraversal(root);
    return max(notSteal, Steal);
}
```

#### 复杂度分析

- 时间复杂度：O(n)  

- 空间复杂度：O(n)



# 扔鸡蛋

[扔鸡蛋问题_public static int eggdrop(int m, int n)-CSDN博客](https://blog.csdn.net/Oblak_ZY/article/details/124585317#comments_28600776)
