---
title: LeetCode Weekly Contest Album (307 - 317)
date: 2022-08-20 08:14:08
tags:
- LeetCode Contest
- 差分数组
- 贪心
- 广度优先搜索(BFS)
- 拓扑排序
- 模拟
- 哈希表
categories: LeetCode
index_img: https://s2.loli.net/2022/10/24/EQuDCg87i3qjKto.jpg
---

### [B85 - T3 字母移位 II](https://leetcode.cn/problems/shifting-letters-ii/)

#### 题意分析

----------

每次将给的区间内的字母`+1`或`-1`

区间和问题，可以使用差分数组来完成

#### 参考代码

-----

```cpp
class Solution {
public:
    string shiftingLetters(string s, vector<vector<int>>& shifts) {
        int n = s.size();
        int cnt[n];
        memset(cnt, 0, sizeof(cnt));

        for(vector<int> a:shifts){
            int start=a[0],end=a[1],op=a[2];
            if (op==0){
                cnt[start]--;
                if (end+1<n)cnt[end+1]++;
            } else{
                cnt[start]++;
                if (end+1<n)cnt[end+1]--;
            }
        }
        for(int i = 1;i<n;++i)
            cnt[i]+=cnt[i-1];
        

        for (int i = 0; i < n; ++i) {
            cnt[i]+=(s[i]-'a');
            while(cnt[i]<0)cnt[i]+=26;
            if(cnt[i]>=26)cnt[i]%=26;
        }
   
        string a;
        for (int i = 0; i < n; ++i) 
            a+=(char)(cnt[i] + 'a');
        
        return a;
    }
};
```



### [B85 - T4 删除操作后的最大子段和](https://leetcode.cn/problems/maximum-segment-sum-after-removals/)

#### 题意分析

-------

每次删除数组中的一个数字，将数组分成多个子段

返回每次分割后的，子串的最大和

逆向遍历 + 并查集：

+ 正向为删除，逆向则为链接子段
+ 每次要链接相应的子段，可以考虑使用并查集
+ 添加 $$x=removeQueries[i]$$ 时，用并查集合并`x`和`x+1`，并把`nums[x]`加入到子段中
+ *ans*[*i*] 要么取上一个*ans[i+1]*的子段和，要么取合并后的子段和(二者取最大值)

#### 参考代码

----

```cpp
class Solution {
    typedef long long ll;
public:
    vector<long long> maximumSegmentSum(vector<int>& nums, vector<int>& removeQueries) {
        int n = nums.size();
        int f[n+1];
        iota(f, f + n + 1, 0);
        ll sum[n+1];
        memset(sum, 0, sizeof(sum));

        function<int(int)> find = [&](int x) -> int { return f[x] == x ? x : f[x] = find(f[x]); };
        std::vector<ll> ans(n);

        for(int i=n-1; i>0; --i){
            int x = removeQueries[i];
            int to = find(x+1);
            f[x]=to;
            sum[to]+= sum[x] + nums[x];
            ans[i-1] = max(ans[i], sum[to]);
        }
        return ans;
    }
};
```



### [W307-T2 最大回文数字](https://leetcode.cn/problems/largest-palindromic-number/)

#### 题意分析

----

本题主要使用贪心算法解决，读者很快就可以想到，有趣的是在如何减少字符串相加带来的时间开销和处理前缀0的问题

+ 对于字符串相加问题，由于题目要求得到回文串，我们可以在增加字符时只增加一半，剩下一半通过反转后再相加，从而减少了时间开销
+ 对于前缀0问题，我们可以使用一个`flag`来统计，由于0只能在结果字符串的中间部分，则若向字符串内增加了0，暂时将`flag`标记为`1`。若后续向0前方又增加了数字，此时便可以将`flag`置`0`(没有前缀0问题了)

#### 参考代码

------

```cpp
class Solution {
public:
    string largestPalindromic(string num) {
        map<int, int> map;
        for(char c:num)map[c-'0']++;
        if(map.size() == 1) {
            return map.begin()->first == 0 ? "0" : num;
        }
        string a;
        bool flag = false;
        for(auto &it: map){
            int to = it.second;
            if(to > 1){
                if(!flag && it.first == 0) flag = 1;
                else flag = 0;
                
                for(int i = 0; i < to / 2; ++i) {
                    a.push_back((char)(it.first + '0'));
                }
                if(to % 2 == 1) it.second = 1;
                else it.second = 0;
            }
        }
        string b;
        if (flag) b = "";
        else {
            b = a; 
            reverse(b.begin(), b.end());
            b += a;
        }
        int mid = b.size() / 2;
        for (auto it = map.rbegin(); it != map.rend(); ++it){
            if (it->second != 0) {
                b.insert(b.begin() + mid, (char)(it->first + '0'));
                break;
            }
        }
        return b;
    }
};
```



