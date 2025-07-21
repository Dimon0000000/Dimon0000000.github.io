---
title: AtCoder Beginner Contest Album (272 - 282)
date: 2022-10-09 16:22:57
tags:
- AtCoder
- 广度优先搜索
- 贪心
ccategories: AtCoder
index_img: https://s2.loli.net/2022/10/24/iwW1qY9VFsQdvEp.jpg
---

> 前言:
>
> 笔者很菜，这里只总结ABC的D，E两题

### [D - Root M Leaper](https://atcoder.jp/contests/abc272/tasks/abc272_d)

#### 题意分析

----

最初你的棋子在(1,1)格，给定一个n*n大小的棋盘，返回一个数字棋盘，上面的数字时到达该位置的最小跳跃次数;

你每次只能跳跃到距离所在格子距离为$$\sqrt m$$的格子上

广度优先搜索，有趣的点在于根据`M`快速定位出每次`i`, `j`移动的范围

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define judge(x) if (x) cout << "Yes" << endl; else cout << "No" << endl
#define printlist(l, n) cout << "[ "; for(int (i)=0; (i)<(n); ++(i)) {cout << (l)[i] << " "; } cout << "]" << endl;
#define loop(t) while (t-- > 0)
#define all(a) a.begin(),a.end()
#define rall(a) rbegin(a), rend(a)
#define put(a) for (auto &_x: a) cin >> _x;

typedef long long ll;
typedef pair<int, int> pii;

const int N = 2e5 + 5;
const double eps = 1e-6;
constexpr int mod = 1e9 + 7;
constexpr int i32 = 0x3f3f3f3f;

using vi = vector<int>;
using vvi = vector<vi>;

int main() {
    fastIO();
    int n, m;
    cin >> n >> m;

    std::vector<std::pair<int, int>> shifts;
    for (int i = -n; i < n; i++) {
        for (int j = -n; j < n; j++) {
            if (i * i + j * j == m) {
                shifts.emplace_back(i, j);
            }
        }
    }

    std::vector<std::vector<int>> d(n, vector<int>(n, -1));
    d[0][0] = 0;
    std::vector<std::pair<int, int>> que;
    que.emplace_back(0, 0);
    for (int b = 0; b < (int) que.size(); b++) {
        for (auto &s: shifts) {
            int ni = que[b].first + s.first;
            int nj = que[b].second + s.second;
            if (ni >= 0 && nj >= 0 && ni < n && nj < n && d[ni][nj] == -1) {
                que.emplace_back(ni, nj);
                d[ni][nj] = d[que[b].first][que[b].second] + 1;
            }
        }
    }
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cout << d[i][j] << " \n"[j == n - 1];
        }
    }
    return 0;
}
```

### [E - Add and Mex](https://atcoder.jp/contests/abc272/tasks/abc272_e)

#### 题意分析

-----

给定一个数组，在每一轮操作时，对应位置为`i`的数字要加`i`

返回在每一次操作后，数组中缺少的最小非负整数

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define judge(x) if (x) cout << "Yes" << endl; else cout << "No" << endl
#define printlist(l, n) cout << "[ "; for(int (i)=0; (i)<(n); ++(i)) {cout << (l)[i] << " "; } cout << "]" << endl;
#define loop(t) while (t-- > 0)
#define all(a) a.begin(),a.end()
#define rall(a) rbegin(a), rend(a)
#define put(a) for (auto &_x: a) cin >> _x;

typedef long long ll;
typedef pair<int, int> pii;

const int N = 2e5 + 5;
const double eps = 1e-6;
constexpr int mod = 1e9 + 7;
constexpr int i32 = 0x3f3f3f3f;

using vi = std::vector<int>;
using vvi = std::vector<vi>;

int main() {
    fastIO();
    int n, m;
    cin >> n >> m;
    std::vector<int> a(n);
    put(a);

    std::vector<std::vector<int>> at(m);
    for (int i = 0; i < n; i++) {
        int k = 0;
        if (a[i] < 0) {
            k = max(k, (-a[i] - 1) / (i + 1));
        }
        while (k < m && a[i] + (i + 1) * (k + 1) < n) {
            at[k].push_back(a[i] + (i + 1) * (k + 1));
            k += 1;
        }
    }
    for (int i = 0; i < m; i++) {
        sort(at[i].begin(), at[i].end());
        int res = 0;
        for (int x : at[i]) {
            if (x == res) {
                res += 1;
            }
        }
        cout << res << "\n";
    }
    return 0;
}
```

### [D - Yet Another Recursive Function](https://atcoder.jp/contests/abc275/tasks/abc275_d)

#### 题意分析

-------

给定函数
$$
f(0) = 1 \\
f(k) = f(⌊\frac{k}{2}⌋)+f(⌊\frac{k}{3}⌋)
$$
返回f(n)

由于每次的计算中会产生很多重复计算，因此可以使用记忆化 + 剪枝来进行优化

#### 参考代码

---------

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)

typedef long long ll;

ll tot = 0;

unordered_map<long double, ll> mem;

ll f(ll k) {
    ll cnt = 0, c = 0, d = 0;
    long double a = k / 2, b = k / 3;
    if (mem.count(a)) {
        tot += mem[a];
        c = mem[a];
    } else if (a < 0.5) {
        c = f(0);
        tot += c;
    } else c = f((ll) a);

    mem[a] = c;

    if (mem.count(b)) {
        tot += mem[b];
        d = mem[b];
    } else if (b < 0.5) {
        d = f(0);
        tot += d;
    } else d = f((ll) b);

    mem[b] = d;

    return c + d;
}

int main() {
    fastIO();
    mem[0] = 1;
    ll n;
    cin >> n;
    if(n ==0) {
        cout << 1;
        return 0;
    }
    f(n);
    cout << tot;
}
```

