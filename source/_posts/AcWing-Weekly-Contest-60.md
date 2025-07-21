---
title: AcWing Weekly Contest 60
date: 2022-07-16 15:33:59
tags:
- AcWing Contest
- 懒标记法
- 动态规划
categories: AcWing
index_img: https://smms.app/image/EtHRL4bfkqzngG8
---

> MY SUBMISSION
>
> > Problem AC: 3
> >
> > Problem Try: 3
> >
> > Wrong Answer: 0

### Problem A：[吃饭](https://www.acwing.com/problem/content/description/4497/)

#### 题意分析

----

签到题

#### 参考代码

----

```cpp
#include "bits/stdc++.h"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define ph push_heap
#define PF(x) = ((x) * (x))
#define LF(x) = ((x) * PF(x))
#define fori(a, b) for (int i = a; i < b; i++)
#define forj(a, b) for (int j = a; j < b; j++)
#define loop(t) while (t-- > 0)
#define put(a) for (auto &_x: a) cin >> _x;
#define RALL(a) rbegin(a), rend(a)

typedef long long ll;
typedef unsigned long long ull;
typedef long double ld;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef unsigned int uint;
typedef pair<int, char> pic;

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

int main() {
    fastIO();
    int n, m, k;
    cin >> n >> m >> k;
    if (n <= m && n <= k) cout << "Yes" << endl;
    else cout << "No" << endl;
}
```

### Problem B：[数组操作](https://www.acwing.com/problem/content/description/4498/)

#### 题意分析

-----

每次找到数组中的最小值，若不为0，则整个数组减去，否则整个输出0

由于数组长度为$$10^5$$，模拟操作会超时

排序 + 懒标记：

这里使用线段树中`lazy`标记的思路，来对整个数组进行**另类模拟**；`lazy`标记代表当前累计减少的值

+ 数组排序
+ 遍历数组，获得减去`lazy`标记后不为0的最小值
+ 输出最小值，并在`lazy`标记上累加
+ 直到只剩0为止

#### 参考代码

-----

```cpp
#include "bits/stdc++.h"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define ph push_heap
#define PF(x) = ((x) * (x))
#define LF(x) = ((x) * PF(x))
#define fori(a, b) for (int i = a; i < b; i++)
#define forj(a, b) for (int j = a; j < b; j++)
#define loop(t) while (t-- > 0)
#define put(a) for (auto &_x: a) cin >> _x;
#define RALL(a) rbegin(a), rend(a)

typedef long long ll;
typedef unsigned long long ull;
typedef long double ld;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef unsigned int uint;
typedef pair<int, char> pic;

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

int main() {
    fastIO();
    int n, k;
    cin >> n >> k;

    vector<int> a(n);
    put(a);
    sort(a.begin(), a.end());


    int lazy = 0, idx = 0;
    while (k > 0) {
        int minn;
        while (idx < n && a[idx] - lazy == 0) {
            idx++;
        }
        if (idx == n) break;
        minn = a[idx];

        cout << minn - lazy << endl;
        lazy += minn - lazy;
        k--;
    }
    loop(k) cout << '0' << endl;
}
```

### Problem C：[吃水果](https://www.acwing.com/problem/content/description/4499/)

#### 题意分析

-----

动态规划：

+ `dp[i][j]`表示第`i`个小朋友前有`j`个满足条件的
+ `dp[i][j]`可由两个状态推导而来
  + `dp[i - 1][j]`为第`i - 1`个小朋友时拥有的符合条件的组合方案数，此时第`i`个小朋友不满足条件
  + `dp[i][j] + dp[i - 1][j - 1] * (m - 1)`为该小朋友满足条件时的组合方案数
+ 结果返回`dp[n][k]`

#### 参考代码

-----

```cpp
#include "bits/stdc++.h"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define ph push_heap
#define PF(x) = ((x) * (x))
#define LF(x) = ((x) * PF(x))
#define fori(a, b) for (int i = a; i < b; i++)
#define forj(a, b) for (int j = a; j < b; j++)
#define loop(t) while (t-- > 0)
#define put(a) for (auto &_x: a) cin >> _x;
#define RALL(a) rbegin(a), rend(a)

typedef long long ll;
typedef unsigned long long ull;
typedef long double ld;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef unsigned int uint;
typedef pair<int, char> pic;

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

int mods = 998244353;

int main() {
    fastIO();
    ll n, m, k;
    cin >> n >> m >> k;

    vector<vector<ll>> dp(n + 1, vector<ll>(n + 1, 0));
    //dp[i][j]表示第i个小朋友前有j个满足条件的
    dp[1][0] = m;

    fori(2, n + 1) {
        forj(0, i) {
            dp[i][j] = dp[i - 1][j] % mods;
            if (j >= 1) dp[i][j] = (dp[i][j] + dp[i - 1][j - 1] * (m - 1)) % mods;
        }
    }
    cout << dp[n][k] << endl;
}
```

