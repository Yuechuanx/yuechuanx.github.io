---
title: 326. Power of Three
categories:
  - Algorithm
  - LeetCode
tags:
  - algorithm
---

~~~cpp
class Solution {
public:
    bool isPowerOfThree(int n) {
        while(n && (n % 3 == 0)) {
          n /= 3;
        }
        return n == 1;
    }
};
~~~