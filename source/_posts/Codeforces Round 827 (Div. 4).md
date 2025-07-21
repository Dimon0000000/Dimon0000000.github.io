---
title: Codeforces Round 827 (Div. 4)
date: 2022-10-14 08:58:15
tags:
- CodeForces Contest
- 哈希表
- 脑筋急转弯
- 二分查找
- 贪心
- 离散化
categories: CodeForces
index_img: https://s2.loli.net/2022/10/24/rcXb3eyAEuG5Lx1.jpg
---

> My Submission
>
> > Problem AC: 5
> >
> > Problem Try: 6
> >
> > Wrong Answer: 3

### [A. Sum](https://codeforces.com/contest/1742/problem/A)

#### 题意分析

--------

签到题

#### 参考代码

-----

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
    int t;
    cin >> t;
    loop(t){
        int a, b, c;
        cin>>a>>b>>c;

        if(a == b + c || b == a + c || c == a + b)cout<<"YES";
        else cout<<"NO";
        cout<<endl;
    }
}
```

### [B. Increasing](https://codeforces.com/contest/1742/problem/B)

#### 题意分析

-------

检测数组是否含有重复元素

#### 参考代码

-----

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
    int t;
    cin >> t;
    loop(t){
        int n;
        cin >> n;
        vi a(n);
        put(a);
        bool f=1;
        unordered_map<int, int> map;
        for(int v:a)map[v]++;
        for(auto &it:map){
            if(it.second != 1){
                f=0;
                break;
            }
        }
        judge(f);
    }
}
```

### [C. Stripes](https://codeforces.com/contest/1742/problem/C)

#### 题意分析

-----

给定一个8 * 8的矩阵

红色的线会横着画，蓝色的线会竖着画

返回最后一笔画使用的颜色

+ 若最后一笔是红色，则一定存在一行全是红色的行，否则答案为蓝色

#### 参考代码

-----

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
    int t;
    cin >> t;
    vector<string> a(8);
    loop(t){
        bool f=1;
        put(a);

        int c1=0,c2=0;

        for (int i = 0; i < 8; ++i) {
            int cc=0;
            for(char c:a[i]){
                if(c=='R')++cc;
            }
            if(cc==8)c1=8;
        }

        for (int i = 0; i < 8; ++i) {
            int cc=0;
            for(char c:a[i]){
                if(c=='B')++cc;
            }
            if(cc==8)c2=8;
        }

        if(c1 == 8) cout<<"R";
        else cout<<"B";
        cout<<endl;
    }
}
```

### [D. Coprime](https://codeforces.com/contest/1742/problem/D)

#### 题意分析

------

给定一个数组，返回其中任意两个互质数的下标最大和

离散化 + 暴力查找 + 贪心

即使数组大小为`2e5`，但由于数字最多只会有1000个不同，因此用`map`记录下每个数字的最大下标，然后暴力所有数字寻找互质关系，然后找到对应的最大下标和即可

#### 参考代码

-----

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
#define space() cout<<endl

typedef long long ll;
typedef pair<int, int> pii;

const int N = 2e5 + 5;
const double eps = 1e-6;
constexpr int mod = 1e9 + 7;
constexpr int i32 = 0x3f3f3f3f;

using vi = std::vector<int>;
using vvi = std::vector<vi>;

template<typename typC>
bool isPrime(typC num) {
    if (num == 1 || num == 4)return 0;
    if (num == 2 || num == 3)return 1;
    if (num % 6 != 1 && num % 6 != 5)return 0;
    typC tmp = sqrt(num);
    for (int i = 5; i <= tmp; i += 6)
        if (num % i == 0 || num % (i + 2) == 0)return 0;
    return 1;
}

bool isrp(int a, int b) {
    if (a == 1 || b == 1)     // 两个正整数中，只有其中一个数值为1，两个正整数为互质数
        return true;
    while (1) {          // 求出两个正整数的最大公约数
        int t = a % b;
        if (t == 0) {
            break;
        } else {
            a = b;
            b = t;
        }
    }
    if (b > 1) return false;// 如果最大公约数大于1，表示两个正整数不互质
    else return true;    // 如果最大公约数等于1,表示两个正整数互质
}

int main() {
    fastIO();
    int t;
    cin >> t;
    loop(t) {
        int n;
        cin >> n;
        vi a(n);
        put(a);
        int aa=-1,bb=-1;
        int ans=-1;
        int idx=1;

        unordered_map<int, int> map;

        for(int v:a){
            if(v==1)ans=idx+idx;
            map[v]=idx++;
        }

        vi b((int) map.size());
        int ii=0;
        for(auto &it:map){
            b[ii++]=it.first;
        }

        for (int i = 0; i < b.size(); ++i) {
            aa=-1,bb=-1;
            for (int j = i; j < b.size(); ++j) {
                if(isrp(b[i],b[j])){
                    aa=max(aa,map[b[i]]),bb=max(bb,map[b[j]]);
                }
            }
            ans=max(ans,aa+bb);
        }
        cout<<ans<<endl;
    }
}
```

### [E. Scuza](https://codeforces.com/contest/1742/problem/E)

#### 题意分析

-----

给定一组台阶，在每一个查询中，给定人物的腿长，返回这个腿长的人最高可以爬多高

二分查找：

+ 遍历查询，二分可以爬到的楼层
+ `max`数组和`presum`辅助查询答案

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
#define space() cout<<endl

typedef long long ll;
typedef pair<int, int> pii;

const int N = 2e5 + 5;
const double eps = 1e-6;
constexpr int mod = 1e9 + 7;
constexpr int i32 = 0x3f3f3f3f;

using vi = std::vector<int>;
using vvi = std::vector<vi>;

bool check(vi &a, int mid, int k) {
    for (int i = 0; i <= mid; ++i) {
        if (a[i] > k)return 0;
    }
    return 1;
}

int main() {
    fastIO();
    int t;
    cin >> t;
    loop(t) {
        int n, m;
        cin >> n >> m;
        vi a(n), q(m), maxx(n);
        vector<ll> presum(n + 1);
        put(a);
        put(q);
        int maxxx=a[0];
        for (int i = 0; i < n; ++i) {
            maxx[i]=max(a[i],maxxx);
            maxxx=max(maxxx,a[i]);
        }

        for (int i = 1; i <= n; ++i)presum[i] = presum[i - 1] + a[i - 1];

        for (int i = 0; i < m; ++i) {
            int k = q[i];
            int ans = -1;

            int left = 0, right = n - 1;
            while (left < right) {
                int mid = left + (right - left) / 2;
                if (k >= maxx[mid])ans = mid, left = mid + 1;
                else right = mid;
            }
            if (k >= maxx[left])ans = left;

            if (ans == -1)cout << 0 << ' ';
            else {
                ll tot = presum[ans + 1] - presum[0];
                cout << tot << ' ';
            }
        }
        cout<<"\n";
    }
}
```

