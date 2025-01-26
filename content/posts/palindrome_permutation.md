---
title: "Leetcode 3: Palindrome permutation"
date: "2025-01-26T20:17:37Z"
Description: ""
Categories: ["Leetcode"]
Author: "Lmbf"
toc:
  enable: false
---

**Problem statement**: given a string, write a function to check if it is a permutation of a palindrome. A palindrome is a word or a phrase that is the same forwards and backwards. A permutation is a rearrangement of letters. The palindrome does not need to be limited to just dictionary words. You can ignore casing and non-letter characters.

This is a nice exercise because it feels complex but is extremelly straight forward once we think about what we are being asked to do. Basically, we just want to if, if we move the letters around freely, we can get a palindrome. Rather than generate all possible permutations, we can simply notice that a palindrome, like all other words, either has an even or an odd number of characters. Moreover, if it has an even number of characters, then all characters must happen an even number of times. If it has an odd number of characters, then we can only have at most one character with odd number of occurrences.

So essentially this problem boils down to a frequency count once again. Then count how many of the counts are odd and, if this number is greater than 1, then there is no palindrome permutation. Otherwise, there is at least one palindrome permutation.

```python
import unittest
from collections import Counter
from dataclasses import dataclass

class PalindromeChecker():
    def check(self, text: str) -> bool:
        counts = Counter(text.lower())
        number_odd_occurrences = sum(count%2 for count in counts.values())
        return True if number_odd_occurrences <= 1 else False

class TestPalindromeChecker(unittest.TestCase):
    def test_check(self):
        @dataclass
        class Testcase:
            name: str
            input: str
            expected: bool

        test_cases = [
            Testcase(name="all characters are different", input="asdfg", expected=False),
            Testcase(name="empty string", input="", expected=True),
            Testcase(name="ignores casing", input="A1kj1Ka", expected=True),
        ]

        checker = PalindromeChecker()
        for tc in test_cases:
            res = checker.check(tc.input)
            self.assertEqual(tc.expected, res, f"[{tc.name}] - expected {tc.expected}, but got {res}")

if __name__ == "__main__":
    unittest.main()
```