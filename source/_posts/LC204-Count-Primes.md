---
title: LC.204 Count-Primes
date: 2022-07-01 17:01:01
tags: 
- Daily algorithm problem solving
- 埃氏晒
- 线性筛
- 奇数晒
categories: LeetCode
index_img: https://smms.app/image/sj6faQexWvnGrZJ
---

### 前言

> 质数是指在大于1的[自然数](https://baike.baidu.com/item/自然数/385394)中，除了1和它本身以外不再有其他[因数](https://baike.baidu.com/item/因数/9539111)的自然数。

本题给定一个整数`n`，返回小于`n`的质数的个数

### 基本思路

#### 枚举因数

-----

对于一个整数`x`，可以从2开始枚举每一个可能的因数，一旦可以被整除，则不为质数

此方法对于小范围的`n`可行，但对于大范围的整数，对每一个数字的因数检查的时间开销都为*O(n)*

如`n` = 499979时会产生**TLE**

##### 参考代码

-----

```cpp
class Solution {
public:
    bool check(int n) {
        for (int i = 2; i < n; i++) 
            if (n % i == 0) return false;
        return true;
    }

    int countPrimes(int n) {
        int cnt = 0;
        for (int i = 2; i < n; i++)
            if (check(i)) cnt++;
        return cnt;
    }
};
```

#### 埃氏晒

-----

 埃拉托斯特尼筛法，简称**埃氏晒**，是一种用来求自然数n以内的全部素数的算法。

他的基本原理是：如果我们要获得小于n的所有素数，那就把不大于$$\sqrt{n}$$ 的所有素数的倍数剔除

 一个合数，必然可以表示成，一个自然数 `i` 和一个素数`j`的乘积。因此我们找到一个素数后，把他小于n的倍数全部标记为合数即可

##### 参考代码

```cpp
class Solution {
public:
    int countPrimes(int n) {
        vector<int> vessel(n, 1);

        for (int i = 2; i * i < n; i++) {
            if (vessel[i]) {
                for (int j = i * i; j < n; j += i) 
                    vessel[j] = 0;
            }
        }

        int cnt = 0;
        for (int i = 2; i < n; i++) 
            if (vessel[i]) cnt++;
        return cnt;
    }
};
```

#### 线性筛

-----

在上分的埃氏晒中由于我们遍历小于$$\sqrt{n}$$ 的数，每一次使用乘法筛选出新的合数，因此会有重复判断的情况

线性筛利用了数学原理：

> 两个或两个以上的质数的乘积只可以组成唯一的合数

由此，有以下遍历的思路

+ 初始，所有数均为质数
+ 若遍历到的数为质数，则加入质数容器中
+ 对于任意一个数，都要与质数容器中的所有数相乘，来得到新的质数
  + 对于一个质数：质数 * 质数 = 合数
  + 对于一个合数：合数可分解为互不相同的n(n ≥ 2)个质数相乘，因此该合数再次乘以质数为一个新的合数
+ 只要作为因数的每一个质数都不相同，那么合数一定不同，不会重复；因此，当某一合数可以整除该质数时直接结束筛选即可

##### 参考代码

-----

```cpp
class Solution {
public:
    int countPrimes(int n) {
        vector<int> vessel(n, 1), prime;

        for (int i = 2; i < n; i++) {
            if (vessel[i]) prime.push_back(i);
            for (int j = 0; j < prime.size(); j++) {
                int num = i * prime[j];
                if (num >= n) break;
                vessel[num] = 0;
                if (i % num == 0) break;
            }
        }
        return prime.size();
    }
};
```

#### 奇数筛

-----

应用数学原理：

> 除了2，质数一定是奇数，偶数一定不是质数
>
> 奇数 * 奇数 = 偶数 | 奇数 * 偶数 = 偶数 | 偶数 * 偶数 = 奇数

运用此思路，参考埃氏晒的解法进行优化

##### 参考代码

-----

```cpp
class Solution {
public:
    int countPrimes(int n) {
        if (n <= 2) return 0;
        vector<int> vessel(n, 1);

        for (int i = 3; i * i < n; i += 2) {
            if (vessel[i]) {
                for (int j = i * i; j < n; j += i) 
                    vessel[j] = 0;
            }
        }

        int cnt = 1;
        for (int i = 2; i < n; i++) 
            if (vessel[i] && i% 2 == 1) cnt++;
        return cnt;
    }
};
```
