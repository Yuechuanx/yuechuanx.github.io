---
title: 35. Search Insert Position
categories:
  - Algorithm
  - LeetCode
tags:
  - algorithm
---

~~~cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        for(int i = 0; i < nums.size(); ++i) {
            if(nums[i] >= target) return i;
        }
        return nums.size();
    }
};
~~~