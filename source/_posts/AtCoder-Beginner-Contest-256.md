---
title: Tokio Marine & Nichido Fire Insurance Programming Contest 2022（AtCoder Beginner Contest 256)
date: 2022-06-21 06:46:47
tags:
- Atcoder Contest
- 快速幂
- 模拟
- 暴力
- 线段树
categories: AtCoder
index_img: https://smms.app/image/aBLNA3vhMZIR4T9
---
> MY SUBMISSIONS:
> > Problems AC: 5
> > Problems Try: 6
> > Wrong Answer: 6

### Problem A：[A - 2^N](https://atcoder.jp/contests/abc256/tasks/abc256_a)

#### 题意分析

-----

给定一个整数`N`返回**2^N^**

数据量较小，直接调用**pow()**即可

注意要使用`long long`类型作为答案返回

#### 参考代码

-----

```cpp
#include "iostream"
#include "climits"
#include "math.h"
#include "unordered_map"

using namespace std;

#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
#define pb push_back
#define PF(x) = ((x) * (x))
#define LF(x) = ((x) * PF(x))

typedef long long ll;
typedef unsigned long long ull;
typedef long double dl;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef unordered_map<int, int> umap;

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

int main() {
    fastIO();
    int N;
    cin >> N;
    ll ans = pow(2, N);
    cout << ans << endl;
}
```



### Problem B：[B - Batters](https://atcoder.jp/contests/abc256/tasks/abc256_b)

#### 题意分析

给定四个棋盘，每次操作的开始现在0号棋盘放一个棋子

再将4个棋盘上所有的棋子向前移动A~i~步，统计被移除棋盘的棋子

我们创建一个统计数组，实时更新棋盘上棋子的情况即可

#### 参考代码

-----

```cpp
#include "iostream"
#include "climits"
#include "math.h"
#include "unordered_map"
#include "vector"
#include "deque"

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

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

int main() {
    fastIO();
    int N;
    cin >> N;
    vector<int> nums(N);

    for (auto &x: nums) cin >> x;

    int p = 0;

    vector<int> square(4);

    for (int i = 0; i < N; ++i) {
        square[0]++;
        int s1 = square[0], s2 = square[1], s3 = square[2], s4 = square[3];
        vector<int> member = {s1, s2, s3, s4};

        for (int j = 0; j < 4; ++j) {
            if (j + nums[i] >= 4) {
                p += member[j];
                square[j] -= member[j];
            } else {
                square[j + nums[i]] += member[j];
                square[j] -= member[j];
            }
        }
    }

    cout << p << endl;
}
```

### Problem C：[C - Filling 3x3 array](https://atcoder.jp/contests/abc256/tasks/abc256_c)

#### 题意分析

-----

给定一个3 * 3的棋盘，输入进每一行，每一列的和，求解一共能有多少字符合每一行和以及列和的情况

