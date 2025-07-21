---
title: CodeForces Global Round 20
date: 2022-06-23 14:03:18
tags:
- CodeForces Contest
- 模拟
- 贪心
categories: CodeForces
index_img: https://smms.app/image/1xVl7XzoFHGeIJj
---
> MY SUBMISSONS:
> > Problems AC: 4
> > Problems Try: 6
> > Wrong Answer: 4

### Problem A：[Log Chopping](https://codeforces.com/contest/1672/problem/A)

#### 题意分析

-----

两人做游戏，一次性把一块大于等于2的木板劈成两块，直到无法再劈的人为失败

无论怎么样劈，一块长度为**n**的木板可劈**n - 1**次

由于`errorgorn`先起手，则可劈次数为**奇数**为`errorgorn`胜利，否则`maomao90`胜利

#### 参考代码

-----

```cpp
#include "bits/stdc++.h"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define PF(x) = ((x) * (x))
#define LF(x) = ((x) * PF(x))

typedef long long ll;
typedef unsigned long long ull;
typedef long double ld;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef unordered_map<int, int> umap;
typedef unsigned int uint;

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;


int t, n;

int main() {
    fastIO();
    cin >> t;
    while (t-- > 0) {
        cin >> n;
        vector<int> nums(n);

        for (auto &x : nums) cin >> x;

        int ans = 0;
        for (int val : nums) {
            ans += val - 1;
        }
        if (ans % 2 == 1) {
            cout << "errorgorn" << endl;
        } else {
            cout << "maomao90" << endl;
        }
    }
}
```

### Problem B：[I love AAAB](https://codeforces.com/contest/1672/problem/B)

#### 题意分析

-----

给你一个空字符串，使用**好字符串**来构成目标字符串

> Let's call a string **good** if its length is at least **2** and all of its characters are **A** except for the last character which is **B**. The good strings are **AB**,**AAB**,**AAAB**,… Note that **B** is **not** a good string.

维护A和B的数量；当遇到B时，若此时前方的A的数量小于B则无法构成

否则从A中减去B的贡献值，同时将B清零

#### 参考代码

-----

```cpp
#include "bits/stdc++.h"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define PF(x) = ((x) * (x))
#define LF(x) = ((x) * PF(x))

typedef long long ll;
typedef unsigned long long ull;
typedef long double ld;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef unordered_map<int, int> umap;
typedef unsigned int uint;

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

//AABBABAAAABABABAABAB
int t, n;

int main() {
    fastIO();
    cin >> t;
    while (t-- > 0) {
        bool flag = true;
        string s2;
        cin >> s2;
        if (s2.size() < 2 || s2[s2.size() - 1] != 'B' || s2[0] == 'B') flag = false;
        else {
            int p = 0, A = 0, B = 0;
            while (p < s2.size()) {
                while (p < s2.size() && s2[p] != 'B') {
                    A++;
                    p++;
                }
                while (p < s2.size() && s2[p] != 'A') {
                    B++;
                    p++;
                }
                if (A < B) {
                    flag = false;
                    break;
                };
                A -= B;
                B = 0;
            }
        }
        if (!flag) {
            cout << "NO" << endl;
        } else {
            cout << "YES" << endl;
        }
    }
}
```

### Problem C：[Unequal Array](https://codeforces.com/contest/1672/problem/C)

#### 题意分析

-----

一次**操作**定义为：将数组中相邻的两个数**[i, i + 1]**更新为***x***；返回使数组中只存在最多一对相邻的数字的最小操作数

统计第一个相邻的位置和最后一个相邻的位置，返回两位置之间的**"数对"**的数量

> 这里数对定义为: 相邻的两个数
>
> 具体例证：
>
> [1,1,5,3,3,3,7,8] -> first = 0, last = 4
>
> [1,2,2,3,3,3,7,8] -> 修改[0, 1]为2
>
> [1,2,3,3,3,3,7,8] -> 修改[1, 2]为3
>
> [1,2,3,4,4,3,7,8] -> 修改[2, 3]为4
>
> [1,2,3,4,5,5,7,8] -> 修改[3, 4]为5
>
> 此时数组中只存在一对相邻且值相同的数对

#### 参考代码

-----

```cpp
#include "bits/stdc++.h"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define PF(x) = ((x) * (x))
#define LF(x) = ((x) * PF(x))

typedef long long ll;
typedef unsigned long long ull;
typedef long double ld;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef unordered_map<int, int> umap;
typedef unsigned int uint;

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

int t, n;

int main() {
    fastIO();
    cin >> t;
    while (t-- > 0) {
        cin >> n;
        vector<int> nums(n);
        for (auto &x : nums) cin >> x;

        int first = -1;
        int last = -1;
        for (int i = 0; i+1 < n; i++) {
            if (nums[i] == nums[i+1]) {
                if (first == -1) first = i;
                last = i;
            }
        }
        if (first == last) {
            cout << 0 << endl;
        } else {
            cout << max(1, last-first-1) << endl;
        }
    }
}
```

### Problem D：[Cyclic Rotation](https://codeforces.com/contest/1672/problem/D)

#### 题意分析

-----

给定一个源数组**a**和目标数组**b**，若在有限次操作内可以将**a**转换为**b**则输出***YES***，否则输出***No***

> You may perform the following operation any number of times:
>
> + Choose two indices ***l*** and ***r*** where 1 ≤ ***l*** < ***r*** ≤ ***n*** and a~l~=a~r~. Then, set a[l…r]=[al+1,al+2,…,a~r~,a~l~]

倒序遍历，一次性统计两数组中相同数字的个数；设某一次我们操作的数字为`val`

+ 若某一时刻`cnt_a`小于等于`cnt_b`，证明前方可能存在有可以移动到后方的`val`
+ 否则前方无法移动到后方，输出***NO***

#### 参考代码

-----

```cpp
#include "bits/stdc++.h"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define PF(x) = ((x) * (x))
#define LF(x) = ((x) * PF(x))

typedef long long ll;
typedef unsigned long long ull;
typedef long double ld;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef unordered_map<int, int> umap;
typedef unsigned int uint;

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

int t, n;

int main() {
    fastIO();
    cin >> t;
    while (t-- > 0) {
        cin >> n;
        vector<int> a(n), b(n);

        for (auto &x: a) cin >> x;
        for (auto &x: b) cin >> x;

        bool flag = true;

        map<int, int> cnt;
        int i, j;

        for (i = n - 1, j = n - 1; i >= 0;) {
            int val = a[i];
            int cnt_a = 0;
            while (i >= 0 && a[i] == val) i--, cnt_a++;
            while (j >= 0 && b[j] == val) j--, cnt[val]++;
            cnt[val] -= cnt_a;
            if (cnt[val] < 0) {
                flag = false;
                break;
            }
        }
        if (j > 0) flag = false;
        if (flag) {
            cout << "YES" << endl;
        } else {
            cout << "NO" << endl;
        }
    }
}
```