### [W307 - T3 感染二叉树需要的总时间](https://leetcode.cn/problems/amount-of-time-for-binary-tree-to-be-infected/)

#### 题意分析

----

本题没有什么思维量，仅存的难点在于由`TreeNode`建图

作者此处使用了较为方便的`unordered_map`建图

#### 参考代码

----

```cpp
class Solution {
public:
    void dfs(unordered_map<int, vector<int>> &map, TreeNode* root){
        if (root== nullptr) return;
        if (root->left!= nullptr)map[root->val].push_back(root->left->val),map[root->left->val].push_back(root->val), dfs(map, root->left);
        if (root->right!= nullptr)map[root->val].push_back(root->right->val),map[root->right->val].push_back(root->val), dfs(map,root->right);
    }

    int amountOfTime(TreeNode* root, int start) {
        unordered_map<int, vector<int>> map;
        dfs(map, root);

        queue<int> queue;
        queue.push(start);

        int ans = 0, n = map.size();
        unordered_set<int> set;


        while (!queue.empty()){
            int size = queue.size();
            while (size-->0){
                int pos = queue.front();
                queue.pop();

                if(set.count(pos)) continue;
                set.insert(pos);

                for(int v: map[pos]) if (!set.count(v)) queue.push(v);
            }
            ++ans;
        }
        return ans - 1;
    }
};
```



### [W308-T3 收集垃圾的最少总时间](https://leetcode.cn/problems/minimum-amount-of-time-to-collect-garbage/)

#### 题意分析

----

每次只能使用一辆垃圾车，每次必须收集完全部的垃圾

则可以对每一栋房子中的三种垃圾以及所有的三种垃圾计数

直到采集完成后结束

#### 参考代码

-------

```cpp
class Solution {
public:
    int garbageCollection(vector<string>& garbage, vector<int>& travel) {
        unordered_map<int, unordered_map<char, int>> map;
        unordered_map<char, int> cnt;
        int n = garbage.size();

        for (int i=0; i<n; ++i) {
            string g = garbage[i];
            for(char c:g) map[i][c]++,++cnt[c];
        }

        char a[3] = {'P','M','G'};

        int ans=0;

        for (const char& c: a){
            int p=0;
            while (cnt[c]>0){
                ans += map[p][c],cnt[c]-=map[p][c];
                if (cnt[c]>0)ans+=travel[p],++p;
                else break;
            }
        }
        return ans;
    }
};
```



### [W308-T4 给定条件下构造矩阵](https://leetcode.cn/problems/build-a-matrix-with-conditions/)

#### 题意分析

-----

给的一个行元素限制和列元素限制，返回按照限制之下所能构成的矩阵

由于元素之间只有先后出现之分，可以使用拓扑排序来确定每一个元素由下到上的出现次序和由右到左的出现次序

#### 参考代码

-----

```cpp
class Solution {
    vector<int> topo_sort(int k, vector<vector<int>>& edges) {
        vector<vector<int>> grid(k);
        vector<int> in(k);

        for(auto &e: edges){
            int x = e[0]-1,y=e[1]-1;
            grid[x].push_back(y);
            in[y]++;
        }

        vector<int> order;
        queue<int> queue;
        for(int i=0; i<k; ++i)
            if(in[i]==0)queue.push(i);
        
        while(!queue.empty()){
            int pos = queue.front();
            queue.pop();
            order.push_back(pos);
            for(int v: grid[pos])if(--in[v]==0)queue.push(v);
        }
        return order;
    }

public:
    vector<vector<int>> buildMatrix(int k, vector<vector<int>>& rowConditions, vector<vector<int>>& colConditions) {
        auto row=topo_sort(k, rowConditions), col=topo_sort(k,colConditions);
        if(row.size()<k || col.size()<k)return {};
        vector<int> pos(k);
        for(int i=0; i<k; ++i)pos[col[i]]=i;

        vector<vector<int>> ans(k,vector<int>(k,0));

        for(int i=0; i<k; ++i)
            ans[i][pos[row[i]]] = row[i] + 1;
    
        return ans;
    }
};
```



### [B86-T3 被列覆盖的最多行数](https://leetcode.cn/problems/maximum-rows-covered-by-columns/)

