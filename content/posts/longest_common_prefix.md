---
title: "Leetcode 11: Longest common prefix"
date: "2025-01-28T20:32:09Z"
Description: ""
Categories: ["Leetcode"]
Author: "Lmbf"
toc:
  enable: false
---

**Problem statement**: Write a function to find the longest common prefix string amongst an array of strings. If there is no common prefix, return an empty string `""`.

First, let's set the obvious limitation of our result: it is either the empty string or our result is at most equal to the smallest string in the given array. With this in mind, we get a first obvious solution, check all substrings of all of the given strings (in ascending order of length) up until the point where we find a mismatch between them. The previously observed substring will then correspond to the longest common prefix.

The second solution is also obvious also doing the first (as it's also faster). We can simply substitute the linear search part of the code with a binary search and we'll get much better results. Plus, this was a good excuse to use a closure.


```python
import unittest
from typing import List
from dataclasses import dataclass


class LCPFinder():
    def with_linear_search(self, strs: List[str]):
        prefix = ""
        max_prefix_size = min([len(s) for s in strs])
        
        for i in range(1, max_prefix_size+1):    
            if all(s[:i] == strs[0][:i] for s in strs):
                prefix = strs[0][:i]

        return prefix

    def with_binary_search(self, strs: List[str]):
        max_prefix_size = min([len(s) for s in strs])
        
        low, high = 1, max_prefix_size
        while low <= high:
            middle = (low + high) // 2

            def compare_strings():
                s0 = strs[0][:middle]
                for i in range(1, len(strs)):
                    if not strs[i][:middle] == s0:
                        return False
                return True
            
            if compare_strings():
                low = middle + 1
            else:
                high = middle - 1

        return strs[0][:(low + high) // 2]


class TestLCPFinder(unittest.TestCase):
    def test_find(self):
        @dataclass
        class TestCase:
            name: str
            strs: list
            expected: str

        test_cases = [
            TestCase(name="single string with one character in list", strs=["a"], expected="a"),
            TestCase(name="lcp exists", strs=["flower","flow","flight"], expected="fl"),
            TestCase(name="lcp does not exist", strs=["dog","racecar","car"], expected=""),
        ]

        finder = LCPFinder()
        for tc in test_cases:
            res = finder.with_linear_search(tc.strs)
            self.assertEqual(tc.expected, res, f"[{tc.name}] - expected {tc.expected}, but got {res}")

            res = finder.with_binary_search(tc.strs)
            self.assertEqual(tc.expected, res, f"[{tc.name}] - expected {tc.expected}, but got {res}")


if __name__ == "__main__":
    unittest.main()
```