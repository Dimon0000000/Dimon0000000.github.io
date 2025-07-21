---
title: 中国银联专场竞赛🏦
date: 2022-09-16 18:22:16
tags: 
- LeetCode Contest
- 模拟
- 双指针
- 二分查找
- 链表
categories: LeetCode
index_img: https://s2.loli.net/2022/10/24/qSx5oJQOdF4APUH.jpg
---

## 专场信息

比赛链接：[中国银联专场竞赛（2023届校园招聘专场） - 力扣（LeetCode）](https://leetcode.cn/contest/cnunionpay2022/)

排名：

<a href="https://imgloc.com/i/cdsCE"><img src="https://s1.328888.xyz/2022/09/16/cdsCE.png" alt="cdsCE.png" border="0"></a>

## 题目解析(Q1, Q2, Q3)

### [银联-1. 重构链表](https://leetcode.cn/contest/cnunionpay2022/problems/VLNEbD/)

#### 题意分析

-----

给定一个链表，删除其中的所有偶数的节点

链表题大多数都可以用线性表水过去，笔者在此提供水版和正解版两种

正解：

+ 使用一个哨兵节点，选择性地将链表中的元素加入其中

#### 参考代码

-----

```cpp
using vi = vector<int>;
class Solution {		//水题版
public:
    ListNode* reContruct(ListNode* head) {
        vi a;
        while(head)a.push_back(head->val),head=head->next;
        auto* ans=new ListNode(-1),*p=ans;
        
        for(int v:a){
            if(v%2)p->next=new ListNode(v),p=p->next;
        }
        return ans->next;
    }
};
```
---------

```cpp
class Solution {		//正解
public:
    ListNode* reContruct(ListNode* head) {
        auto *dummy=new ListNode(-1),*p=dummy;
        while(head){
            if(head->val%2){
                p->next=new ListNode(head->val);
                p=p->next;
            }
            head=head->next;
        }
        return dummy->next;
    }
};
```

### [银联-2. 勘探补给](https://leetcode.cn/contest/cnunionpay2022/problems/6olJmJ/)

#### 题意分析

-----

给定工程队的位置和所有补给点的位置，返回工程队选择补给的位置下标

二分查找每一个工程队的补给位置即可(这里采用`map`的`lower_bound`方法)；

本题中使用了统计最大最小值的方法，来巧妙处理边界问题(实际上就是边界WA了好几次懒得改了)

#### 参考代码

----

```cpp
#define pb push_back

class Solution {
public:
    vector<int> explorationSupply(vector<int> &station, vector<int> &pos) {
        vector<int> ans;
        map<int, int> map;
        int maxx=*max_element(all(station)), minn=*min_element(all(station));
        for (int i = 0; i < station.size(); ++i) {
            map[station[i]] = i;
        }
        for (int p: pos) {
            if(p>=maxx){
                ans.push_back(station.size()-1);
                continue;
            }else if (p<minn){
                ans.push_back(0);
                continue;
            }
            
            auto it = map.lower_bound(p);
            if (it->first == p) {
                ans.push_back(it->second);
                continue;
            }
            if (it != map.begin())--it;
            
            if (it == map.end()) {
                --it;
                ans.pb(it->second);
            } else if (it == map.begin()) {
                int i1 = it->second, i2 = i32;
                ++it;
                if (it != map.end())i2 = it->second;
                if (abs(station[i1] - p) <= abs(station[i2] - p))ans.pb(i1);
                else ans.pb(i2);
            } else {
                int i1 = it->second, i2, i3 = i32;
                --it;
                i2 = it->second;
                ++it;
                ++it;
                if (it != map.end())i3 = it->second;

                int d1 = abs(p - station[i1]), d2 = abs(p - station[i2]), d3 = abs(p - station[i3]);
                if (d1 <= d2 && d1 <= d3)ans.pb(i1);
                else if (d2 <= d1 && d2 <= d3)ans.pb(i2);
                else ans.pb(i3);
            }
        }
        return ans;
    }
};
```

### [银联-3. 风能发电](https://leetcode.cn/contest/cnunionpay2022/problems/wMGN0t/)

#### 题意分析

----

给定容量为`storeLimit`的容器，在每一个使用区间内：

+ 若此时的供电量小于`minSupply`则从容器中去除电量(如果有富余)
+ 若此时的供电量大于`maxSupply`则向容器中加入电量(如果还有空间)

随时间变化，供电的最大最小需求也会不同，返回最终容器中剩余的电荷量

双指针模拟，一个指向供电，一个指向时间戳

#### 参考代码

----

```cpp
class Solution {
public:
    int StoredEnergy(int storeLimit, const vector<int>& power, const vector<vector<int>>& supply){
        int i=0,j=0,m=power.size(),n=supply.size(),time=0;
        int limit=0;
        while (i<m){
            if(time>=supply[min(j+1,n-1)][0])j=min(j+1,n-1);
            int p=power[i];

            if(p<=supply[j][1])limit=max(limit-(supply[j][1]-p),0);
            else if(p>= supply[j][2])limit=min(storeLimit, limit+(p-supply[j][2]));
            ++i;
            ++time;
        }
        return limit;
    }
};
```

