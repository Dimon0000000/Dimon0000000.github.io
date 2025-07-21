---
title: LeetCode Weekly Contest 305
date: 2022-08-07 13:12:19
tags:
- LeetCode Contest
- 枚举
- 广度优先搜索(BFS)
- 动态规划
categories: LeetCode
index_img: https://s2.loli.net/2022/10/24/ti5VJEc6s13vFfC.jpg
---

My Submission

> Problem AC: 4
>
> Problem Try: 4
>
> Wrong Answer: 1

### Problem A：[算术三元组的数目](https://leetcode.cn/problems/number-of-arithmetic-triplets/)

#### 题意分析

-----

签到题：暴力模拟

#### 参考代码

----

```cpp
class Solution {
public:
    int arithmeticTriplets(vector<int>& nums, int diff) {
        int n = nums.size();
        int ans = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                for (int k = j + 1; k < n; ++k) {
                    if (nums[j] - nums[i] == diff && nums[k] - nums[j] == diff) ans++;
                }
            }
        }
        return ans;
    }
};
```



### Problem B：[受限条件下可到达节点的数目](https://leetcode.cn/problems/reachable-nodes-with-restrictions/)

#### 题意分析

-----

广度优先搜索：先将限制节点设为已访问，再广度优先遍历图即可

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define loop(t) while (t-- > 0)

using vi = vector<int>;
using vvi = vector<vi>;

class Solution {
public:
    int reachableNodes(int n, vector<vector<int>>& edges, vector<int>& restricted) {
        vvi grid(n);
        for (vi e: edges) {
            grid[e[0]].push_back(e[1]);
            grid[e[1]].pb(e[0]);
        }
        int ans = 0;
        set<int> vis;
        for (int r: restricted) vis.insert(r);
        queue<int> queue;
        queue.push(0);

        while (!queue.empty()) {
            int size = queue.size();
            loop(size) {
                int pos = queue.front();
                queue.pop();

                if (vis.count(pos)) continue;

                if (!vis.count(pos)) ans++, vis.insert(pos);

                for (int v: grid[pos]) if (!vis.count(v)) queue.push(v);
            }
        }
        return ans;
    }
};
```



### Problem C：[6137. 检查数组是否存在有效划分](https://leetcode.cn/problems/check-if-there-is-a-valid-partition-for-the-array/)

#### 题意分析

-----

动态规划：

+ 定义 `dp[i]` 表示从 $$\textit{nums}[0]$$ 到 $$\textit{nums}[i]$$ 的这些元素能否有效划分。
+ 初始化`dp[0] = 1`
+ 状态转移有
  + 和前一位相同: `dp[i] = dp[i-1] && nums[i] == nums[i-1]`
  + 和前两位相同: `dp[i] = dp[i-2] && nums[i] == nums[i-1] == nums[i-2]`
  + 和前两位呈等差数列: `dp[i] = dp[i-2] && nums[i] == nums[i-1] + 1 == nums[i-2] + 2`
+ 最终结果位于`dp[n]`

#### 参考代码

----

```cpp
using vb = vector<bool>;

class Solution {
public:
    bool validPartition(vector<int> &nums) {
        int n = nums.size();
        vb dp(n, 0);
        dp[0] = 1;
        
        for (int i = 1; i < n; ++i)
            if (dp[i - 1] && nums[i] == nums[i - 1] ||
                i > 1 && dp[i - 2] && (nums[i] == nums[i - 1] && nums[i] == nums[i - 2] ||
                                       nums[i] == nums[i - 1] + 1 && nums[i] == nums[i - 2] + 2))
                dp[i + 1] = 1;
        return dp[n];
    }
};
```



### Problem D：[最长理想子序列](https://leetcode.cn/problems/longest-ideal-subsequence/)

#### 题意分析

-----

动态规划：

+ `dp[i]` 表示以字符 `i` 为结尾的最长理想子序列
+ 对于字符`s`中的每一个字符，枚举其绝对差在`k`以内的字符，在其后形成新的理想子序列

#### 参考代码

----

```cpp
using vi = vector<int>;

class Solution {
public:
    int longestIdealString(string &s, int k) {
        vi cnt(26, 0);
        for (char c: s) {
            c -= 'a';
            cnt[c] = 1 + *max_element(cnt.begin() + max(c - k, 0), cnt.begin() + min(c + k + 1, 26));
        }
        return *max_element(all(cnt));
    }
};
```

