---
title: 383. Ransom Note
categories:
  - Algorithm
  - LeetCode
tags:
  - algorithm
---

~~~cpp
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        unordered_map<char, int> m;
        for(char c : magazine) ++m[c];
        for(char c : ransomNote) {
            if(--m[c] < 0) return false;
        }
        return true;
    }
};
~~~