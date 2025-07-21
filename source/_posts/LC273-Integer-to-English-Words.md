---
title: LC.273 Integer to English Words
date: 2022-06-30 16:36:11
tags: 
- Daily algorithm problem solving
- 递归
- 模拟
categories: LeetCode
index_img: https://smms.app/image/ICZ1sY78XzAltpN
---
### 前言
外国数字的读法和国内不同，其分割方式为
> 2,234,911,679 
> 即每三个数为一组，从后向前分别视作
>
> 679
>
> 911个千
>
> 234个万
>
> 2个亿

### 递归模拟

#### 思路分析

-----

有了以上对数字的分析，我们就会发现，把数字转变为英文写法，无非就是分组后的**数字读法 + 数量级(千，万)**

对于数字数量级分组，可以采用**除法 + 整除**的形式；

在数字读法方面，还需要有更细致的讨论：

+ 众所周知，英文中100一下的数字分为三个部分
  + [0 - 10)
  + [10 - 20]
  + (20 - 99]
+ 对于[0 - 10)或[10 - 20]内的数字，可以直接返回读法
+ 对于(20, 99]的数字读法为(这里用45举例)：
  + "Forty Five"
  + 即**40**的读法 + **5**的读法
+ 基于此，我们事先打表一套**英文数字读法表**，按需求往`ans`中添加即可

细节方面，需要注意空格问题；参考代码中，数量级后加空格而前不加空格，计算数字读法时对每一位后都加空格；由此最后还需要弹出位于末尾的无用空格

#### 参考代码

-----

```cpp
class Solution {
private:
    vector<string> singles = {"", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine"};
    vector<string> ten2Twenty = {"Ten", "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen",
                            "Eighteen", "Nineteen"};
    vector<string> tens = {"", "Ten", "Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", "Eighty", "Ninety"};

public:
    string underHundred(int n) {
        string ans;
        int hundred = n / 100;
        if (hundred) {
            ans += singles[hundred] + " Hundred ";
        }
        n %= 100;
        if (n < 20 && n >= 10) {
            ans += ten2Twenty[n % 10] + " ";
            return ans;
        }
        if (n >= 20) {
            ans += tens[n / 10] + " ";
        }
        n %= 10;
        if (n == 0) return ans;
        ans += singles[n % 10] + " ";
        return ans;
    }

    string recur(int n) {
        string ans;

        int billion = n / 1000000000;
        n %= 1000000000;
        int million = n / 1000000;
        n %= 1000000;
        int thousand = n / 1000;
        n %= 1000;

        if (billion) {
            ans += recur(billion) + "Billion ";
        }
        if (million) {
            ans += recur(million) + "Million ";
        }
        if (thousand) {
            ans += recur(thousand) + "Thousand ";
        }

        ans += underHundred(n);
        return ans;
    }

    string numberToWords(int num) {
        if (num == 0) return "Zero";
        string ans = recur(num);
        ans.pop_back();
        return ans;
    }
};
```

#### 文章参考

-----

[【微扰理论】模拟题 英文数字三位一组 (微软面试题原题)](https://leetcode.cn/problems/integer-to-english-words/solution/wei-rao-li-lun-mo-ni-ti-ying-wen-shu-zi-1sc7b/)