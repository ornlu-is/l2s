---
title: "Leetcode 4: One edit away"
date: "2025-01-27T10:19:12Z"
Description: ""
Categories: ["Leetcode"]
Author: "Lmbf"
toc:
  enable: false
---

**Problem statement**: there are three types of edits that can be performed on strings: insert a character, remove a character, or replace a character. Given two strings, write a function to check if they are one edit (or zero edits) away.

This one is very easy to solve. We simply get the frequency counts of the characters in both strings and subtract them, and then count how many keys we get whose value is not zero, since that corresponds to the number of edits.

```python
import unittest
from collections import Counter
from dataclasses import dataclass

class EditChecker():
    def is_one_edit_away(self, s, t):
        c = Counter(s)
        c.subtract(t)
        num_edits = sum(1 for val in c.values() if val != 0)

        return True if num_edits <= 1 else False

class TestEditChecker(unittest.TestCase):
    def test_one_edit_away(self):
        @dataclass
        class TestCase:
            name: str
            str1: str
            str2: str
            expected: bool

        test_cases = [
            TestCase(name="one operation away", str1="asdf", str2="asdfg", expected=True),
            TestCase(name="zero operations away", str1="asdf", str2="asdf", expected=True),
            TestCase(name="works for empty strings", str1="", str2="", expected=True),
            TestCase(name="works for random characters", str1="123 -kj", str2="12 -kj", expected=True),
            TestCase(name="one edit and one add away", str1="asdf", str2="sdt", expected=False),
            TestCase(name="two adds away", str1="asdf", str2="asdfgh", expected=False),
        ]

        checker = EditChecker()
        for tc in test_cases:
            res = checker.is_one_edit_away(tc.str1, tc.str2)
            self.assertEqual(res, tc.expected, msg=f"[{tc.name}] - expected {tc.expected}, but got {res}")

if __name__ == "__main__":
    unittest.main()
```