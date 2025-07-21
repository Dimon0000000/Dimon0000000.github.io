---
title: LC.51 N-Queens
date: 2022-06-18 13:56:14
tags:
- Daily algorithm problem solving
- 回溯算法
categories: LeetCode
index_img: https://img1.sycdn.imooc.com/5cf76da60001fef010220522.jpg
---

## 前言

本题是`Eight Queens`的衍生题

> **八皇后问题**（[英文](https://baike.baidu.com/item/英文/3079091)：**Eight queens**），是由[国际象棋](https://baike.baidu.com/item/国际象棋/80888)棋手马克斯·贝瑟尔于1848年提出的问题
>
> 问题表述为：在8×8格的[国际象棋](https://baike.baidu.com/item/国际象棋/80888)上摆放8个[皇后](https://baike.baidu.com/item/皇后/15860305)，使其不能互相攻击，即任意两个皇后都不能处于同一行、同一列或同一斜线上，问有多少种摆法。

本题将`Eight Queens`扩展为`N Queens`

-----

### 朴素回溯法

我们会优先想到朴素的回溯算法思路，对于`N`个皇后，我们可以在`N * N`的棋盘上摆出所有皇后可能出现的情况，再对每一种情况下皇后之间的关系进行判断。

##### 参考代码

------

```cpp
#include "bits/stdc++.h"

using namespace std;

class Solution {

    vector<vector<string>> ans;

    int line;

    int directions[8][2] = {{0,  1},
                            {-1, 1},
                            {-1, 0},
                            {-1, -1},
                            {0,  -1},
                            {1,  -1},
                            {1,  0},
                            {1,  1}};

private:
    /**
     *
     * @param queen 棋盘位置
     * @param i 皇后所在行
     * @param j 皇后所在列
     * @return 本方法用于检测在某一位置的皇后是否合法
     */
    bool canAttack(vector<string> queen, int i, int j) {
        for (int k = 0; k < 8; ++k) {
            int ni = i + directions[k][0], nj = j + directions[k][1];
            while (ni >= 0 && ni < line && nj >= 0 && nj < line) {
                if (queen[ni][nj] == 'Q') return true;
                else {
                    ni += directions[k][0];
                    nj += directions[k][1];
                }
            }
        }
        return false;
    }

    /**
     *
     * @param solve 棋盘
     * @return 本方法用于检测整个棋盘上的皇后是否合法
     */

    bool check(vector<string> solve) {
        for (int i = 0; i < line; ++i) {
            for (int j = 0; j < line; ++j) {
                if (solve[i][j] == 'Q') {
                    if (canAttack(solve, i, j)) return false;
                }
            }
        }
        return true;
    }

public:
    /**
     * 本方法用于摆盘
     * @param board 棋盘
     * @param lin 该摆第几行了
     */
    void backTrack(vector<string>& board, int lin) {
        if (lin == line) {
            if (check(board)) ans.push_back(board);
            return;
        }
        for (int i = 0; i < line; ++i) {
            board[lin][i] = 'Q';
            backTrack(board, lin + 1);
            board[lin][i] = '.';
        }
    }
    /**
     * 主方法
     * @param n 个皇后
     * @return 返回合法的方案
     */
    vector<vector<string>> solveNQueens(int n) {
        line = n;
        vector<string> board;
        for (int i = 0; i < n; ++i) {
            string l;
            l.append(n, '.');
            board.push_back(l);
        }
        backTrack(board, 0);
        return ans;
    }
};
```

朴素回溯法可以得到当`N < 8`时的答案

当`N >= 8`时，仅仅拿`N == 8`来说，此方法的计算量会达到8^8^ = 16,777,216 ≈ 10 ^8^

此数据量过大，会导致`Time Limit Exceeded`

##### 运行结果如下

<a href="https://sm.ms/image/KZLavRCghIQ6Bde" target="_blank"><img src="https://s2.loli.net/2022/06/18/KZLavRCghIQ6Bde.png" ></a>

***

### 基于朴素回溯法的优化

在上面的方案中，我们对任意一种摆法都进行了判断，甚至于最明显不可行的方案都进行了判断

这里使用N == 4举例

> Q = Queen, B = Board
>
> \[Q,B,B,B]
>
> \[Q,B,B,B]
>
> \[Q,B,B,B]
>
> \[Q,B,B,B]

类似于这样明显错误的我们都进行了判断。

基于此的优化方法，便是直接寻找可以放置皇后的地方

我们知道：

> 皇后可以攻击同一行，同一列，同一主对角线，同一副对角线上的任意一个棋子

我们寻找皇后位置时，可以基于这个思路大大减少计算量(这里定义为“竖列横行”)

1.  由于我们是一行一行放置皇后，对于列，我们只需要一个`used`数组来判断同一列上是否有皇后

2.  对于主对角线上的元素，任意两个在同一主对角线上的元素相减之差相等

    -   举个例子(1, 1),   (3, 3)位于同一主对角线上，二者相减之差均为0

3.  对于副对角线上的元素，任意两个在同一副对角线上的元素相加之和相等

    -   举个例子(1, 5),   (3, 3)位于同一副对角线上，二者相加之和均为6

4.  在回溯方法中，我们可以直接传入一个空棋盘，在上方放置皇后即可，这里利用了C++中字符串可变的性质

***

##### 运行结果如下

<a href="https://sm.ms/image/mkJRQFM9BUSEsCi" target="_blank"><img src="https://s2.loli.net/2022/06/18/mkJRQFM9BUSEsCi.png" ></a>

##### 参考代码

-----

```cpp
#include "bits/stdc++.h"

using namespace std;

class Solution {

    vector<vector<string>> ans;
    set<int> diag1, diag2;

    vector<bool> used;

    int line;

public:

    /**
     * 本方法用于摆盘
     * @param board 棋盘
     * @param lin 该摆第几行了
     */
    void backTrack(vector<string> &board, int lin) {
        if (lin == line) {
            ans.push_back(board);
            return;
        }
        for (int i = 0; i < line; ++i) {
            if (used[i]) continue;
            used[i] = true;

            int d1 = lin - i, d2 = lin + i;
            if (diag1.count(d1) == 1 || diag2.count(d2) == 1) {
                used[i] = false;
                continue;
            }
            diag1.insert(d1);
            diag2.insert(d2);

            board[lin][i] = 'Q';
            backTrack(board, lin + 1);
            board[lin][i] = '.';

            diag2.erase(d2);
            diag1.erase(d1);

            used[i] = false;
        }
    }

    /**
     * 主方法
     * @param n 个皇后
     * @return 返回合法的方案
     */
    vector<vector<string>> solveNQueens(int n) {
        line = n;

        for (int i = 0; i < n; ++i) {
            used.push_back(false);
        }

        vector<string> board;
        for (int i = 0; i < n; ++i) {
            string l;
            l.append(n, '.');
            board.push_back(l);
        }
        backTrack(board, 0);
        return ans;
    }
};
```

+ 时间复杂度: *O*(*N*!)， 其中*N*为皇后数量

+ 空间复杂度: *O*(*N*)，其中 *N* 是皇后数量。

-----

## 相关趣题

最后分享一道写这篇题解时联想到的趣题

来自于**CodeForces Round #799 (Div 4)** [Problem - C](https://codeforces.com/contest/1692/problem/C)

<a href="https://sm.ms/image/nxTU38HkOl7qsAm" target="_blank"><img src="https://s2.loli.net/2022/06/18/nxTU38HkOl7qsAm.png" >

------

<a href="https://sm.ms/image/rAofaC5MK9wPh4O" target="_blank"><img src="https://s2.loli.net/2022/06/18/rAofaC5MK9wPh4O.png" ></a></a>

### 题意解读

-----

在棋盘上，我们用**#**标注了**教皇**可攻击的格子，请根据此，输出教皇所在的位置

#### 思路分析

-----

由于棋盘大小固定为*8*，我们只需暴力搜索
$$
1 \leq i, j \leq 7
$$
当`grid[i][j]`的左上，右上，左下，右下均为**#**时返回该位置**[i, j]**

##### 参考代码

-----

```cpp
#include <bits/stdc++.h>
 
using namespace std;
 
int main() {
    int t, n;
    cin >> t;
    for (int i = 0; i < t; ++i) {
        vector<string> grid(8);
        for (int j = 0; j < 8; ++j) {
            cin >> grid[j];
        }
 
        for (int j = 1; j < 8; ++j) {
            for (int k = 1; k < 8; ++k) {
                if (grid[j][k] == '#' && grid[j - 1][k - 1] == '#' && grid[j - 1][k + 1] == '#' && grid[j + 1][k - 1] == '#' && grid[j + 1][k + 1] == '#') {
                    cout << j + 1 << ' ' << k + 1 << endl;
                }
            }
        }
    }
} 
```