#### 题意分析

------

选择其中`col`列，检测是否每一行中所有的`1`都被选中了

数据量小，直接回溯即可

#### 参考代码

-----

```cpp
class Solution {
    int ans = 0;

    void check(vector<vector<>int> &mat, unordered_set<int> &set) {
        int cnt = 0;
        for (int i = 0; i < mat.size(); ++i) {
            int j;
            for (j = 0; j < mat[0].size(); ++j) {
                if (mat[i][j] == 1) {
                    if (!set.count(j))break;
                }
            }
            if (j == mat[0].size())++cnt;
        }
        ans = max(ans, cnt);
    }

public:
    void dfs(vector<vector<int>> &mat, int cols, unordered_set<int> set, int idx) {
        if (idx == mat[0].size()&&cols!=0)return;
        if (cols == 0) {
            check(mat, set);
            return;
        }
        for (int i = idx; i < mat[0].size(); ++i) {
            set.insert(i);
            dfs(mat, cols - 1, set, i + 1);
            set.erase(i);
        }
    }

    int maximumRows(vector<vector<int>> &mat, int cols) {
        unordered_set<int> set;
        dfs(mat, cols, set, 0);
        return ans;
    }
};
```



### [B86-T4 预算内的最多机器人数目](https://leetcode.cn/problems/maximum-number-of-robots-within-budget/)

#### 题意分析

-----

运行 `k` 个机器人 **总开销** 是 `max(chargeTimes) + k * sum(runningCosts)`

注意到需要取所有机器人中的最大充电时间，我们可以使用单调队列来动态维护区间最大值，双指针进行区间的选择

#### 参考代码

------

```cpp
class Solution {
public:
    int maximumRobots(vector<int>& chargeTimes, vector<int>& runningCosts, long long budget) {
        int ans=0,left=0,right=0;
        deque<int> deque;
        long long sum=0ll;
        while(right<chargeTimes.size()){
            while(!deque.empty()&&chargeTimes[right]>=chargeTimes[deque.back()])
                deque.pop_back();
            deque.push_back(right);
            sum += runningCosts[right];
            while(!deque.empty()&&chargeTimes[deque.front()]+(right-left+1)*sum>budget){
                if(deque.front()==left)deque.pop_front();
                sum-=runningCosts[left],left++;
            }
            ans=max(ans,right-left+1),++right;
        }
        return ans;
    }
};
```



### [W309-T3 最长优雅子数组](https://leetcode.cn/problems/longest-nice-subarray/)

#### 题意分析

----

一个数组中各个元素相与之和均为0，则这个数组为一个优雅子数组；返回长度最长的优雅子数组

模拟位运算+滑动窗口

根据位运算知识易知：

+ 两个数字a与b的二进制串中，只有每一个位置上只有其中一个数字可以出现1时，两数相与的结果才为0
+ 推广到多个数字，即为任意数字在32位长的二进制串上，只有一个数字可以占据其中一个二进制位上的1

`Java`提供了使得数字转换为二进制数的方法，利用其进行模拟

#### 参考代码

-----

```Java
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

class Solution {
    boolean check(List<String> list, String s, int[] cnt) {
        int n = s.length();
        boolean f = true;
        for (int i = n - 1, j = 0; i >= 0; --i, ++j) {
            if (s.charAt(i) == '1' && cnt[j] == 1) {
                f = false;
                break;
            }
        }
        return f;
    }

    public int longestNiceSubarray(int[] nums) {
        int[] cnt = new int[32];
        List<String> list = new ArrayList<>();
        int maxLen = 0;
        for (int n : nums) {
            list.add(Integer.toBinaryString(n));
        }

        int ans = 1;
        int l = 0, r = 0;
        while (r < nums.length) {
            String s = list.get(r);
            if (check(list,s,cnt)){
                ans= Math.max(ans, r-l+1);
                ++r;
                for (int i = s.length() - 1, j = 0; i >= 0; --i, ++j) {
                    if (s.charAt(i)=='1')cnt[j]=1;
                }
            } else {
                while (!check(list, s,cnt)){
                    String q=list.get(l);
                    for (int i = q.length() - 1, j = 0; i >= 0; --i, ++j) {
                        if (q.charAt(i)=='1')cnt[j]=0;
                    }
                    ++l;
                }
            }
        }
        return ans;
    }
}
```

### [W310-T3 将区间分为最少组数](https://leetcode.cn/problems/divide-intervals-into-minimum-number-of-groups/)

#### 题意分析

