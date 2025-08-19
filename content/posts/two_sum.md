---
title: "Leetcode 10: Two sum"
date: "2025-01-28T19:25:28Z"
Description: ""
Categories: ["Leetcode"]
Author: "Lmbf"
draft: true
toc:
  enable: false
---

**Problem statement**: given an array of integers `nums` and an integer `target`, return the indices `i` and `j` such that `nums[i] + nums[j] == target` and `i != j`. You may assume that every input has exactly one pair of indices `i` and `j` that satisfy the condition. Return the answer with the smaller index first.

Let's do some thinking. The obvious answer would be to have two nested loops and iterate over `nums` while checking when the sum equals `target`, but that's too easy so surely there must be a better way. We can treat our condition as we would any other equation and do:

$$
\text{nums}[i] + \text{nums}[j] = \text{target} \Leftrightarrow \text{nums}[j] = \text{target} - \text{nums}[i]
$$

This means that, if we iterate over `nums` while calculating the above difference and saving it in a hashmap, if we ever find ourselves in the situation where the difference is already stored as a key in the hashmap, it means that we have found the value that satisfies the above equation together with the value used as a key in the hashmap. Thus, just associate these keys with the index they were calculated from and return the values in the appropriate format when a matching key is found.

```python
import unittest

from typing import List
from dataclasses import dataclass


class Sigma():
    def two_sum_equals_target(self, nums: List[int], target: int):
        diffs = {}
        for i in range(len(nums)):
            diff = target - nums[i]
            if diff in diffs:
                return [diffs[diff], i]
            diffs[nums[i]] = i


class TestSigma(unittest.TestCase):
    def test_two_sum_equals_target(self):
        @dataclass
        class TestCase:
            name: str
            nums: list
            target: int
            expected: list

        test_cases = [
            TestCase(name="working example 1", nums=[3,4,5,6], target=7, expected=[0, 1]),
            TestCase(name="working example 2", nums=[4,5,6], target=10, expected=[0, 2])
        ]

        sigma = Sigma()
        for tc in test_cases:
            res = sigma.two_sum_equals_target(tc.nums, tc.target)
            self.assertEqual(tc.expected, res, f"[{tc.name}] - expected {tc.expected}, but got {res}")


if __name__ == "__main__":
    unittest.main()
```