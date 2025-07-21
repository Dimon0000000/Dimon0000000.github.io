---
title: LeetCode Weekly Contest 304
date: 2022-07-31 19:43:24
tags:
- LeetCode Contest
- 堆(优先队列)
- 哈希表
- 脑筋急转弯
- 广度优先搜索(BFS)
- 深度优先搜索(DFS)
- 拓扑排序
categories: LeetCode
index_img: https://smms.app/image/2utJoWYC3qQes9w
---

> My Submission
>
> > Problem AC: 4
> >
> > Problem Try: 4
> >
> > Wrong Answer: 2

### Problem A：[使数组中所有元素都等于零](https://leetcode.cn/problems/make-array-zero-by-subtracting-equal-amounts/)

#### 题意分析

----

签到题，优先队列 + 标记

#### 参考代码

----

```cpp
class Solution {
public:
    int minimumOperations(vector<int>& nums) {
        priority_queue<int, vector<int>, greater<>> pq;
        int lazy = 0, ans = 0;
        for (int v: nums) if (v != 0) pq.push(v);
        while (!pq.empty() && lazy != pq.top()) {
            int num = pq.top();
            lazy += num - lazy;
            ans++;
            while (!pq.empty() && pq.top() == num) pq.pop();
        }
        return ans;
    }
};
```

### Problem B：[分组的最大数量](https://leetcode.cn/problems/maximum-number-of-groups-entering-a-competition/)

#### 题意分析

----

脑筋急转弯：

按等差数列分组，若每次可以满足则下一个组人数 + 1，否则直接结束循环

#### 参考代码

-----

```cpp
class Solution {
public:
    int maximumGroups(vector<int>& grades) {
        int ans = 1, now = 1, next = 2;

        while (now + next <= grades.size()) {
            ans++;
            now += next;
            next++;
        }
        return ans;
    }
};
```

### Problem C：[找到离给定两个节点最近的节点 ](https://leetcode.cn/problems/find-closest-node-to-given-two-nodes/)

### 题意分析

----

广度优先搜索 + 记录距离

遍历`node1`和`node2`的所有节点并统计距离，再遍历距离返回符合要求的答案

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back

#define loop(t) while (t-- > 0)
#define enhanceFor(t) for (auto it: t)

typedef unordered_map<int, int> umii;

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int i32 = 0x3f3f3f3f;
const ll i64 = 0x3f3f3f3f3f3f3f3f;

using vvi = vector<vi>;

class Solution {
public:
    int closestMeetingNode(vector<int>& edges, int node1, int node2) {
        int n = edges.size();
        vvi grid(n);

        for (int i = 0; i < n; ++i) {
            if (edges[i] == -1) continue;
            grid[i].pb(edges[i]);
        }
        
        int dis = 1;
        us<int> vis;
        queue<int> queue;
        umii u1,u2;
        
        queue.push(node1);
        while (!queue.empty()) {
            int size = queue.size();
            loop(size) {
                int pos = queue.front();
                queue.pop();
                if (vis.count(pos)) continue;
                
                vis.insert(pos);
                u1[pos] = dis;
                for (int v: grid[pos]) queue.push(v);
            }
            dis++;
        }
        
        dis = 1;
        vis.clear();
        
        queue.push(node2);
        while (!queue.empty()) {
            int size = queue.size();
            loop(size) {
                int pos = queue.front();
                queue.pop();
                if (vis.count(pos)) continue;

                vis.insert(pos);
                u2[pos] = dis;
                for (int v: grid[pos]) queue.push(v);
            }
            dis++;
        }
        
        int ans = -1;
        dis = i32;

        enhanceFor(u1) {
            if (u2.count(it.first)) {
                if (max(it.second, u2[it.first]) < dis) {
                    dis = max(it.second, u2[it.first]);
                    ans = it.first;
                } else if (max(it.second, u2[it.first]) == dis) {
                    ans = min(ans, it.first);
                }
            }
        }
        return ans;
    }
};
```

### Problem D：[图中的最长环](https://leetcode.cn/problems/longest-cycle-in-a-graph/)

#### 题意分析

----

给定一个图，返回其中最大的环

拓扑排序 + 深度优先搜索

+ 先使用拓扑排序的方法，确定图种环的节点序号
+ 对于每一个环上的点深搜确定其长度

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define fori(a, b) for (int i = a; i < b; i++)
#define enhanceFor(t) for (auto it: t)
#define us unordered_set

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int i32 = 0x3f3f3f3f;
const ll i64 = 0x3f3f3f3f3f3f3f3f;

using vi = vector<int>;
using vvi = vector<vi>;

class Solution {
public:
    int ans = -1;
    us<int> set;
    
    void dfs(vvi &grid, int dee, int pos) {
        if (set.count(pos)) {
            ans = max(ans, dee);
            return;
        }
        set.insert(pos);
        for (int v: grid[pos]) {
            dfs(grid, dee + 1, v);
        }
    }
    
    int longestCycle(vector<int>& edges) {
        int n = edges.size();
        vi systemIO(n,0);
        vvi grid(n);
        
        for (int i = 0; i < n; ++i) {
            if (edges[i] != -1) {
                systemIO[edges[i]]++;
                grid[i].pb(edges[i]);
            }
        }
        
        queue<int> queue;
        fori(0,n){
            if (systemIO[i]==0) queue.push(i);
        }

        while (!queue.empty()) {
            int size = queue.size();
            loop(size){
                int pos = queue.front();
                queue.pop();

                enhanceFor(grid[pos]) {
                    systemIO[it]--;
                    if (systemIO[it] == 0) queue.push(it);
                }
            }
        }

        fori(0,n){
            if (!set.count(i) && systemIO[i] == 1) {
                dfs(grid, 0, i);
            }
        }
        return ans;
    }
};
```

