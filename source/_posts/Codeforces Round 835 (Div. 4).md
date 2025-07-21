---
title: Codeforces Round 835 (Div. 4)
date: 2022-11-22 08:33:13
tags:
- CodeForces Contest
- 模拟
- 排序
- 哈希表
- 二分查找
- 贪心
categories: CodeForces
index_img: https://s2.loli.net/2022/11/22/QxqplI7SEANkVRs.jpg
---

> My Submission
>
> > Problem AC: 6
> >
> > Problem Try: 6
> >
> > Wrong Answer: 2

### [Medium Number](https://codeforces.com/contest/1760/problem/A)

#### 题意分析

-----------

返回三个数中的第二大的数字

#### 参考代码

------------

```cpp
int main(){
    fastIO();
    int t;
    cin >>t;
    loop(t){
        int a,b,c;
        cin >> a >> b >> c;
        vi d;
        d.push_back(a);
        d.push_back(b);
        d.push_back(c);
 
        sort(all(d));
        cout << d[1] << endl;
    }
}
```

### [Atilla's Favorite Problem](https://codeforces.com/contest/1760/problem/B)

#### 题意分析

------

返回给定字符串中最大的字符

#### 参考代码

----

```cpp
int main(){
    fastIO();
    int t;
    cin >>t;
    loop(t){
        int n;
        string s;
        cin >> n >> s;
        int ans = 0;
        for(char c:s){
            ans = max(ans, c - 'a' + 1);
        }
        cout << ans << endl;
    }
}
```

### [Advantage](https://codeforces.com/contest/1760/problem/C)

#### 题意分析

----------

返回最大值和第二大的值的差值

#### 参考代码

```cpp
int main(){
    fastIO();
    int t;
    cin >>t;
    loop(t){
        int n;
        cin >> n;
        vi a(n), b(n);
        put(a);
 
        for (int i = 0; i < n; ++i) {
            b[i]=a[i];
        }
        sort(all(b));
        int c = b[n-1], d = b[n-2];
        for (int i = 0; i < n; ++i) {
            if(a[i] == c) cout << c - d << ' ';
            else cout << a[i] - c << ' ';
        }
        space();
    }
}
```

### [Challenging Valleys](https://codeforces.com/contest/1760/problem/D)

#### 题意分析

--------

寻找谷底，可在数组前后加入`INT_MAX`辅助判断

#### 参考代码

------

```cpp
int main() {
    fastIO();
    int t;
    cin >> t;
    loop(t) {
        int n;
        cin >> n;
        vi a(n + 2);
        a[0] = INT_MAX, a[n + 1] = INT_MAX;
 
        for (int i = 1; i <= n; ++i) {
            cin >> a[i];
        }
        int i = 0;
        while (i < n + 2) {
            if (i == n + 1) {
                ++i;
                break;
            }
            if (a[i] >= a[i + 1])++i;
            else {
                break;
            }
        }
 
        while (i < n + 2) {
            if (i == n + 1) {
                ++i;
                break;
            }
            if (a[i] <= a[i + 1])++i;
            else break;
        }
 
        judge(i == n + 2);
    }
}
```

### [Binary Inversions](https://codeforces.com/contest/1760/problem/E)

#### 题意分析

----------

贪心：把最前面的0改成1和把最后面的1改成0计数即可

#### 参考代码

------

```cpp
ll check(vi &a) {
    ll tot = 0;
    unordered_map<int, int> map;
    for(int v:a)++map[v];
 
    for(int v:a){
        if (v == 1) {
            tot += map[0];
        } else --map[0];
    }
    return tot;
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
 
        ll ans = check(a);
 
        for(int &v:a){
            if (v == 0) {
                v = 1;
                ans = max(ans, check(a));
                v = 0;
                break;
            }
        }
 
        for(int i = n - 1; i >= 0; --i) {
            if (a[i] == 1) {
                a[i] = 0;
                ans = max(ans,check(a));
                break;
            }
        }
        cout << ans << endl;
    }
}
```

### [Quests](https://codeforces.com/contest/1760/problem/F)

#### 题意分析

----------

二分查找：二分答案`k`进行判定即可

注意返回`Impossible`和`Infinity`的条件

#### 参考代码

-----

```cpp
bool check(vector<ll> &a, ll tot, ll day) {
    for (int i = 0; i < a.size() && day > 0; ++i, --day) {
        tot -= a[i];
        if (tot <= 0) return 1;
    }
    return tot <= 0;
}
 
bool cc(vector<ll> &a, ll tot, ll day, ll k) {
    ll backet = k + 1, more = day % (k + 1), have = day / backet;
    for(int i = 0; i < a.size() && backet > 0; ++i) {
        tot -= (a[i] * have);
        if (more)--more, tot -= a[i];
        if (tot <= 0) return 1;
        --backet;
    }
    return tot <= 0;
}
 
int main() {
    fastIO();
    int t;
    cin >> t;
    loop(t) {
        ll n, c, d;
        cin >> n >> c >> d;
        vector<ll> a(n);
        put(a);
 
        sort(rall(a));
        if (check(a, c,d)) {
            cout << "Infinity" << endl;
            continue;
        }
        ll left = 0, right = LLONG_MAX, ans = -1;
 
        while (left < right) {
            ll mid = left + (right - left) / 2;
            if (cc(a, c, d, mid))ans = mid, left = mid + 1;
            else right = mid;
        }
 
        if (cc(a,c,d,left))ans = left;
 
        if (ans == -1) {
            cout << "Impossible" << endl;
        } else cout << ans << endl;
    }
}
```