----

给定一些区间段，按照一定的顺序将其分为`n`组，使得在`n`组中，每一个区间都不相交，返回`n`的最小数目

由于两区间段是否相交只取决于前一个区间的`end`和后一个区间的`start`

-   `start` <= `end`则两个区间会相交

-   例如`[1,4]`和`[3,5]`

则可以维护一个区间`end`值的优先队列，每次一添加新的区间时，只需要贪心地选择最小的那个`end`值进行判断

-   若`start` > `end`则可以加入该分组中，并更新该分组的末尾

-   若`start` <= `end`则不可加入任何分组(这一点接下来会有详细的分析)，直接重新开一个分组，并将`start`对应的`end`加入其中

由于要求区间不可相交，则需要从`start`小的区间开始选取；因此需要对数组进行一个**排序**

#### 参考代码

----

```C++
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define all(a) a.begin(),a.end()
#define rall(a) rbegin(a), rend(a)

using vi = vector<int>;

class Solution {
public:
    int minGroups(vector<vector<int>>& intervals) {
        sort(all(intervals));//排序，满足从小到大选择区间
        
        priority_queue<int, vi, greater<>> pq;
        
        for (vi a:intervals){
            int start=a[0],end=a[1];//当前请求加入分组的区间的起始值
            if (pq.empty())pq.push(end);//空分组时
            else{
                if (start <= pq.top()){//不可加入已有的分组，直接以end新建分组
                    pq.push(end);
                }else{//可加入已有分组，加入其中并更新区间尾数end
                    pq.pop();
                    pq.push(end);
                }
            }
        }
        //答案即为优先队列大小
        return pq.size();
    }
};
```

### [B87 - T3 按位或最大的最小子数组长度](https://leetcode.cn/problems/smallest-subarrays-with-maximum-bitwise-or/)

#### 题意分析

----

给定一个数组`nums`，对于其中的每一位`i`，找到包含这一位的一个子数组`[i,len(nums)]`，使得该子数组中所有数或运算结果最大

枚举 + 二分

对于每一个数，结果最大则需要每一个二进制位上均为1，因此使用哈希表统计各个位上，存在1的下标，使用二分查找确定最远位置即可

#### 参考代码

-----

```cpp
string Binary(int x) {
    string s = "";
    while (x) {
        if (x % 2 == 0) s = '0' + s;
        else s = '1' + s;
        x /= 2;
    }
    return s.size()==0 ? "0" : s;
}

using vi=vector<int>;
using vs=vector<string>;

class Solution {
public:
    vector<int> smallestSubarrays(vector<int>& nums) {
        int n=nums.size(),maxx=0;

        vs s(n);
        vi ans(n);
        map<int, set<int>> map;

        for (int i = 0; i < n; ++i) {
            s[i]= Binary(nums[i]);
            maxx=max(maxx,(int)s[i].size());
        }

        for (int i = 0; i < n; ++i) {
            string b=s[i];
            int j=b.size()-1;
            while (j>=0){
                if (b[j]=='1'){
                    map[b.size()-j].insert(i);
                }
                --j;
            }
        }

        for (int i = 0; i < n; ++i) {
            string target=s[i];
            int last=i;
            int j=maxx-1;
            while (target.size() < maxx)target.insert(target.begin(), '0');
            while (j>=0){
                if (target[j]=='0'){
                    auto it=map[maxx-j].lower_bound(i);
                    if(it!=map[maxx-j].end())last=max(last, *it);
                }
                --j;
            }
            ans[i]=last-i+1;
        }
        return ans;
    }
};
```

### [W311 - T4 字符串的前缀分数和](https://leetcode.cn/problems/sum-of-prefix-scores-of-strings/)

#### 题意分析

----

找到每一个字符的前缀在全部数组中的数量

字典树的每个节点记录以该节点为前缀的字符串有几个，计算答案的时候只需要将字符串所有前缀节点的值加起来即可。复杂度 $$\mathcal{O}(n|s|)$$，其中 |s| 是字符串长度

#### 参考代码

----

