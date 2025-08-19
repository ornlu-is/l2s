---
title: "Leetcode 6: String rotation"
date: "2025-01-27T19:48:58Z"
Description: ""
Categories: ["Leetcode"]
Author: "Lmbf"
draft: true
toc:
  enable: false
---

**Problem statement**: Assume you have a method which checks if one word is a substring of another. Given two strings, write code to check if the latter is a rotation of the former using only one call to the aforementioned method. 

This one is super straightforward once you know the trick. If you take the original string and concatenate it with itself, then all of the string's rotations will be included in the concatenation. Thus, it will then suffice to simply use the method to check if the word is a substring of another and, if it is, then it must be a rotation. Note that we must check the length because rotation preserves the length.

```python
import unittest
from dataclasses import dataclass

class RotationChecker():
    def check(self, s: str, t: str) -> bool:
        if len(s) == len(t) != 0:
            return t in s+s
        return False
        

class TestRotationChecker(unittest.TestCase):
    def test_check(self):
        @dataclass
        class TestCase:
            name: str
            given1: str
            given2: str
            expected: bool

        test_cases = [
            TestCase(name="correctly identifies rotation", given1="waterbottle", given2="erbottlewat", expected=True),
            TestCase(name="correctly identifies not being a rotation", given1="waterbottle", given2="rebottlewat", expected=False),
        ]

        checker = RotationChecker()
        for tc in test_cases:
            res = checker.check(tc.given1, tc.given2)
            self.assertEqual(res, tc.expected, f"[{tc.name}] - expected {tc.expected}, but got {res}")


if __name__ == "__main__":
    unittest.main()
```