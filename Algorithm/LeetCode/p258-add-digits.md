---
title: 258. Add Digits
categories:
  - Algorithm
  - LeetCode
tags:
  - algorithm
---

~~~cpp
class Solution {
public:
    int addDigits(int num) {
        int res = 0;
        while(num > 0) {
            res += num % 10;
            num /= 10;
        }
        return res >= 10 ? addDigits(res) : res;
    }
};
~~~