```cpp
class Solution {
    int nCnt;
    vector<vector<int>> ch;
    vector<int> f;

    int newNode() {
        ch.push_back(vector<int>(26, -1));
        f.push_back(0);
        return nCnt++;
    }

    void add(string &s) {
        int now = 0;
        for (int i = 0; i < s.size(); i++) {
            f[now]++;
            int c = s[i] - 'a';
            if (ch[now][c] == -1) ch[now][c] = newNode();
            now = ch[now][c];
        }
        f[now]++;
    }

    int query(string &s) {
        int now = 0, ret = 0;
        for (int i = 0; i < s.size(); i++) {
            if (now > 0) ret += f[now];
            int c = s[i] - 'a';
            now = ch[now][c];
        }
        ret += f[now];
        return ret;
    }

public:
    vector<int> sumPrefixScores(vector<string>& words) {
        newNode(); 
        for (string &s : words) add(s);
        vector<int> ans;
        for (string &s : words) ans.push_back(query(s));
        return ans;
    }
};
```

### [W314 - T4 矩阵中和能被 K 整除的路径](https://leetcode.cn/problems/paths-in-matrix-whose-sum-is-divisible-by-k/)

#### 题意分析

-----

从**(0,0)**出发前往**(m - 1, n - 1)**，返回路径上和可以整除`k`的路径数目

动态规划：

+ 定义状态为$$f[i][j][l]$$表示以**(i,j)**为路径结尾时，整除`k`余数`l`的路径数目

+ 状态转移：

  + 由格子上方转移而来：

    ```
    f[i][j][l] = (f[i][j][l] + f[i - 1][j][(l - grid[i][j] % k + k) % k]) % mod;
    ```

  + 由格子左边转移而来

    ```
    f[i][j][l] = (f[i][j][l] + f[i][j - 1][(l - grid[i][j] % k + k) % k]) % mod;
    ```

+ 状态初始化：

  + `f[0][0][grid[0][0] % k] = 1`

#### 参考代码

----

```cpp
class Solution {
    typedef long long ll;
    const int mod = 1e9 + 7;
public:
    int numberOfPaths(vector<vector<int>> &grid, int k) {
        int m = grid.size(), n = grid[0].size();

        ll f[m][n][k];
        memset(f, 0, sizeof(f));

        f[0][0][grid[0][0] % k] = 1;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                for (int l = 0; l < k; l++) {
                    if (i > 0)
                        f[i][j][l] = (f[i][j][l] + f[i - 1][j][(l - grid[i][j] % k + k) % k]) % mod;
                    if (j > 0) 
                        f[i][j][l] = (f[i][j][l] + f[i][j - 1][(l - grid[i][j] % k + k) % k]) % mod;
                }
            }
        }
        return (int) (f[m - 1][n - 1][0] % mod);
    }
};
```

### [B90 - T3 摧毁一系列目标](https://leetcode.cn/problems/destroy-sequential-targets/)

#### 题意分析

-----

在水平的位置上有一些目标需要击毁，你可以击毁的目标为
$$
nums[i] + c * space
$$
返回可以击毁的最大数目

“表达式中有`space`，那我去掉`space`会怎么样？”

两边同时整除`space`得到（`MarkDown`中`|`为整除）
$$
nums[i] \mid space + 0
$$
那么问题一下子就简洁了，直接计数即可出结果

#### 参考代码

------

```cpp
class Solution {
public:
    int destroyTargets(vector<int>& nums, int space) {
        std::unordered_map<long long, int> map;
        for(int v:nums) map[v % space]++;
        int tot = 0, ans = INT_MAX;
        
        for (int i: nums) {
            int have = map[i % space];
            if (tot == have) {
                ans = min(ans, i);
            } else if (tot < have) {
                tot = have;
                ans = i;
            }
        }
        return ans;
    }
};
```

### [W317 - T3 美丽整数的最小增量](https://leetcode.cn/contest/weekly-contest-317/problems/minimum-addition-to-make-integer-beautiful/)

#### 题意分析

---------

给你两个正整数 `n` 和 `target` 。

如果某个整数每一位上的数字相加小于或等于 `target` ，则认为这个整数是一个 **美丽整数** 。

目标是找到最小的`X`则可以使用贪心法

由于是要让整个数字之和变小，则我们直接让每一个数字都变0，从最小开始即可

#### 参考代码

---------

```cpp
typedef long long ll;

class Solution {
    bool check(ll n, int target) {
        int to = 0;
        while (n > 0){
            to += n % 10;
            n /= 10;
        }
        return to <= target;
    }
public:
    long long makeIntegerBeautiful(long long n, int target) {
        if (check(n,target)) return 0;
        ll t = n;
        ll ans = 0, p = 1;
        while (!check(n, target)) {
            ll num = t % 10;
            t /= 10;

            if (num != 0){
                n += ((10 - num) * p);
                ans += p * (10 - num);
            }
            p *= 10;
            t = n / p;
        }
        return ans;
    }
};
```