![sLvPt.png](https://s1.328888.xyz/2022/06/21/sLvPt.png)

对于该表格，我们枚举a, b, c, d四个位置的数字，利用行和以及列和求出其他位置的数字

若符合要求则方案数`ans + 1`

#### 参考代码

-----

```cpp
#include "iostream"
#include "climits"
#include "math.h"
#include "unordered_map"
#include "vector"
#include "deque"
#include "algorithm"

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

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

int main() {
    fastIO();
    int h1, h2, h3, w1, w2, w3;
    cin >> h1 >> h2 >> h3 >> w1 >> w2 >> w3;

    int ans = 0;

    for (int a = 1; a <= 30; ++a) {
        for (int b = 1; b <= 30; ++b) {
            for (int d = 1; d <= 30; ++d) {
                for (int e = 1; e <= 30; ++e) {
                    int c = h1 - a - b;
                    int f = h2 - d - e;
                    int g = w1 - a - d;
                    int h = w2 - b - e;
                    int i = w3 - c - f;
                    if (min({c, f, g, h, i}) > 0 && g + h + i == h3) ans++;
                }
            }
        }
    }

    cout << ans << endl;
}
```

### Probiem D：[D - Union of Interval](https://atcoder.jp/contests/abc256/tasks/abc256_d)

-----

#### 题意分析

输入N个区间，将其合并为最简

[56. 合并区间](https://leetcode.cn/problems/merge-intervals/)为原题

#### 参考代码

-----

```cpp
#include "iostream"
#include "climits"
#include "math.h"
#include "unordered_map"
#include "vector"
#include "deque"
#include "algorithm"

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

const double eps = 1e-6;
const int MOD = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll infl = 0x3f3f3f3f3f3f3f3fll;

class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> ans;
        sort(intervals.begin(), intervals.end());

        int p = 0;

        for (int i = 0; i < intervals.size();) {
            vector<int> nums = intervals[i];
            ans.pb(nums);
            i++;
            while (i < intervals.size() && intervals[i][0] <= ans[p][1]) {
                ans[p][1] = max(intervals[i][1], ans[p][1]);
                i++;
            }
            p++;
        }
        return ans;
    }
};

int main() {
    Solution s;
    fastIO();
    int n;
    cin >> n;

    vector<vector<int>> nums(n, vector<int>(2));

    for (int i = 0; i < n; ++i) {
        cin >> nums[i][0] >> nums[i][1];
    }

    vector<vector<int>> list = s.merge(nums);

    for (int i = 0; i < list.size(); ++i) {
        cout << list[i][0] << ' ' << list[i][1] << endl;
    }
}
```

### Problem F：[F - Cumulative Cumulative Cumulative Sum](https://atcoder.jp/contests/abc256/tasks/abc256_f)

#### 题意分析

-----

给定一些数字，对于每一个询问`query`我们需要对其进行**单点更新**和**返回特定的值**

线段树求解即可.

注意返回的特定值求法为

![sXGh0.png](https://s1.328888.xyz/2022/06/21/sXGh0.png)

#### 参考代码

-----

```java
import java.io.*;
import java.util.*;

public class Main {

    static Scanner sc = new Scanner(System.in);
    static PrintWriter out = new PrintWriter(System.out);

    public static void solve() {
        int n = sc.nextInt();
        int q = sc.nextInt();
        long[] a = new long[n + 1];
        for (int i = 0; i < n; i++) {
            a[i + 1] = sc.nextInt();
        }
        long mod = 998244353;
        BIT b0 = new BIT(n + 1);
        BIT b1 = new BIT(n + 1);
        BIT b2 = new BIT(n + 1);

        for (int i = 1; i <= n; i++) {
            b0.set(i, (int) a[i]);
            b1.set(i, (int) (a[i] * i % mod));
            b2.set(i, (int) ((a[i] * i % mod) * i % mod));
        }

        for (int i = 0; i < q; i++) {
            int c = sc.nextInt();
            int x = sc.nextInt();
            if (c == 1) {
                long y = sc.nextInt();
                b0.set(x, (int) y);
                b1.set(x, (int) (y * x % mod));
                b2.set(x, (int) ((y * x % mod) * x % mod));
            } else {
                long d = b0.range(x) * ((long) (x + 2) * (x + 1) % mod) % mod
                        + mod - b1.range(x) * (x * 2 + 3) % mod
                        + b2.range(x);
                d %= mod;
                d = d * ((mod + 1) / 2) % mod;
                out.println(d);
            }
        }

    }

    public static void main(String[] args) {
        solve();
        out.flush();
    }
}

class BIT {
    long mod = 998244353;
    long[] a;
    int n;

    BIT(int n) {
        this.n = n;
        a = new long[n + 1];
    }

    void set(int index0, long num) {
        long cur = get(index0);
        add(index0, (num - cur + mod) % mod);
    }

    void add(int index0, long num) {
        int i = index0 + 1;
        while (0 < i && i <= n) {
            a[i] += num;
            a[i] %= mod;
            i += (i & -i);
        }
    }

    long range(int index0) {
        int res = 0;
        int i = index0 + 1;
        while (i > 0) {
            res += a[i];
            res %= mod;
            i -= (i & -i);
        }
        return res;
    }

    long range(int i0, int j0) {
        return range(j0) - range(i0 - 1);
    }

    long get(int i0) {
        return range(i0, i0);
    }
}
```

