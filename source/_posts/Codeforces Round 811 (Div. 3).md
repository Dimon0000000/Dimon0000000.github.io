---
title: Codeforces Round 811 (Div. 3)
date: 2022-07-30 18:45:40
tags:
- CodeForces Contest
- 脑筋急转弯
- 哈希表
- 贪心
- 数学
categories: CodeForces
index_img: https://s2.loli.net/2022/10/24/1eFKxhHICNPDwJ8.jpg
---

> My Submission
>
> > Problem AC: 4
> >
> > Problem Try: 5
> >
> > Wrong Answer: 1

### Problem A：[Everyone Loves to Sleep](https://codeforces.com/contest/1714/problem/A)

#### 题意分析

-----

给出Vlad的入睡时间和他的所有闹钟时间，返回他可以睡着的时长

模拟：

+ 将所有小时转换为分钟数，找到第一个大于入睡时间的闹钟，若找不到，则使用第二天的第一个闹钟

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define loop(t) while (t-- > 0)

#define put(a) for (auto &_x: a) cin >> _x;


using vi = vector<int>;
using vvi = vector<vi>;


int main() {
    fastIO();
    int t;
    cin >> t;

    loop(t) {
        int n, H ,M;
        cin >> n >> H >> M;

        int h, m;

        vvi time(n, vi(2));
        for (int i = 0; i < n; ++i) {
            put(time[i]);
        }
        vi minute(n);
        for (int i = 0; i < n; ++i) {
            minute[i] = time[i][0] * 60 + time[i][1];
        }
        sort(all(minute));
        int i = 0, now = H * 60 + M;
        for (; i < n; ++i) {
            if (minute[i] >= now) break;
        }
        int diff;
        if (i == n) {
            diff = (24 * 60) - now + minute[0];
        } else {
            diff = minute[i] - now;
        }
        cout << diff / 60 << ' ' << diff % 60 << endl;
    }
}
```



### Problem B：[ Remove Prefix](https://codeforces.com/contest/1714/problem/B)

#### 题意分析

-----

从前向后删除元素，直到不存在重复元素为止

可以从后向前使用哈希表统计，直到出现重复元素为止

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define loop(t) while (t-- > 0)
#define us unordered_set

using vi = vector<int>;

int main() {
    fastIO();
    int t;
    cin >> t;

    loop(t) {
        int n;
        cin >> n;
        vi a(n);
        put(a);

        us<int> set;
        int i;
        for (i = a.size() - 1; i >= 0 ; --i) {
            if (set.count(a[i])) break;
            set.insert(a[i]);
        }
        i++;
        cout << a.size() - (a.size() - i) << endl;
    }
}
```



### Problem C：[Minimum Varied Number](https://codeforces.com/contest/1714/problem/C)

#### 题意分析

-----

给定`n`个查询，对于查询的每一个数，返回它的最短数之和(每个数字只可以用一次)

贪心，从9开始统计，每次减少1

若查询的数`s`大于当前数，则减去，直到`s`为0

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define loop(t) while (t-- > 0)
#define all(a) a.begin(),a.end()

int main() {
    fastIO();
    int t, s;
    cin >> t;

    loop(t) {
        cin >> s;
        string a;
        int used = 9;
        while (s > 0) {
            if (s >= used) a += (char) (used + '0'), s -= used;
            used--;
        }
        reverse(all(a));
        cout<<a << endl;
    }
}
```

### Problem E：[Add Modulo 10](https://codeforces.com/contest/1714/problem/E)

#### 题意分析

-----

数学：

对于任意一个数，会发现按题目要求加下去一定会出现循环

例：

> 22 → 24 → 28 → 36 → 42
>
> 此时数字的个位出现了循环
>
> 5 → 10 → 10
>
> 此时数字的个位出现了循环
>
> 18 → 26 → 32 →  34 → 38
>
> 此时数字的个位出现了循环

基于此，可以对每一个数字的变化进行循环，直到出现个位重复的情况时，检查二者余20的模是否相同

例：

> Ⅰ: 18
>
> Ⅱ : 22 → 24 → 28
>
> Ⅰ余20的模为18，Ⅱ余20的模为8；无论再如何操作，二者个位相同时，差值总是为10，此时无法使得数组全部数字相同

根据以上思路遍历每一个数字即可

需要注意的是，当数组中出现5或10，整个数组就只能为`I * 10`

#### 参考代码

----

```cpp
#include "bits/stdc++.h"
#include "iomanip"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define sz(a) a.size()
#define judge(x) if (x) cout << "Yes" << endl; else cout << "No" << endl
#define loop(t) while (t-- > 0)
#define enhanceFor(t) for (auto it: t)

using vi = vector<int>;

int main() {
    fastIO();
    int t;
    cin >> t;
    loop(t) {
        int n;
        cin >> n;
        vi a(n),b;
        us <int> set;
        for (int i = 0; i < n; ++i) {
            cin >> a[i];
            if (a[i] % 10 == 5) b.pb(a[i] + 5);
            else if (a[i] % 10 == 0) b.pb(a[i]);
            else {
                int v = a[i];
                while (v % 10 != 8) {
                    v += v % 10;
                }
                set.insert(v % 20);
            }
        }
        if (sz(b)) {
            bool flag = sz(b) == n;
            for (int v: b) if (v != b[0]) flag = 0;
            judge(flag);
        } else {
            judge(set.size() == 1);
        }
    }
}
```

