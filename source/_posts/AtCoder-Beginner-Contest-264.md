---
title: freee Programming Contest 2022 (AtCoder Beginner Contest 264)
date: 2022-08-18 18:33:21
tags:
- AtCoder Contest
categories: AtCoder
index_img: https://s2.loli.net/2022/10/24/7QzSwKjCig1BaM9.jpg
---

> My SubMisson
>
> > Number of Problem: 7
> >
> > Problem AC: 4
> >
> > Wrong Answer: 2

### [C - Matrix Reducing](https://atcoder.jp/contests/abc264/tasks/abc264_c)

#### 题意分析

----

给定一个源矩阵和目标矩阵，返回任意次操作后，是否可以得到目标矩阵

操作定义为：

+ 选择矩阵的某一行并删除该行
+ 选择矩阵的某一列并删除该列

模式匹配 + 模拟：

由于较小的数据量，当某一行符合目标矩阵时，按照改行保留的列数，遍历剩余的行数

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define judge(x) if (x) cout << "Yes" << endl; else cout << "No" << endl
#define loop(t) while (t-- > 0)
#define enhanceFor(t) for (auto it: t)
#define all(a) a.begin(),a.end()
#define rall(a) rbegin(a), rend(a)
#define put(a) for (auto &_x: a) cin >> _x;

using vl = vector<ll>;
using vvl = vector<vl>;

static bool check(vl a, vl b) {
    int p = 0;
    for (int i = 0; i < a.size() && p < b.size(); ++i) {
        if (a[i] == b[p])++p;
    }
    return p == b.size();
}

bool checks(vl a, vl b){
    for (int i = 0; i < a.size(); ++i) {
        if (a[i]!=b[i])return 0;
    }
    return 1;
}

static bool check(vvl a, vvl b, int start, set<int> set) {
    int p = 1;
    for (int i = start; i < a.size() && p < b.size(); ++i) {
        vl aa=a[i];
        vl aaa;
        for (int v:set){
            aaa.pb(aa[v]);
        }
        if (checks(aaa,b[p]))++p;
    }
    return p == b.size();
}

bool flag = 0;

bool confirm(vl a, vl b, set<int> &set) {
    int p = 0;
    for (int i = 0; i < a.size() && p < b.size(); ++i) {
        if (a[i] == b[p]) set.insert(i), ++p;
    }
    return p == b.size();
}

int main() {
    fastIO();
    int h1, w1, h2, w2;
    cin >> h1 >> w1;
    vvl a(h1, vl(w1));

    for (int i = 0; i < h1; ++i) {
        put(a[i]);
    }
    cin >> h2 >> w2;
    vvl b(h2, vl(w2));
    for (int i = 0; i < h2; ++i) {
        put(b[i]);
    }
    vl bb = b[0];
    for (int i = 0; i < h1 && !flag; ++i) {
        vl aa = a[i];
        set<int> set;
        if (confirm(aa, bb, set)) {
            if (check(a, b, i + 1, set)) flag = 1;
        }
    }
    judge(flag);
}
```

### [D - "redocta".swap(i,i+1) (atcoder.jp)](https://atcoder.jp/contests/abc264/tasks/abc264_d)

#### 题意分析

----

形成目标字符串`atcoder`

贪心：

+ 按照目标字符串从前到后，每次选择目标字符
+ 每次从原串中选择最先出现的匹配字符移动到既定位置，计数

#### 参考代码

-----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)

static void swap(string &s, int i, int j, int &ans){
    while (i !=j) {
        char d=s[i-1];
        s[i-1]=s[i];
        s[i]=d;
        --i;
        ++ans;
    }
}

int findI(string s, char c){
    for (int i = 0; i < s.size(); ++i) {
        if (s[i]==c)return i;
    }
    return -1;
}

int main() {
    fastIO();
    string s;
    cin>>s;
    string t="atcoder";

    int ans=0;

    for (int i = 0; i < t.size(); ++i) {
        if (s[i]==t[i]) continue;
        char c=t[i];
        int j= findI(s,c);
        swap(s, j,i,ans);
    }
    cout << ans<<"\n";
}
```

### [E - Blackout 2 (atcoder.jp)](https://atcoder.jp/contests/abc264/tasks/abc264_e)

#### 题意分析

----



#### 参考代码

-----
