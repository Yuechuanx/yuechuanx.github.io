---
title: 168. Excel Sheet Column Title
categories:
  - Algorithm
  - LeetCode
tags:
  - algorithm
---

~~~cpp
class Solution {
public:
    string convertToTitle(int n) {
        string res = "";
        while(n) {
            res += --n % 26 + 'A';
            n /= 26;
        }
        return string(res.rbegin(), res.rend());
    }
};
~~~