---
title: 136. Single Number
categories:
  - Algorithm
  - LeetCode
tags:
  - algorithm
---

~~~cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int res = 0;
        for(int num : nums) res ^= num;
        return res;
    }
};
~~~