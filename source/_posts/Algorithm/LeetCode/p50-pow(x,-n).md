---
title: '50. Pow(x, n)'
categories:
  - Algorithm
  - LeetCode
tags:
  - algorithm
---

~~~cpp
class Solution {
public:
    double myPow(double x, int n) {
        if(n == 0) return 1;
        double t = myPow(x, n / 2);
        if(n % 2)
            return n < 0 ? 1 / x * t * t : x * t * t;
        else
            return t * t;
    }
};
~~~